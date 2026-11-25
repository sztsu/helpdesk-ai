# 01. Visão Geral, Escopo e Regras de Negócio

> **Sistema:** HelpDesk AI 2.0 — Chatbot e Agente Autônomo de ITSM (NexusTech S.A.)  
> **Autor:** Gustavo Negreiros  
> **Instituição:** Centro Universitário do Distrito Federal (UDF) — Tendências em Ciência da Computação  
> **Docente:** Profa. Kadidja Valéria  

---

## 1. Classificação do Sistema

| Dimensão | Classificação Atual |
| :--- | :--- |
| **Tipo de sistema** | Aplicação Web híbrida (Chatbot + Agente Autônomo de IA no frontend) integrada a automação Low Code / No Code em nuvem (Google Forms, Google Sheets, Make.com, Gmail e API Google Gemini). |
| **Público principal** | **Colaboradores finais** dos 5 setores corporativos da NexusTech S.A. (solicitantes de suporte), **equipe de suporte de TI Nível 1 e Nível 2** (operadores da fila) e **gestores/auditores** (avaliação de SLA, equidade e compliance). |
| **Estágio do projeto** | **Protótipo funcional v2.0 em desenvolvimento evolutivo** (MVP validado em operação local e via GitHub Pages, evoluindo da automação de prompt estático v1.0 para arquitetura de Agente Autônomo em 4 camadas). |

---

## 2. Objetivo do Sistema e Problema Real

### 2.1 Contexto Organizacional: NexusTech S.A.
O sistema foi projetado para resolver um gargalo real de Gestão de Serviços de TI (*IT Service Management* — ITSM) modelado no estudo de caso da **NexusTech S.A.**, uma organização de tecnologia financeira e logística estruturada em cinco departamentos com diferentes impactos operacionais e financeiros.

### 2.2 O Problema Real
Em centrais de atendimento convencionais, todos os incidentes entram em uma fila linear única (*FIFO — First-In, First-Out*). Esse modelo produz quatro falhas recorrentes:
1. **Cegueira setorial:** Uma indisponibilidade no cluster de produção (NOC) ou no faturamento (Financeiro) compete por atenção com dúvidas administrativas ou falhas de impressora no RH.
2. **Urgência subjetiva:** Quando o próprio usuário escolhe a prioridade no formulário, quase todos os chamados são marcados como "urgentes", destruindo a confiabilidade dos Acordos de Nível de Serviço (SLAs).
3. **Sobrecarga do Suporte Nível 1:** Analistas gastam a maior parte do tempo triando relatos leigos, mal formatados ou vagos e respondendo procedimentos repetitivos (reconexão VPN, limpeza de cache, reinício de sessão).
4. **Riscos éticos e de privacidade em soluções de IA ingênuas:** Chatbots baseados apenas em "prompt estático" não mascaram dados sensíveis (LGPD), alucinam quando o relato é vago, são vulneráveis a *Prompt Injection* e não oferecem mecanismo formal para o usuário contestar uma decisão algorítmica.

### 2.3 Objetivo do HelpDesk AI 2.0
Automatizar a recepção conversacional, a triagem contextual sensível ao negócio (*Business-Aware Triage*) e o autoatendimento seguro de primeiro nível por meio de um **Agente Autônomo de IA de 4 camadas**, garantindo:
* Priorização justa baseada no cruzamento **Setor Corporativo x Gravidade Técnica**.
* Proteção ativa de dados pessoais (*Privacy by Design*).
* Respostas ancoradas em manuais internos (RAG) com controle de fluxo multi-turno.
* Mecanismos de equidade (*aging* anti-inanição), auditabilidade (*Accountability*) e contestação humana (*Remediação*).

---

## 3. Escopo e Limites da Entrega

### 3.1 Dentro do Escopo (Entrega Atual v2.0)
* Interface Web completa (`index.html`) com Chatbot conversacional e Painel de Telemetria ao vivo das 4 camadas do agente.
* Camada de percepção com detecção e mascaramento automático de PII (CPF, senhas expostas, cartões, chaves de API e e-mails pessoais).
* Gestão de memória em 3 dimensões: curto prazo (diálogo multi-turno), médio prazo (histórico persistente com botão "Limpar Memória / LGPD") e longo prazo (Base de Conhecimento RAG com 6 manuais internos da NexusTech).
* Guardrails contra ataques adversariais (*Prompt Injection*, pedidos destrutivos e tom hostil).
* Fila operacional interativa com métricas KPI, auditoria de equidade (*aging*), trilha de *Accountability*, botão de contestação humana e exportação de backup em JSON.
* Suíte automatizada de 9 testes (funcionais, adversariais, privacidade e stress/HTTP 503) executável no navegador.
* Integração opcional ao vivo com a API Google Gemini (`gemini-2.5-flash`) com fallback automático para o motor local em caso de erro HTTP 503/429.
* Manutenção da esteira v1.0 (Google Forms + Google Sheets + Make.com com `Parse JSON` + Gmail) como camada No Code / Low Code documentada.

### 3.2 Fora do Escopo (Limites Explícitos)
* Execução remota automatizada de scripts ou comandos administrativos na máquina do usuário final (proibido por segurança).
* Banco de dados relacional corporativo em servidor dedicado (PostgreSQL/SQL Server) com autenticação SSO Active Directory/LDAP em produção.
* Integração bidirecional produtiva com Jira Service Management, ServiceNow ou alertas push pagos em Slack/Microsoft Teams (mapeados como evoluções futuras no roadmap).
* Atendimento humano em tempo real (chat ao vivo com analista N2) dentro da interface atual — o sistema realiza o escalonamento estruturado e registra o protocolo na fila da equipe técnica.

