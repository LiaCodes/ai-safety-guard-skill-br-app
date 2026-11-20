---
name: ai-safety-guard-skill-br-app
description: Triagem inicial de segurança, privacidade (LGPD) e segurança de IA — além de diretrizes defensivas de implementação — para aplicações no Brasil, especialmente sistemas que tratam dados de pacientes e saúde (prontuário eletrônico), identificadores CPF/CRM, RLS no Supabase/Postgres, Edge Functions, buckets de storage, serviços em nuvem e fluxos de IA/RAG.
---

# Revisão de Segurança e IA para Aplicações Brasileiras

## Propósito e escopo

Realizar triagem técnica inicial baseada em evidências — ou aplicar diretrizes defensivas durante a implementação — para riscos de segurança de aplicações, privacidade e integração de IA em sistemas brasileiros, com atenção especial a dados de pacientes e informações médicas.

Revisar controle de acesso a bancos de dados e storage (ex.: RLS no PostgreSQL/Supabase, RPCs, buckets), tratamento de dados pessoais e dados sensíveis de saúde, autenticação e autorização, APIs e edge functions, invariantes do domínio brasileiro (CPF/CRM, fusos horários, retenção vs. eliminação), responsabilidade compartilhada em nuvem, integrações de terceiros e segurança de IA no nível da aplicação.

**Fora do escopo:** diagnóstico clínico, recomendações de tratamento, tomada de decisão médica, acurácia clínica, parecer formal de conformidade com a LGPD, aconselhamento jurídico, testes de intrusão (*pentest*) ou auditoria abrangente de segurança. Caso a IA possa influenciar decisões clínicas, sinalize a necessidade de avaliação especializada de segurança clínica e regulatória.

## Modos de operação

1. **Modo de Revisão e Triagem (padrão ao inspecionar código existente, PRs, schemas ou arquiteturas):** Siga o fluxo de revisão em 7 etapas abaixo e estruture a saída conforme o modelo **Achados e relatório**.
2. **Modo de Implementação Defensiva (quando solicitado a gerar ou alterar código, migrations ou APIs):** Aplique proativamente todos os controles das seções 1 a 6 antes de escrever o código e registre ao final quaisquer premissas de segurança ou verificações operacionais pendentes.

## Fluxo de revisão

### 1. Mapear o sistema e as fronteiras de confiança

Antes de apontar problemas, estabeleça o que se sabe sobre:

- Usuários, perfis/papéis, *tenants* (clínicas, organizações, profissionais de saúde, equipe administrativa, pacientes), autenticação, autorização e identidades privilegiadas (`service_role`, chaves administrativas, webhooks).
- Fluxos de dados: onde dados pessoais e de saúde entram, são armazenados, consultados, registrados em logs, exportados, enviados a provedores de IA, arquivados e excluídos.
- Bancos de dados, APIs, buckets de storage, edge functions, handlers de backend, serviços externos, modelos de IA, fontes de recuperação (RAG) e ferramentas (*tools*).
- Entradas confiáveis versus não confiáveis, ações que alteram estado e operações que afetam outros usuários ou *tenants*.
- Serviços em nuvem e as responsabilidades de segurança do provedor, do responsável pela aplicação e de terceiros.

Não infira que um controle está ausente apenas porque não aparece em um trecho isolado de código. Solicite contexto adicional relevante quando necessário.

### 2. Revisar segurança da aplicação e do banco de dados

- **Autorização e isolamento:** verifique titularidade e fronteiras de *tenant* antes de leituras, escritas e exclusões; inspecione políticas RLS (`USING` e `WITH CHECK`), chaves estrangeiras compostas, checagens de acesso em nível de objeto, funções `SECURITY DEFINER`, views, procedures e permissões de privilégio mínimo (`anon`, `authenticated`, `service_role`). Considere acesso indevido entre usuários, clínicas ou organizações (IDOR).
- **Autenticação e acesso privilegiado:** distinga verificação de identidade de verificação de permissão; confirme que as checagens ocorrem antes do acesso aos dados, mudanças de estado e chamadas externas. Verifique se credenciais privilegiadas (`SUPABASE_SERVICE_ROLE_KEY`, segredos de assinatura) permanecem exclusivamente no servidor — nunca em bundles de frontend (`VITE_*`, `NEXT_PUBLIC_*`) — e avalie como operações com `service_role` impõem fronteiras de *tenant* ao contornar RLS.
- **Consultas e storage:** procure construção insegura de queries / SQL injection (endurecimento de `search_path` em funções SQL); revise permissões de arquivos enviados, visibilidade de buckets (`public` vs. privado), caminhos de upload, tempo de expiração (TTL) de URLs assinadas e se os documentos herdam as fronteiras de acesso dos registros associados.
- **Resistência a abuso:** avalie limites de taxa (*rate limits*), CAPTCHA/Turnstile em endpoints públicos, bloqueio contra força bruta em PINs ou tokens curtos, payloads excessivos, operações custosas não autenticadas e consistência de proteção entre endpoints.
- **Exposição e sinais do domínio brasileiro:** verifique se APIs, queries, URLs, logs, caches, analytics, ferramentas de depuração, exportações ou payloads de IA expõem desnecessariamente e-mails, telefones, identificadores brasileiros (`CPF`, `CNPJ`, `CRM`, `CNS`), registros de saúde, texto livre ou credenciais. Verifique o tratamento de fuso horário (ex.: `America/Sao_Paulo` vs. demais fusos do Brasil) sempre que janelas de expiração, agendamentos ou carimbos temporais dependerem da hora local.

