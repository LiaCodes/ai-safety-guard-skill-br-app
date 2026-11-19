---
name: br-app-ai-safety-review
description: Realiza triagem tecnica inicial de seguranca, dados pessoais (contexto Brasil) e AI safety em codigo de banco de dados, autenticacao, guards de edge functions e fluxos com IA. Use quando o usuario pedir revisao de seguranca, auth, guards, edge functions, banco de dados, privacidade, dados pessoais ou AI safety em aplicacoes.
---

# BR App & AI Safety Review

## Resumo

Auxilia na revisão técnica inicial de código para aplicações voltadas ao Brasil cobrindo banco de dados e privacidade, autenticação e guards em edge functions, e fronteiras de AI safety na aplicação, com postura cautelosa e verificável.

## Quando usar

* Revisão inicial de consultas, políticas de acesso, triggers ou manipulação de dados de usuários no Brasil.
* Inspeção de autenticação, autorização, uso de privilégios e guards em edge functions ou rotas de backend.
* Avaliação de fluxos com IA que recebem entradas externas, montam contexto ou disparam ações e respostas.
* Triagem técnica de apoio para desenvolvedores ou revisores em estágio inicial.

## Etapas

1. **Mapeie a fronteira analisada:** identifique o que é entrada externa, lógica de autenticação/guard, acesso a banco de dados e interação com IA.
2. **Revise banco de dados e dados pessoais (contexto Brasil):**
   * Verifique se consultas, retornos ou campos abertos expõem dados pessoais (como e-mail, telefone, documentos, histórico ou queixas sensíveis) além do necessário para o papel de quem acessa.
   * Confira se o isolamento por usuário, organização ou contexto é aplicado de forma confiável e se operações destrutivas em registros sensíveis ou financeiros têm proteção adequada.
   * Observe se dados pessoais ou textos livres de usuários entram em logs, caches, payloads externos ou fluxos de IA sem necessidade clara.
3. **Revise auth e guards em edge functions:**
   * Confira se identidade e autorização são validadas antes de consultar dados, alterar estado ou chamar serviços externos, sem confiar cegamente em identificadores vindos do payload do cliente.
   * Verifique se chaves ou operações privilegiadas ficam protegidas no servidor e são acionadas apenas após checagem de permissão.
   * Observe se há controles básicos contra abuso (limites de taxa, validação de entrada, bloqueio de enumeração ou proteção em rotas públicas e custosas).
4. **Revise a fronteira de AI safety:**
   * Verifique se entradas de usuário, documentos ou dados externos são tratados como dados não confiáveis, separados das instruções do sistema.
   * Confira se o contexto montado para o modelo pode vazar dados de outros usuários, informações restritas ou segredos.
   * Observe se saídas do modelo que disparam consultas, ferramentas ou alterações de estado passam por validação e limites claros.
5. **Entregue o parecer com confiança calibrada:**
   * Organize cada achado com: **Evidência no trecho**, **Risco potencial**, **Nível e confiança (Alto / Médio / Baixo ou Ponto a verificar)** e **O que verificar no restante do código**.

## Gotchas

* **Não trate ausência visível como falha confirmada:** um guard, política de banco, middleware ou validação pode existir em outro arquivo. Aponte o risco condicional e indique o que checar no contexto completo.
* **Evite soluções genéricas sem causa raiz:** explique qual princípio está falhando e sugira uma correção mínima, clara e compatível com os contratos existentes.
* **Não emita parecer jurídico nem garanta segurança completa:** deixe explícito que esta é uma triagem técnica inicial e não substitui testes, revisão de arquitetura nem avaliação especializada.