---

## 4. Regras de Negócio (RN)

As políticas abaixo regem integralmente o motor de decisão do agente (`assets/agent.js`):

### RN-01 — Matriz de Criticidade Setor x Incidente
O mesmo sintoma técnico recebe prioridades e prazos de SLA distintos conforme o impacto de negócio do departamento solicitante:

| Código | Setor Corporativo | Impacto de Negócio | Prioridade Base | Janela de SLA | Fila Padrão | Peso |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| `noc` | 1. Operações e Infraestrutura Crítica (NOC) | Altíssimo (clientes finais e disponibilidade global) | **P1 / P2** | 15 min a 1 hora | Nível 2 Infraestrutura | 10 |
| `financeiro` | 2. Financeiro e Faturamento | Alto (fluxo de caixa, ERP e emissão de NF-e) | **P1 / P2** | 30 min a 1 hora | Nível 2 Sistemas | 8 |
| `engenharia` | 3. Engenharia e Desenvolvimento | Médio/Alto (esteiras CI/CD, Git e deploys) | **P2 / P3** | 2 horas a 4 horas | Nível 2 Sistemas | 7 |
| `comercial` | 4. Comercial e Atendimento (CX) | Médio (vendas, CRM e telefonia VoIP) | **P2 / P3** | 2 horas a 6 horas | Nível 1 Suporte | 5 |
| `rh` | 5. Recursos Humanos e Administrativo | Moderado (rotinas internas e impressoras) | **P3 / P4** | 4 horas a 24 horas | Nível 1 Suporte | 3 |

### RN-02 — Regra de Relato Vago (Controle Multi-Turno Anti-Alucinação)
Se a mensagem inicial do colaborador possuir menos de 4 palavras e não corresponder a nenhum termo técnico da Base de Conhecimento (ex.: *"não funciona"*, *"travou"*), o agente **não abre chamado imediatamente** nem inventa solução. Ele retém o estado na memória de curto prazo (`pendingClarification`) e solicita esclarecimento objetivo sobre qual sistema falhou e qual erro foi exibido.

### RN-03 — Ancoragem em Base de Conhecimento (RAG) e Limite de Nível 1
Todo guia de autoatendimento gerado ao usuário deve ser limitado a **3 passos seguros de Nível 1** (verificação de rede/VPN, reinício da aplicação, limpeza de cache, checagem física ou validação de certificado). O agente é proibido de instruir alterações no registro do sistema, comandos destrutivos (`rm -rf`, `DROP TABLE`, formatação) ou divulgação de credenciais.

### RN-04 — Política de Privacidade (*Privacy by Design* / LGPD)
Antes que qualquer texto chegue à camada de raciocínio, à memória persistente ou a uma API externa (Gemini), a Camada 1 deve detectar e substituir por marcadores seguros: CPF (`[CPF_MASCARADO]`), números de cartão (`[CARTAO_MASCARADO]`), chaves de API (`[CHAVE_API_MASCARADA]`), senhas declaradas (`[SENHA_MASCARADA]`) e e-mails pessoais (`[EMAIL_PESSOAL_MASCARADO]`).

### RN-05 — Política de Defesa Adversarial
Tentativas explícitas de manipulação de regras (*Prompt Injection* tentando forçar prioridade P1 ou personificar outro setor) e pedidos inseguros devem ser **bloqueados preventivamente** antes da abertura de ticket, gerando registro imediato na trilha de auditoria (`ADVERSARIAL_BLOCK`). Relatos com tom hostil ou agressivo **não** resultam em bloqueio nem em rebaixamento punitivo: o agente mantém postura profissional e preserva a equidade da classificação.

### RN-06 — Política de Equidade e Anti-Inanição (*Aging* e Reincidência)
Para equilibrar eficiência técnica e justiça organizacional (Eysenck & Eysenck, 2023):
1. **Reincidência:** Se a Memória de Médio Prazo identificar que o mesmo colaborador já abriu chamado anterior no mesmo setor e categoria técnica, chamados classificados como P3 ou P4 são promovidos automaticamente em 1 nível (`P4 -> P3` ou `P3 -> P2`) com redução de 50% no SLA.
2. **Aging de Fila:** A auditoria periódica de equidade promove chamados P3/P4 antigos que aguardam sem atendimento, impedindo que setores administrativos fiquem indefinidamente presos atrás de demandas contínuas do NOC e do Financeiro.

### RN-07 — Direito à Contestação e Remediação Humana
Todo chamado triado pela IA deve disponibilizar ao solicitante a ação explícita **"Contestar decisão da IA"**. Ao ser acionada, o status muda para `Em Revisão Humana (Contestação)`, o motivo é salvo no ticket e um evento `HUMAN_REMEDIATION` é gravado na trilha de *Accountability*.

### RN-08 — Continuidade Operacional (Fallback HTTP 503)
Quando a integração externa com a API Google Gemini retornar indisponibilidade (ex.: `HTTP 503 High Demand`, `HTTP 429` ou falha de rede), o agente não pode interromper o atendimento ao colaborador; deve acionar automaticamente o motor local determinístico ancorado na base RAG e sinalizar a operação em modo de contingência.