### 3. Dar escrutínio adicional a dados de saúde

Sob a LGPD (Art. 5º, II e Art. 11), dados referentes à saúde são dados pessoais sensíveis. Verifique:

- Classificação de dados e acesso com privilégio mínimo separando registros clínicos de dados administrativos/de agendamento entre pacientes, profissionais de saúde, recepção/administração, clínicas e organizações.
- Isolamento do prontuário e autorização em todos os caminhos de acesso, incluindo imutabilidade pós-finalização de evoluções clínicas e prescrições/documentos assinados.
- Acesso de emergência (*break-glass*): escopo restrito, justificado, registrado em log e auditável.
- Tensão entre retenção e eliminação: a legislação brasileira de saúde (ex.: Lei nº 13.787/2018 e normas do CFM) exige guarda mínima de 20 anos para prontuários médicos, prevalecendo sobre pedidos genéricos de eliminação previstos no Art. 16 da LGPD. Verifique se registros clínicos estão protegidos contra exclusão física acidental ou não autorizada (utilizando arquivamento/bloqueio de acesso) enquanto dados não sujeitos à guarda obrigatória possuem ciclo de vida adequado.
- Criptografia em trânsito e em repouso, responsabilidades de gestão de chaves, backups, restauração e integridade de assinaturas digitais (ex.: fluxos ICP-Brasil, verificação de hash).
- Cópias ou divulgações desnecessárias em logs, analytics, ambientes de desenvolvimento, exportações, anexos e terceiros.
- Se pseudonimização ou anonimização é apropriada; não presuma que dados pseudonimizados sejam automaticamente anônimos.
- Dados de pacientes em prompts, contexto de modelos, recuperação (RAG), saídas e serviços externos de IA; avalie minimização, restrições de acesso, retenção e salvaguardas contratuais.
- Se conteúdo gerado pode revelar informações de um paciente a outro usuário.

Um plano corporativo (*enterprise*), declaração de privacidade do provedor ou certificação de segurança não comprova, por si só, que o tratamento realizado pela aplicação seja seguro ou adequado.

### 4. Revisar segurança de IA no nível da aplicação

Avalie integração segura e proteção de dados, não correção clínica.

- Trate entradas do usuário, textos livres clínicos, documentos recuperados, conteúdo web e dados externos como não confiáveis; considere *prompt injection* direto e indireto e conflitos de instrução.
- Verifique contexto montado, filtros de RAG e histórico de conversa quanto a vazamento entre *tenants* ou usuários, exposição de dados de pacientes, informações internas, segredos e dados pessoais desnecessários.
- Valide e restrinja saídas do modelo antes de utilizá-las em SQL, APIs, HTML, dados estruturados ou operações que alteram estado.
- Garanta que ferramentas (*tools*) e ações imponham autorização de forma independente das decisões do modelo; exija aprovação humana para ações relevantes ou irreversíveis quando apropriado.
- Considere renderização insegura, injeção na saída (*output injection*) e uso não intencional de ferramentas.
- Se a funcionalidade de IA aparentar influenciar diagnóstico, tratamento ou decisões clínicas, interrompa a avaliação de segurança clínica e recomende análise por especialistas.

### 5. Aplicar o contexto brasileiro de privacidade

Identifique sinais técnicos relevantes para a LGPD; não emita conclusões jurídicas.

- Distinga dados pessoais comuns de dados pessoais sensíveis; trate dados de saúde como sensíveis.
- Considere limitação de finalidade, minimização de dados, retenção excessiva ou indefinida (equilibrada com obrigações legais de guarda na área da saúde), compartilhamento com terceiros e transferência internacional de dados (ex.: APIs de IA ou nuvem hospedadas no exterior).
- Verifique suporte técnico aos direitos dos titulares (acesso, correção, anonimização/bloqueio/eliminação quando juridicamente cabível), restrições de acesso e trilhas de auditoria/responsabilização (atribuição de `actor_id` mesmo em operações privilegiadas de backend).
- Identifique exposição em logs, monitoramento, analytics e ambientes de desenvolvimento.

