# 05. Ambiente, Deploy, Monitoramento, Backup e Contingência

> **Sistema:** HelpDesk AI 2.0 — NexusTech S.A.  
> **Foco:** Execução local, variáveis de ambiente, publicação em nuvem, observabilidade e continuidade operacional.

---

## 1. Como Rodar o Projeto Localmente

### 1.1 Pré-requisitos
* **Navegador moderno:** Google Chrome, Microsoft Edge, Mozilla Firefox ou Safari (atualizados).
* **Dependências externas:** **Nenhuma.** O projeto v2.0 segue arquitetura *Zero-Build* (HTML5 + CSS3 + JavaScript ES6+ Vanilla), permitindo execução imediata sem precisar instalar Node.js, Python ou bancos de dados locais.
* *(Opcional)* **Git** para clonar o repositório e uma **API Key gratuita do Google AI Studio** caso deseje ativar o modo *Gemini API Live* na sessão.

### 1.2 Instalação e Execução Local

**Opção A — Execução Direta (1 clique):**
```bash
git clone https://github.com/sztsu/helpdesk-ai.git
cd helpdesk-ai
```
Abra o arquivo `index.html` diretamente no navegador (duplo clique no Windows/macOS ou via comando `start index.html`).

**Opção B — Servidor HTTP Local (opcional para desenvolvimento):**
```bash
# Usando Python 3 (se instalado)
python -m http.server 8080

# Ou usando extensão Live Server no VS Code
# Acesse: http://localhost:8080/index.html
```

### 1.3 Variáveis de Ambiente e Segredos
Por tratar-se de aplicação estática no frontend (v2.0) + orquestração gerenciada na nuvem (v1.0), o gerenciamento de credenciais segue políticas estritas de segurança:

| Variável / Segredo | Onde é Armazenada | Escopo e Ciclo de Vida |
| :--- | :--- | :--- |
| `helpdesk_gemini_key` (v2.0 Web) | `window.sessionStorage` (via modal *Agente Local / Gemini API Live*) | Opcional. Volátil: apagada automaticamente ao fechar a aba do navegador. Nunca versionada no Git. |
| `GEMINI_API_KEY` (v1.0 Make.com) | Cofre de Conexões Criptografadas do Make.com | Utilizada no módulo `Google Gemini AI: Generate a response`. Fora do repositório público. |
| `GOOGLE_OAUTH2_TOKEN` (v1.0 Make.com) | Cofre OAuth2 do Make.com | Autoriza leitura/gravação no Google Sheets e envio de e-mail via Gmail. |

Caso o sistema evolua futuramente para um backend dedicado (Node.js/FastAPI), o arquivo `.env` local (ignorado pelo `.gitignore`) deverá seguir o formato:
```env
GEMINI_API_KEY=sua_chave_aqui
GOOGLE_SHEETS_SPREADSHEET_ID=1wQwLty4UdgL0f-Vay306g_o7PP_l3d6vIycTtGo4vec
ENABLE_PII_MASKING=true
EQUITY_AGING_THRESHOLD_PERCENT=75
```

---

## 2. Deploy e Infraestrutura

### 2.1 Hospedagem da Aplicação Web (v2.0 — GitHub Pages)
* **Provedor:** GitHub Pages (publicação automática a partir da branch `main`, diretório raiz `/`).
* **URL de Produção:** `https://sztsu.github.io/helpdesk-ai/`
* **Pipeline de Publicação:**
  1. Alterações validadas localmente via suíte automatizada (`TC-01` a `TC-09`) e inspeção visual no navegador.
  2. Commit padronizado (*Conventional Commits*) e `git push origin main`.
  3. O workflow gerenciado do GitHub Pages publica automaticamente `index.html`, `assets/` e `slides/` sobre HTTPS com certificado TLS gerenciado.

