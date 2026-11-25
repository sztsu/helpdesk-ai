# Changelog — HelpDesk AI (NexusTech S.A.)

Todas as mudanças relevantes deste projeto são documentadas neste arquivo seguindo o padrão *Keep a Changelog* e versionamento semântico (*SemVer*).

---

## [2.0.0] — 2026-11-24 (Trabalho Final / Avaliação A1)

### Adicionado
* **Chatbot & Agente Autônomo Web (`index.html`, `assets/styles.css`, `assets/agent.js`):**
  * Arquitetura explícita de **4 Camadas** com painel lateral de telemetria ao vivo:
    1. *Camada 1 — Percepção e Privacy by Design:* detecção e mascaramento automático de PII (CPF, cartões, senhas expostas, API Keys e e-mails pessoais).
    2. *Camada 2 — Memória em 3 Dimensões:* Curto Prazo (controle de diálogo multi-turno para relatos vagos), Médio Prazo (`localStorage` com detecção de reincidência e botão *Limpar Memória / LGPD*) e Longo Prazo (Base de Conhecimento RAG com 6 manuais internos `KB-NOC-01` a `KB-SEC-06`).
    3. *Camada 3 — Planejamento, Guardrails Adversariais e Equidade:* defesa contra *Prompt Injection*, pedidos destrutivos/senhas e tom hostil; política de *Aging* e reincidência contra inanição de setores administrativos.
    4. *Camada 4 — Ação (Tools):* execução estruturada e auditável de ferramentas JSON (`sanitize_pii`, `search_knowledge_base`, `evaluate_business_sla`, `create_ticket`, `query_ticket_status`, `activate_fallback_contingency`).
  * **Licença Social para Operar (Córdova, 2025):** trilha de *Accountability* persistente e botão **Contestar decisão da IA** (*Pilar 3: Remediação*).
  * **Integração Opcional Live Gemini API (`gemini-2.5-flash`):** chave armazenada apenas em `sessionStorage` com fallback automático para o motor local em caso de erro `HTTP 503`.
  * **Painel de Testes Automatizados (`TC-01` a `TC-09`):** validação executável de cenários funcionais, adversariais, de privacidade e de stress (20 chamados concorrentes + simulação `HTTP 503`).
  * **Exportação de Backup JSON** na aba *Fila & Equidade*.
* **Documentação Modular Padrão TCC (`docs/01` a `docs/07`):**
  * Visão geral, escopo e regras de negócio (`01`).
  * Requisitos funcionais/não funcionais, histórias de usuário, fluxograma Mermaid e wireframes (`02`).
  * Arquitetura de 4 camadas, 3 dimensões de memória, DER, dicionário de dados e contratos de API (`03`).
  * Ética aplicada, vieses, Custo da Equidade (Eysenck & Eysenck, 2023), Confiança/Robustez/Impacto (Mello, 2024) e Licença Social (Córdova, 2025) (`04`).
  * Ambiente, deploy no GitHub Pages, monitoramento, backup e plano de contingência (`05`).
  * Matriz de testes adversariais e stress, padrões de código e manutenção (`06`).
  * Guia do usuário, operação e FAQ (`07`).

### Alterado
* Evolução do `README.md` para portal central da documentação técnica v2.0 com link direto para o `index.html` no GitHub Pages.

---

## [1.2.0] — 2026-10-29

### Adicionado
* Módulo deserializador `JSON (Parse JSON)` no cenário do Make.com (5 módulos encadeados) para gravar `Categoria`, `Prioridade`, `SLA Estimado` e `Fila Responsável` em colunas separadas no Google Sheets.
* Formatação condicional em cores na planilha (`P1` vermelho, `P2` amarelo, `P3` verde).
* Galeria completa de evidências reais em `docs/images/` (`make_pipeline.png`, `google_forms.png`, `google_sheets.png`, `email_received.png`).
* Apresentação executiva em HTML/CSS Editorial Technical Dark Mode (`slides/pitch_deck.html`) e exportação widescreen 16:9 (`slides/Apresentacao_HelpDesk_AI.pdf`).

---

## [1.0.0] — 2026-10-15

### Adicionado
* Criação do estudo de caso **NexusTech S.A.** (5 setores corporativos e matriz de criticidade).
* Protótipo inicial No Code / Low Code / Vibecode com Google Forms, Google Sheets, Make.com (prompt estático no Gemini Flash) e Gmail.
* Registro inicial das 5 etapas do cronograma em `docs/etapas_desenvolvimento.md` e slides LaTeX Beamer (`slides/main.tex`).
