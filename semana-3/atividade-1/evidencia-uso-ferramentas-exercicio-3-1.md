# Evidência de Uso das Ferramentas — Exercício 3.1 (Delivery Manager)
**Projeto:** Assistente de IA NovaTech (RAG) — DB1
**Exercício:** 3.1 — Critérios de go-live com harness de governança
**Ferramentas:** Claude (chat) — critérios e rollback · Claude Cowork — dashboard de readiness
**Data:** 19/06/2026

> **Arquivos relacionados:**
> - `exercicio-3-1-criterios-go-live-harness.md` (critérios pelas 5 camadas)
> - `exercicio-3-1-plano-rollback.md` (plano de rollback)
> - `exercicio-3-1-dashboard-readiness-go-live.html` (dashboard — artefato Cowork)

---

## Turno 1 — Contexto da fase 3

**Participante:** Forneceu a skill de avaliação do DM (cenário 3), a Foundation e o prompt padrão de avaliação; depois o Cenário-Âncora 3 completo com o enunciado oficial de 3.1 e 3.2.

**Claude:** Antes de produzir, mapeou explicitamente os alvos de score 3 e as regras de corte da skill de avaliação, e registrou os fatos novos da fase que ancoram os critérios: 12% de respostas incorretas em teste interno, respostas em texto livre sem campos obrigatórios garantidos, módulo de feedback gerado por IA que violou o AGENTS.md (logou e-mail), demo da diretoria em 2 semanas, piloto com 5 atendentes.

---

## Turno 2 — Execução do Exercício 3.1

**Claude:** Produziu os três artefatos exigidos.

**1. Critérios de go-live (Claude) — organizados pelas 5 camadas:**
- Camada 1 Tool Orchestration: ingestão dos 847 docs, context budget na orquestração, vigência (ADR-0003) com as 12 contradições como bloqueante, fallback como desejável.
- Camada 2 Verification Loops: structured output com Zod (bloqueante), verificação de fonte contra docs válidos, golden dataset com meta de aceite ≤ 5% (vs 12% atuais) como bloqueante.
- Camada 3 Context & Memory: budget da ADR-0002 (não reinventado), truncamento de histórico de 3 turnos.
- Camada 4 Guardrails + HITL: 2 guardrails determinísticos que bloqueiam (não só logam), guardrail anti-fonte-informal (caso resposta 6 do staging), e o ponto de HITL detalhado com 4 condições concretas de disparo.
- Camada 5 Observability: logging estruturado sem dado pessoal (lição do módulo que vazou e-mail), feedback no Teams, visibilidade da taxa de escalação.
- Cada critério classificado como bloqueante 🔴 ou desejável 🟡, com o princípio de separação explicitado (bloqueia o que causa dano não detectável/irreversível).

**Ponto de HITL concreto:** resposta não chega ao atendente automaticamente quando (1) baixa confiança + tema sensível (carga perigosa, sinistro, frete > R$100k, SLA contratual), (2) fonte suspeita, (3) única fonte é o FAQ informal em tema crítico, ou (4) guardrail determinístico bloqueou. Exemplo: "posso devolver carga perigosa classe 3?" com baixa confiança → supervisor valida contra POL-001 §3.2 antes do repasse.

**2. Dashboard de readiness (Claude Cowork):**
- Semáforo verde/amarelo/vermelho por critério, com responsável e data-alvo, organizado pelas 5 camadas;
- KPIs no topo (16 bloqueantes; 6 verdes, 6 amarelos, 4 vermelhos);
- Veredito explícito: go-live não autorizado com bloqueante vermelho; caminho crítico = 12 contradições (Compliance, 26/jun) + golden dataset ≤5% (QA, 30/jun);
- Entregue como HTML standalone.

**3. Plano de rollback (Claude):**
- Dois níveis (mitigar via forçar HITL; rollback total);
- Matriz de 7 gatilhos objetivos (G1-G7), cada um com nível, responsável nomeado e ação imediata — ex.: feedback negativo > 25% em 24h → DM desliga; inversão de regra de carga perigosa → Tech Lead desliga sozinho; vazamento de dado pessoal → notificar Compliance;
- Procedimento de execução em 6 passos, papéis (quem pode agir sozinho em segurança), e mensagem-modelo aos pilotos pré-redigida.

---

## Evidência dos critérios de avaliação (skill do papel)

| Critério (score 3) | Onde foi atendido |
|---|---|
| Organizado pelas 5 camadas com critério específico cada | Documento de critérios: seções 1 a 5, cada uma com tabela própria de critérios |
| Bloqueante vs desejável pragmático | Classificação 🔴/🟡 em todos os itens + princípio de separação + regra "se apertar, corta desejável, nunca bloqueante" |
| Ponto de HITL concreto | Seção dedicada: 4 condições de disparo + exemplo carga perigosa classe 3 vs POL-001 §3.2 |
| Dashboard (Cowork) verde/amarelo/vermelho, responsável, data-alvo | HTML com semáforo, coluna de responsável e data-alvo por critério |
| Plano de rollback: trigger + responsável + ação | Matriz G1-G7 com gatilho objetivo, responsável nomeado e ação imediata (não "avaliar a situação") |
| D5 — conexão com cenários 1 e 2 | ADR-0002 (context budget), ADR-0003 (vigência), golden-queries, guardrails do cenário 2, AGENTS.md (logging pino sem dado pessoal), validation gates |