### 2.2 Infraestrutura da Esteira Assíncrona em Nuvem (v1.0)
* **Portal de Formulário:** Google Forms (Google Cloud — disponibilidade gerenciada).
* **Base Tabular:** Google Sheets (planilha `Form_Responses` com formatação condicional P1/P2/P3).
* **Orquestrador:** Make.com Free Tier (5 módulos encadeados, cota de 1.000 operações/mês).
* **Serviço de Mensageria:** Gmail API via OAuth2 (`Raw HTML`).

### 2.3 Monitoramento e Logs
* **Observabilidade em Tempo Real (v2.0):** O painel lateral do `index.html` exibe latência em milissegundos, motor ativo, redações PII, memórias consultadas e cada chamada de ferramenta (`tool_call`) em JSON.
* **Trilha de Auditoria Persistente:** A aba *Fila & Equidade* consolida todos os eventos críticos (`TICKET_CREATED`, `ADVERSARIAL_BLOCK`, `EQUITY_AGING_AUDIT`, `HUMAN_REMEDIATION`) em ordem cronológica reversa.
* **Telemetria em Nuvem (v1.0):** O Make.com registra nativamente histórico de bundles, contagem de tokens (`PromptTokenCount`, `CandidatesTokenCount`), `Response ID` e códigos de erro HTTP por execução.

---

## 3. Backup, Recuperação e Plano de Contingência

### 3.1 Rotina de Backup e Restauração
* **Na Aplicação Web (v2.0):** Na aba *Fila & Equidade*, o botão **Exportar Backup JSON** gera instantaneamente o arquivo `helpdesk_ai_backup_AAAA-MM-DD.json` contendo o snapshot completo de todos os chamados (`tickets`) e da trilha de auditoria (`auditLog`), pronto para arquivamento externo ou migração.
* **Na Planilha Corporativa (v1.0):** O Google Sheets mantém versionamento automático histórico (*Histórico de versões*) no Google Drive, permitindo restauração pontual de células ou exportação em `.xlsx`/`.csv`.
* **No Código-Fonte e Documentação:** Versionamento distribuído em Git com espelho remoto no GitHub (`sztsu/helpdesk-ai`).

### 3.2 Plano de Resposta a Incidentes e Contingência

| Cenário de Incidente | Sintoma Detectado | Ação Automática / Contingência | Responsável |
| :--- | :--- | :--- | :--- |
| **Indisponibilidade da API Gemini (`HTTP 503` / `429`)** | Erro de alta demanda ou estouro de cota gratuita. | Acionamento imediato de `activate_fallback_contingency()` na Camada 3/4: o agente muda para o motor determinístico + base RAG local com `0%` de queda no atendimento e sinaliza aviso transparente ao usuário. | Automático (Agente) + Monitoramento N2 |
| **Tentativa de Ataque Adversarial (*Prompt Injection* / Destrutivo)** | Padrão detectado por `evaluateAdversarialRisk()`. | Bloqueio preventivo antes de criar chamado, recusa segura e gravação do evento `ADVERSARIAL_BLOCK` na trilha de auditoria para investigação de segurança. | Automático (Camada 3) + Segurança da Informação |
| **Exposição Acidental de PII pelo Usuário (LGPD)** | CPF, senha, cartão ou chave de API digitados no chat. | Redação automática na Camada 1 (`[CPF_MASCARADO]`, `[SENHA_MASCARADA]`) antes de qualquer gravação ou chamada de rede; orientação para o usuário trocar a senha exposta. | Automático (Camada 1) + DPO / Suporte N1 |
| **Subestimação de Criticidade pela IA** | Colaborador discorda do SLA ou da prioridade atribuída. | Acionamento do botão **Contestar decisão da IA** (Remediação), movendo o protocolo para `Em Revisão Humana (Contestação)` com análise prioritária do plantão N1/N2. | Colaborador + Analista Humano N1/N2 |
| **Esgotamento de Cota do Make.com (1.000 ops/mês)** | Cenário pausado no provedor Low Code. | Operação integral absorvida pela interface web `index.html` com exportação periódica de backup JSON até a renovação do ciclo ou migração para plano corporativo. | Gestor de TI |
