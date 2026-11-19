---
name: ai-safety-guard-skill-br-app
description: Realiza triagem técnica inicial de segurança de aplicações, tratamento de dados pessoais no contexto brasileiro, autenticação, autorização, acesso a bancos de dados, edge functions e limites de segurança de aplicações com IA. Utilize ao revisar código, APIs, bancos de dados, privacidade, dados pessoais ou fluxos de IA.
---

# Revisão de Segurança de Aplicações e IA — Contexto Brasileiro

## Resumo

Uma skill para agentes de IA realizarem triagem técnica inicial de aplicações voltadas ao mercado brasileiro, abrangendo segurança de bancos de dados, tratamento de dados pessoais, autenticação e autorização, proteções em edge functions e limites de segurança em aplicações com IA.

A skill prioriza evidências verificáveis, confiança calibrada, limitações explícitas e recomendações direcionadas para verificação humana.

## Quando Utilizar

- Revisão inicial de consultas, políticas de acesso e tratamento de dados pessoais de usuários brasileiros.
- Inspeção de autenticação, autorização, APIs, edge functions e funções de backend.
- Avaliação de fluxos que envolvam entrada de dados do usuário, geração aumentada por recuperação (RAG) ou integração com modelos de IA.
- Triagem inicial de riscos de segurança para desenvolvedores e revisores.
- Identificação de riscos técnicos potencialmente relacionados aos requisitos da LGPD.

## Etapas da Revisão

### 1. Mapeie as Fronteiras de Confiança

Antes de revisar componentes individuais:

- Identifique entradas confiáveis e não confiáveis.
- Mapeie os mecanismos de autenticação e autorização.
- Identifique os caminhos de acesso ao banco de dados e integrações com serviços externos.
- Identifique onde os dados pessoais entram, circulam e saem da aplicação.
- Mapeie as interações entre usuários, lógica da aplicação, modelos de IA, sistemas de recuperação de contexto e ferramentas.
- Identifique ações que possam modificar dados, acionar serviços externos ou afetar outros usuários.

Não presuma que um controle de segurança está ausente apenas porque ele não aparece no trecho de código fornecido.

### 2. Revise a Segurança do Banco de Dados e o Tratamento de Dados Pessoais

Inspecione as seguintes áreas quando aplicáveis:

**Exposição e minimização de dados**
- Verifique se consultas ou respostas de APIs expõem mais dados pessoais do que o necessário.
- Identifique possíveis exposições de e-mails, telefones, números de documentos de identificação, dados pessoais sensíveis ou registros de outros usuários.
- Verifique se dados sensíveis ou conteúdo livre fornecido por usuários são incluídos em logs, caches, ferramentas de analytics ou payloads enviados a modelos de IA sem necessidade clara.

**Controle de acesso e isolamento**
- Verifique se o isolamento entre usuários ou tenants é aplicado antes da leitura, atualização ou exclusão de registros.
- Inspecione políticas de Row Level Security (RLS), quando aplicável.
- Identifique possíveis problemas de autorização em nível de objeto, incluindo acesso a recursos de outros usuários por meio da manipulação de identificadores.
- Revise funções, views e stored procedures do banco de dados em busca de possíveis elevações de privilégio ou desvios dos controles de acesso esperados.

**Segurança de consultas e armazenamento**
- Procure construções inseguras de consultas e possíveis vulnerabilidades de SQL injection.
- Inspecione permissões de armazenamento de arquivos, políticas de buckets e controles de acesso quando houver upload de arquivos.
- Verifique se o acesso ao banco de dados segue o princípio do menor privilégio.

### 3. Revise Autenticação e Autorização

- Diferencie autenticação (quem é o usuário) de autorização (o que ele tem permissão para fazer).
- Verifique se identidade e permissões são validadas antes de consultar dados, modificar estados ou chamar serviços externos.
- Identifique possíveis ausências de verificações de propriedade dos recursos e autorização das operações.
- Verifique se credenciais privilegiadas permanecem no servidor e não são expostas em bundles do cliente, respostas de APIs ou logs.
- Verifique se credenciais privilegiadas ignoram políticas de segurança do banco de dados, incluindo RLS, e se existem verificações explícitas de autorização que compensam esse comportamento.
- Procure proteções básicas contra abuso, incluindo limitação de requisições, restrições de payloads excessivamente grandes e acesso não autenticado a operações de alto custo.
- Considere se os controles de autenticação e autorização são aplicados consistentemente nos endpoints relevantes.

### 4. Revise os Limites de Segurança da Aplicação com IA

**Limites entre entradas e instruções**
- Verifique se entradas de usuários e dados externos são tratados como conteúdo não confiável, e não como instruções de sistema.
- Considere ataques de prompt injection diretos e indiretos por meio de mensagens de usuários, documentos recuperados, conteúdo da web e outras fontes externas.
- Inspecione se o contexto recuperado pode conter instruções conflitantes com o comportamento esperado da aplicação.

**Vazamento de contexto e dados**
- Verifique se o contexto fornecido ao modelo pode expor dados de outros usuários, informações internas, credenciais ou detalhes sensíveis da aplicação.
- Revise histórico de conversas, sistemas de recuperação de contexto e integrações externas em busca de exposição indevida de informações.
- Identifique dados pessoais enviados desnecessariamente a provedores externos de IA.

