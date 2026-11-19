# BR App & AI Safety Review Skill

Skill para agentes de IA voltada à triagem técnica inicial de **banco de dados e privacidade (contexto Brasil)**, **autenticação e guards em edge functions** e **pontos críticos de AI safety** em aplicações.

## Motivação e contexto

Este projeto nasceu como um artefato de estudo e portfólio a partir da vontade de ajudar a revisar a base técnica de uma aplicação voltada ao mercado brasileiro, unindo interesses em **cibersegurança**, **machine learning** e **AI safety**.

Como estou no início da jornada prática nesses temas, a skill foi desenhada para **reduzir falsas certezas**, evitar diagnósticos inflados e incentivar a verificação humana nos pontos certos.

## O que esta skill faz

* Revisa sinais de exposição excessiva de dados pessoais, isolamento por usuário/contexto e envio de dados sensíveis para fluxos ou logs de IA.
* Verifica ordem de validação, uso de privilégios elevados e controles básicos contra abuso em autenticação e edge functions/guards.
* Avalia fronteiras de AI safety na aplicação, como separação entre instruções e entradas externas, vazamento de contexto e limites sobre ações/saídas do modelo.
* Classifica achados separando evidência no código, risco potencial e necessidade de checagem manual.

## Limitações importantes

* **Não substitui** revisão de segurança profunda, teste de invasão (pentest), revisão de arquitetura completa ou parecer jurídico/regulatório (como conformidade formal com a LGPD).
* A ausência de uma trava visível em um único trecho de código **não prova** que a falha existe em produção; o guard ou filtro pode estar em outra camada.

## Como usar

1. Adicione `SKILL.md` ao diretório de skills do seu agente (ou cole o conteúdo como instrução de fluxo quando quiser revisar trechos de código).
2. Envie o código de banco, auth/edge functions ou fluxos de IA pedindo uma revisão inicial.
3. Valide os achados no contexto completo do repositório antes de aplicar mudanças.

## Idiomas

* Português: [README.md](README.md) e [SKILL.md](SKILL.md)
* English: [README.en.md](README.en.md) e [SKILL.en.md](SKILL.en.md)
