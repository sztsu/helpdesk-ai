# Registro das Etapas de Desenvolvimento — HelpDesk AI (NexusTech S.A.)

Este documento consolida os registros técnicos de cada uma das 5 etapas do ciclo de desenvolvimento do protótipo **HelpDesk AI**, em conformidade com o cronograma da disciplina **Tendências em Ciência da Computação (UDF — Profa. Kadidja Valéria)**.

---

## 01. Definir o Desafio (08/10)
**Status do Checklist:** 4 / 4 itens concluídos.

### Problema, público, objetivo e critérios de sucesso:
* **Problema e Público Beneficiado:** Na empresa hipotética NexusTech S.A. (composta por 5 setores: Operações/NOC, Financeiro, Engenharia, Comercial e RH/Administrativo), os chamados de suporte técnico caíam em uma fila única sem contexto organizacional. Isso gerava sobrecarga nos analistas de Nível 1, atraso no atendimento de setores críticos e classificação subjetiva de urgência pelos próprios usuários. O público beneficiado compreende tanto a equipe de TI (que recebe chamados já triados e priorizados) quanto os colaboradores dos 5 departamentos (que recebem orientação imediata).
* **Objetivo e Resultado Esperado:** Desenvolver o protótipo funcional HelpDesk AI para automatizar a triagem primária de chamados, cruzando o relato técnico da falha com o setor corporativo do solicitante para atribuir Categoria, Prioridade (P1 a P4), SLA estimado, Fila de Escalonamento e enviar por e-mail um roteiro imediato de autoatendimento.
* **Delimitação de Escopo:** 
  * *Dentro do escopo:* Captura estruturada via formulário com seleção de setor, persistência em planilha de governança, inferência contextual via IA generativa e disparo automático de e-mail com orientações de primeiro nível.
  * *Fora do escopo:* Execução remota automatizada de scripts na máquina do usuário, controle de concorrência em banco de dados relacional dedicado e atendimento humano de Nível 2/3.
* **Critérios de Sucesso:** 1) Registro íntegro de 100% das submissões na planilha; 2) Diferenciação correta de prioridade e SLA pela IA para o mesmo problema em setores de criticidades distintas (ex.: Financeiro vs. Administrativo); 3) Entrega do e-mail de autoatendimento em menos de 1 minuto após o processamento do gatilho.

---

## 02. Escolher e Configurar a Plataforma (15/10)
**Status do Checklist:** 4 / 4 itens concluídos.

### Plataforma escolhida, justificativa e configuração realizada:
* **Comparação de Ferramentas:** Foram avaliadas as plataformas Zapier, n8n e Make para orquestração Low Code, além de Google Forms/Sheets e Airtable para a camada No Code.
* **Análise de Limites, Custos e Recursos:** O Zapier apresentou restrições severas no plano gratuito para fluxos de múltiplas etapas (multi-step zaps). O n8n exigiria auto-hospedagem (self-hosting) via Docker para uso gratuito contínuo. O Make (Integromat) oferece plano gratuito com 1.000 operações mensais, suporte visual a múltiplos módulos encadeados e manipulação nativa de payloads JSON, operando em conjunto com a camada gratuita do Google Workspace e da API do Google Gemini.
* **Justificativa da Decisão:** Optou-se pela tríade Google Forms + Google Sheets (No Code), Make (Low Code) e Google Gemini + Gmail (Vibecode/Entrega) pela ausência de custo financeiro, rapidez de integração nativa com o ecossistema Google e clareza visual na inspeção de dados entre os nós do fluxo.
* **Configuração do Ambiente e Teste Inicial:** Foi estruturado o formulário contendo os 5 setores da NexusTech S.A., vinculada a planilha com colunas adicionais de governança (`Categoria`, `Prioridade`, `SLA Estimado` e `Fila Responsável`) e estabelecida a conexão OAuth2 no Make com execução bem-sucedida de um disparo de teste inicial.

---

## 03. Integrar Vibecode / IA (22/10)
**Status do Checklist:** 5 / 5 itens concluídos.

