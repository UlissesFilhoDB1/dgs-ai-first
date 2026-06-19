# Workflow de Desenvolvimento AI First — NovaTech Assistant
**Projeto:** Assistente de IA NovaTech (RAG) — DB1
**Autor:** Delivery Manager
**Fase:** Estruturação do Trabalho
**Data:** 12/06/2026

---

## 1. Princípio

No modelo AI First, a IA **gera** e o humano **valida e decide**. A velocidade vem de deixar os agentes produzirem rascunhos (specs, planos, tasks, código, testes); a segurança vem de checkpoints humanos obrigatórios (validation gates) entre cada etapa em que um erro propagado custaria caro. Cada papel usa a ferramenta de IA adequada à sua atividade — não há uma ferramenta única para todos.

O ciclo de vida de cada módulo segue 6 etapas: **Spec → Plan → Tasks → Implement → Review → Deploy**. Entre as etapas críticas há 4 gates (detalhados no checklist anexo).

---

## 2. Ferramentas por papel

| Papel | Ferramenta(s) de IA | Uso principal |
|-------|---------------------|---------------|
| Product Specialist | Claude (chat) · Claude Cowork · Claude Design | Redigir requirements, guardrails de produto, protótipos de UI do painel/cards |
| Tech Lead | Claude (chat) · GitHub Copilot | Escrever plans técnicos, revisar tasks e código, manter AGENTS.md e ADRs |
| Desenvolvedor (pleno e sênior) | GitHub Copilot · Claude (chat) | Gerar tasks a partir do plan, implementar código, escrever testes unitários |
| QA | Claude (chat) · Claude Cowork | Especificar plano de testes, gerar cenários, validar cobertura |
| Delivery Manager | Claude (chat) · Claude Cowork | Workflow, governança de specs, boards de tracking, artefatos de gestão |

Ferramentas de infraestrutura (não-IA): **Azure DevOps** (boards e tracking de tasks/issues) e **GitHub** (repositório, PRs, CI/CD).

---

## 3. Fluxo por etapa — quem faz, com qual ferramenta, e o gate de saída

### Etapa 1 — Spec (requirements.md)
- **Responsável:** Product Specialist
- **Ferramenta:** Claude (chat) para redigir e refinar os requisitos; Claude Design quando o módulo tem UI (painel web, Adaptive Cards do Teams).
- **Produto:** `specs/<modulo>/requirements.md` — o *quê*, com critérios de aceite verificáveis.
- **Gate de saída → Gate 1 (Spec → Plan).**

### Etapa 2 — Plan (plan.md)
- **Responsável:** Tech Lead
- **Ferramenta:** Claude (chat) para estruturar a abordagem técnica, ancorada nos ADRs (GPT-4o, context budget, vigência de documentos).
- **Produto:** `specs/<modulo>/plan.md` — o *como*: componentes, contratos, decisões técnicas, riscos.
- **Gate de saída → Gate 2 entra na próxima transição.**

### Etapa 3 — Tasks (tasks.md)
- **Responsável:** Desenvolvedor (com apoio do Copilot)
- **Ferramenta:** GitHub Copilot para decompor o plan em unidades atômicas; Claude (chat) para revisar a decomposição.
- **Produto:** `specs/<modulo>/tasks.md` — unidades executáveis, cada uma com critério de pronto.
- **Gate de saída → Gate 2 (Tasks → Implement).**

### Etapa 4 — Implement (código)
- **Responsável:** Desenvolvedores
- **Ferramenta:** GitHub Copilot como agente de implementação, guiado pelo AGENTS.md e pelas skills do projeto (`/skills/`).
- **Produto:** código em `/src/...` + testes unitários, em branch e PR.
- **Gate de saída → Gate 3 (Code → Merge).**

### Etapa 5 — Review (PR + testes)
- **Responsável:** Tech Lead (code review) + QA (validação de testes)
- **Ferramenta:** Copilot para pré-review automatizado; Claude/Cowork (QA) para checar suficiência de cenários.
- **Produto:** PR aprovado, suíte de testes verde, cobertura validada.
- **Gate de saída → Gate 4 (Tests → Deploy).**

### Etapa 6 — Deploy
- **Responsável:** Tech Lead (aprova) · CI/CD do GitHub (executa)
- **Ferramenta:** pipelines `ci.yml` / `cd.yml`.
- **Produto:** módulo em staging/produção.

---

## 4. Mapa visual do fluxo

```
        SPEC            PLAN           TASKS         IMPLEMENT        REVIEW          DEPLOY
   ┌───────────┐   ┌───────────┐   ┌───────────┐   ┌───────────┐   ┌───────────┐   ┌──────────┐
   │ Product   │   │ Tech Lead │   │ Dev +     │   │ Devs +    │   │ TL (code) │   │ TL aprova│
   │ Spec +    │   │ + Claude  │   │ Copilot   │   │ Copilot   │   │ QA (test) │   │ CI/CD    │
   │ Claude/   │   │           │   │           │   │ + skills  │   │ + Copilot │   │ executa  │
   │ Design    │   │           │   │           │   │ +AGENTS.md│   │           │   │          │
   └─────┬─────┘   └─────┬─────┘   └─────┬─────┘   └─────┬─────┘   └─────┬─────┘   └────┬─────┘
         │  ◆ GATE 1     │               │  ◆ GATE 2     │               │  ◆ GATE 3     │  ◆ GATE 4
         │  PS aprova    │               │  TL aprova    │               │  TL: 1 approval│  QA+TL
         │  requirements │               │  tasks        │               │  no PR         │  validam
         ▼               ▼               ▼               ▼               ▼               ▼
   IA gera rascunho → humano valida → IA gera rascunho → humano valida → IA gera → humano valida
```

A leitura horizontal mostra o equilíbrio do modelo: **toda etapa em que a IA gera é seguida por um ponto em que um humano nomeado valida antes de a saída virar insumo da etapa seguinte.** Nenhum artefato gerado por IA avança sozinho.

---

## 5. Como o fluxo equilibra velocidade e segurança

- **Velocidade:** os agentes produzem os rascunhos pesados (decomposição de tasks, código repetitivo, primeira versão de testes), e as skills do projeto garantem que o output já saia no padrão — menos retrabalho.
- **Segurança:** os 4 gates concentram a atenção humana exatamente onde o erro é caro: requisito ambíguo (Gate 1), task mal decomposta (Gate 2), código inseguro ou fora do padrão (Gate 3), teste insuficiente antes de produção (Gate 4).
- **Rastreabilidade:** cada gate registra aprovação no Azure DevOps / PR do GitHub, ligando o artefato à pessoa que o validou.
