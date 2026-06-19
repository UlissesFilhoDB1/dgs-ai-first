# Evidência de Uso das Ferramentas — Exercício 2.1 (Delivery Manager)
**Projeto:** Assistente de IA NovaTech (RAG) — DB1
**Exercício:** 2.1 — Definição do workflow de desenvolvimento AI First
**Ferramentas:** Claude (chat) — fluxo de trabalho · Claude Cowork — checklist de validation gates
**Data:** 12/06/2026

> **Arquivos relacionados:**
> - `exercicio-2-1-workflow-ai-first-novatech.md` (fluxo de trabalho)
> - `exercicio-2-1-checklist-validation-gates.md` (checklist de gates — artefato Cowork)

---

## Turno 1 — Carga de contexto da fase 2

**Participante:** Forneceu o Cenário-Âncora 2 (fase de Estruturação do Trabalho), com as decisões herdadas do cenário 1 (Azure OpenAI GPT-4o / ADR-0001; context budget / ADR-0002; vigência de documentos / ADR-0003; chunking de tabelas / ADR-0004; base de 847 docs válidos, 63 descartados, 12 contradições pendentes no Compliance; stack TypeScript/React/Bicep; time de 7 pessoas).

**Claude:** Sintetizou a transição de fase, destacando que as decisões viraram restrições e que as mitigações dos riscos da semana 1 se materializaram em ADRs (ex.: context budget responde ao risco de context rot; vigência por metadado responde à contradição PROC-042 v1/v2).

---

## Turno 2 — Anexos C e D

**Participante:** Enviou o pacote `Prática_2_-_V2.zip` (anexos A, B, C, D + arquivo-mestre de exercícios).

**Claude:** Extraiu e leu o Anexo C, registrando a restrição-chave da fase: **ambiente totalmente local, sem remoto/GitHub/Azure**; MCP via reference servers gratuitos (filesystem, git, memory, everything); infra Bicep como estado narrativo. Mapeou os 3 exercícios do Delivery Manager (2.1, 2.2, 2.3) e leu seus enunciados na íntegra. Propôs método incremental (um exercício por vez, com pacote de entregáveis).

---

## Turno 3 — Execução do Exercício 2.1

**Participante:** "Vamos para o 2.1".

**Claude:** Produziu os dois artefatos exigidos:

**1. Fluxo de trabalho (Claude):**
- Princípio do modelo AI First (IA gera, humano valida e decide);
- Tabela de ferramentas por papel, distinguindo Copilot (devs/Tech Lead), Cowork (gestão/QA), Design (Product) e Claude chat (todos);
- Detalhamento das 6 etapas (Spec → Plan → Tasks → Implement → Review → Deploy) com responsável, ferramenta e gate de saída;
- Mapa visual do fluxo com os 4 gates posicionados entre as etapas;
- Seção explícita de equilíbrio velocidade × segurança × rastreabilidade.

**2. Checklist de validation gates (Claude Cowork):**
- Os 4 gates obrigatórios (Spec→Plan, Tasks→Implement, Code→Merge, Tests→Deploy);
- Para cada um: quem aprova, prazo, critérios objetivos de aprovação (checkboxes) e o que acontece se reprovar;
- Critérios ancorados no projeto NovaTech (citação de fonte, abstenção, security trimming, golden-queries, contradições do Compliance, chunking de tabelas);
- Tabela-resumo dos 4 gates + regra de não-afrouxamento por pressão de prazo.

---

## Evidência dos critérios de avaliação

| Critério do exercício | Onde foi atendido |
|---|---|
| Diferentes papéis usam diferentes ferramentas | Tabela "Ferramentas por papel" + atribuição em cada etapa (Copilot p/ devs, Cowork p/ gestão e QA, Design p/ Product) |
| Gates específicos e executáveis (não genéricos) | Cada gate tem checklist de critérios objetivos verificáveis, não "revisar antes de continuar" |
| Critérios concretos de aprovação por gate | Ex.: Gate 1 exige "critério de aceite verificável por requisito"; Gate 4 exige cobertura dos cenários de falha + golden-queries sem regressão |
| Fluxo equilibra velocidade (IA gera) e segurança (humano valida) | Mapa visual mostra "IA gera rascunho → humano valida" em cada transição; seção 5 do fluxo torna o equilíbrio explícito |
| Evidência do uso das ferramentas | Este registro: contexto → divisão IA-gera/humano-valida → artefato de fluxo (Claude) + checklist operacional (Cowork) |