### Prompts utilizados, integração implementada e ajustes realizados:
* **Estruturação Inicial do Prompt:** Na primeira iteração, o prompt solicitava apenas uma resposta livre para o problema técnico relatado. Observou-se que a resposta gerada era excessivamente longa, sem padronização de SLA e desconsiderava a criticidade do setor solicitante.
* **Refinamento Iterativo e Ajustes:** O prompt foi reestruturado com definição explícita de papel (motor especialista de ITSM da NexusTech S.A.), contexto organizacional (tabela de pesos dos 5 setores corporativos) e restrições de formato. Exigiu-se saída padronizada contendo: Categoria Técnica, Prioridade Calculada (P1 a P4), SLA Estimado, Fila de Escalonamento e Guia de Autoatendimento restrito a 3 passos seguros.
* **Integração Implementada:** No Make, as variáveis dinâmicas capturadas da planilha (`{{Nome}}`, `{{Setor}}`, `{{Título}}`, `{{Descrição}}`) foram injetadas no módulo da IA, cujo retorno alimenta tanto a atualização da linha no Google Sheets quanto o corpo do e-mail no módulo do Gmail.
* **Segurança e Proteção de Credenciais:** Todas as chaves de API e tokens OAuth2 foram armazenados exclusivamente no cofre de conexões criptografadas do Make, garantindo que nenhuma credencial, senha ou dado pessoal real constasse no código-fonte ou no repositório público do GitHub.

---

## 04. Testar e Verificar a Segurança (29/10)
**Status do Checklist:** 6 / 6 itens concluídos.

### Casos de teste, resultados, falhas e correções:
* **Testes do Fluxo Principal com Dados Fictícios:** Foram executados cenários simulando diferentes departamentos da NexusTech S.A.:
  * *Caso 1 (Setor Administrativo):* Relato de falha na impressora departamental -> Classificado corretamente como Hardware, Prioridade P4, SLA de 24h, Fila Nível 1.
  * *Caso 2 (Setor Financeiro):* Relato de erro ao emitir nota fiscal no ERP -> Classificado como Software, Prioridade P1, SLA de 30 minutos, Fila Nível 2 Sistemas.
  * *Caso 3 (Setor Operações/NOC):* Relato de queda de conectividade em servidor -> Classificado como Rede/Infraestrutura, Prioridade P1, SLA de 15 minutos, Fila Nível 2 Infraestrutura.
* **Validação de Entradas Inválidas e Campos Vazios:** Identificou-se que o envio de campos em branco ou e-mails malformados causava falha no módulo de envio do Gmail no Make.
* **Correções Aplicadas:** Ativou-se a obrigatoriedade de preenchimento em todos os campos do Google Forms, validação nativa de expressão regular para formato de e-mail válido e substituição de campo aberto por lista suspensa fechada para o campo `Setor Corporativo`. No prompt da IA, adicionou-se uma cláusula de contingência para relatos vagos ou incompreensíveis, instruindo o modelo a classificar como P3 e solicitar esclarecimentos adicionais sem inventar procedimentos técnicos.
* **Verificação de Segurança e Critérios de Sucesso:** As permissões da planilha foram restritas e todos os testes repetidos atingiram 100% dos critérios de sucesso estabelecidos na Etapa 01.

---

## 05. Consolidação Final — Documentar a Solução
**Status do Checklist:** 5 / 5 itens concluídos.

### Link do projeto, evidências, limitações e próximos passos:
* **Repositório Oficial no GitHub:** https://github.com/sztsu/helpdesk-ai
* **Resumo da Solução e Arquitetura:** O protótipo HelpDesk AI integra Google Forms e Google Sheets (No Code), Make (Low Code) e Google Gemini (Vibecode) para automatizar a triagem contextual de chamados de TI por setor corporativo na empresa hipotética NexusTech S.A., atualizando a planilha de governança e enviando instruções de autoatendimento via Gmail.
* **Limitações Registradas:** 1) Risco de diagnósticos genéricos caso o relato do usuário seja excessivamente superficial (mitigado por restrições no prompt); 2) Limite mensal de 1.000 operações e latência de polling na camada gratuita do Make; 3) Tráfego de dados em provedores externos de nuvem, exigindo anonimização e instâncias empresariais privadas para conformidade estrita com a LGPD em produção real.
* **Próximos Passos:** Integração com canais de comunicação instantânea (Slack e Microsoft Teams) para alertas críticos de chamados P1, conexão da IA com a base de conhecimento interna da empresa via RAG (Retrieval-Augmented Generation) e integração bidirecional com Jira Service Management.