Sinalize dúvidas sobre base legal, papéis de controlador/operador ou conformidade formal para revisão jurídica/de privacidade qualificada.

### 6. Avaliar responsabilidade compartilhada em nuvem e terceiros

Para cada serviço gerenciado ou em nuvem, identifique controles geridos pelo provedor versus responsabilidades do cliente/aplicação. Consulte a documentação específica do serviço em vez de presumir.

- **Provedor:** identifique controles operacionais e de infraestrutura geridos pelo provedor, bem como documentação ou garantias relevantes. Isso não comprova que a aplicação esteja segura.
- **Equipe da aplicação / cliente:** revise lógica da aplicação, IAM, autorização, classificação de dados, permissões de banco e storage, segredos, configuração de criptografia, controles de rede e logging.
- **Terceiros:** mapeie quais provedores de IA, analytics, identidade, assinatura digital, mensageria (ex.: WhatsApp), storage e outros processam, armazenam ou acessam dados da aplicação ou de pacientes; inspecione fluxos e permissões entre organizações.
- **Responsabilização:** documente controles indefinidos ou sem responsável claro e evidências que precisam ser obtidas junto aos fornecedores.

Não confunda responsabilidade compartilhada em nuvem com os papéis jurídicos da LGPD. Um provedor de nuvem não é automaticamente operador sob a LGPD, nem o dono da aplicação é automaticamente controlador; os papéis dependem das decisões reais de tratamento e dos arranjos contratuais.

### 7. Usar o OWASP Top 10:2025 como lente de revisão

Mantenha o **OWASP Top 10:2025** em mente ao revisar riscos aplicáveis. Utilize-o como lista de verificação complementar para ampliar cobertura e organizar observações pertinentes — não como substituto para análise contextual, evidências, modelagem de ameaças ou verificação. Não presuma que todas as categorias se aplicam e não reporte uma vulnerabilidade apenas porque um item da lista não está visível em um trecho isolado. Para riscos específicos de IA, examine também as fronteiras de confiança e controles descritos nesta skill.

## Achados e relatório

Para cada achado no Modo de Revisão e Triagem, inclua:

1. **Título** — descrição concisa do problema.
2. **Evidência** — código exato, configuração ou comportamento observado que sustenta o achado.
3. **Impacto potencial** — o que pode acontecer e quem é afetado.
4. **Severidade** — Crítica, Alta, Média, Baixa ou Indeterminada.
5. **Confiança** — Alta, Média ou Baixa, conforme a qualidade da evidência.
6. **Status** — Confirmado na evidência fornecida, Suspeito ou Requer investigação.
7. **Ação recomendada** — mitigação mínima e prática ou próxima verificação.
8. **Verificação adicional** — arquivos, middlewares, políticas, configurações, documentação de provedor ou comportamento em tempo de execução necessários.

Mantenha estes conceitos separados:
- **Severidade** estima o impacto potencial.
- **Confiança** descreve o quão fortemente a evidência sustenta o achado.
- **Status** indica o que foi efetivamente comprovado.

Exemplos de potencial alto impacto incluem exposição de prontuários entre *tenants*, acesso não autorizado a histórico médico, adulteração de receitas/documentos assinados ou divulgação descontrolada de dados identificáveis de pacientes a serviços externos. Trate esses casos apenas como sinais de triagem: atribua a severidade com base na exposição real, impacto e contexto, não por regra fixa.

Organize os achados por severidade, mantendo confiança e status explícitos. Conclua com os principais riscos, incertezas ou contexto ausente, responsabilidades de nuvem ou terceiros a verificar, próximos passos priorizados e o escopo/limitações da revisão.

## Disciplina de evidência e limitações

- Separe fatos observados de suposições, hipóteses e recomendações.
- Não classifique um problema suspeito como confirmado apenas porque um trecho visível não exibe uma validação; o controle pode existir em middleware, políticas do banco ou outra camada.
- Declare riscos condicionais quando o contexto estiver incompleto; solicite a menor evidência adicional necessária.
- Inspeção de código não comprova explorabilidade. Nunca afirme ter executado testes, verificado comportamento em produção ou realizado testes de intrusão sem tê-lo feito de fato.
- Um trecho isolado ou a ausência de achados não comprova que a aplicação inteira seja segura.
- Prefira recomendações específicas, mínimas e adequadas à arquitetura; não presuma tecnologias nem prescreva complexidade desnecessária.
- Consulte a documentação específica de cada provedor antes de atribuir responsabilidades em nuvem.
- Observações técnicas apoiam a avaliação de privacidade, mas não estabelecem conformidade com a LGPD nem substituem aconselhamento jurídico.
- Esta skill destina-se apenas à triagem inicial; não substitui modelagem de ameaças, revisão arquitetural, testes de intrusão, avaliação especializada de segurança ou revisão clínica/regulatória.