**Saídas e ações do modelo**
- Verifique se as saídas do modelo são validadas antes de serem utilizadas em consultas SQL, chamadas de API, renderização HTML ou operações que alterem estados.
- Verifique se consultas geradas e saídas estruturadas são validadas e possuem restrições apropriadas.
- Verifique se ferramentas e ações acionadas pelo modelo aplicam autorização independentemente das decisões do próprio modelo.
- Identifique ações que possam exigir aprovação humana, especialmente operações irreversíveis ou com consequências significativas.
- Procure formas inseguras de utilização das saídas, incluindo possíveis ataques de injeção por meio de conteúdo gerado.

### 5. Revise o Contexto de Privacidade Brasileiro (LGPD)

Identifique sinais técnicos e possíveis preocupações de privacidade relacionados à Lei Geral de Proteção de Dados Pessoais (LGPD).

Quando aplicável:

- Diferencie dados pessoais comuns de dados pessoais sensíveis conforme a LGPD.
- Identifique possíveis problemas relacionados à minimização de dados e à limitação do tratamento às finalidades pertinentes.
- Procure indícios de retenção excessiva ou indefinida de dados pessoais.
- Identifique dados pessoais compartilhados com provedores externos de IA, plataformas de analytics ou outros serviços terceirizados.
- Sinalize possíveis transferências internacionais de dados para avaliação jurídica e de privacidade.
- Identifique mecanismos técnicos relevantes para solicitações dos titulares, incluindo acesso, correção e exclusão de dados.
- Considere se dados pessoais são expostos desnecessariamente em logs, sistemas de monitoramento ou ambientes de desenvolvimento.

Essas verificações identificam sinais técnicos que podem exigir avaliação adicional. Elas não determinam descumprimento legal nem substituem orientação jurídica especializada.

### 6. Apresente Resultados com Confiança Calibrada

Para cada achado, forneça:

- **Título:** Descrição objetiva do possível problema.
- **Evidência no código fornecido:** Código, configuração ou comportamento relevante observado.
- **Risco potencial:** O que pode acontecer caso o problema suspeito exista.
- **Severidade:** Crítica, Alta, Média, Baixa ou Indeterminada.
- **Confiança:** Alta, Média ou Baixa, de acordo com a força das evidências disponíveis.
- **Status da verificação:** Confirmado nas evidências fornecidas, suspeito ou requer investigação adicional.
- **Ação recomendada:** Mitigação ou etapa de investigação prática e objetiva.
- **O que verificar:** Arquivos adicionais, middlewares, configurações, políticas ou comportamentos em execução necessários para estabelecer a existência do problema.

Mantenha severidade e confiança separadas:

- Severidade representa o impacto potencial do problema.
- Confiança representa o quanto as evidências disponíveis sustentam o achado.
- Status da verificação descreve o que foi efetivamente estabelecido.

Não classifique uma vulnerabilidade como confirmada apenas porque um trecho de código não apresenta uma verificação esperada.

## Cuidados e Limitações

### Evite Falsos Positivos

- Não trate a ausência de verificações visíveis como vulnerabilidades confirmadas. A autorização pode ser aplicada por meio de middleware, políticas do banco de dados ou outra camada da aplicação.
- Apresente riscos condicionais quando o contexto disponível estiver incompleto.
- Solicite contexto adicional quando necessário, em vez de inventar detalhes da implementação.
- Diferencie comportamentos observados de suposições e hipóteses.

### Não Confunda Inspeção com Testes

- A inspeção de código, por si só, não comprova que uma vulnerabilidade pode ser explorada.
- Não afirme que testes foram executados, que o comportamento em runtime foi verificado ou que houve um teste de intrusão (pentest), a menos que essas atividades tenham realmente ocorrido.
- A revisão de um trecho de código não comprova a segurança da aplicação inteira.
- Informe o escopo e as limitações das evidências utilizadas.

### Evite Recomendações Genéricas

- Explique o princípio de segurança que está em risco.
- Prefira mitigações mínimas, legíveis e adequadas ao contexto.
- Evite recomendar complexidade ou controles desnecessários, sem relação com o risco observado.
- Não presuma tecnologias ou arquiteturas específicas sem evidências.

### Limitações Importantes

- Esta skill realiza triagem técnica inicial, não auditorias completas de segurança.
- Não substitui testes de intrusão, revisões de arquitetura, modelagem de ameaças ou avaliações especializadas de segurança.
- Não determina conformidade formal com a LGPD nem fornece aconselhamento jurídico.
- Os achados devem ser verificados considerando o restante da base de código e, quando necessário, o comportamento real da aplicação.
- A ausência de problemas identificados não comprova que uma aplicação é segura.

## Formato Esperado da Resposta

Organize os achados por severidade, preservando as informações de confiança e status da verificação.

Finalize com:

1. Principais riscos identificados.
2. Incertezas importantes ou contexto ausente.
3. Próximas etapas de verificação mais relevantes.
4. Uma declaração clara sobre o escopo e as limitações da revisão.

Priorize precisão, rastreabilidade e incerteza responsável em vez da quantidade de achados.
