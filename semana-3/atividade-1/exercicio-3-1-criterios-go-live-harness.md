# Critérios de Go-Live — NovaTech Assistant
**Projeto:** Assistente de IA NovaTech (RAG) — DB1
**Autor:** Delivery Manager
**Fase:** Governança e Validação · Demo da diretoria em 2 semanas
**Data:** 19/06/2026

---

## Como ler este documento

Os critérios estão organizados pelas **5 camadas do harness** de governança. Cada critério é classificado como:

- **🔴 Bloqueante** — sem isso, **não vai ao ar**. O risco de ir sem é alto demais (resposta errada ao cliente, vazamento, indisponibilidade silenciosa).
- **🟡 Desejável** — melhora o sistema, mas o go-live (piloto controlado) pode acontecer sem. Vira backlog pós-go-live com data.

O princípio que separa um do outro: **bloqueia o que, se falhar, produz dano não detectável ou irreversível para o cliente/atendente.** Não bloqueia o que é melhoria incremental ou o que já tem mitigação manual viável no piloto.

> Contexto que pesa na régua: testes internos mostraram **12% de respostas incorretas** (alucinação, documento desatualizado, chunk errado) e as respostas saem em **texto livre sem garantia de campos obrigatórios**. Esses dois fatos definem vários bloqueantes abaixo.

---

## Camada 1 — Tool Orchestration (coordenação de ingestão, retrieval e geração)

| Critério | Classe | Estado-alvo para go-live |
|----------|--------|--------------------------|
| Pipeline de ingestão processa os 847 documentos válidos e reindexa sob demanda | 🔴 Bloqueante | Já implementado; precisa de execução de reindex documentada |
| Query endpoint orquestra retrieval → geração com o context budget da ADR-0002 (~8K p/ chunks, 5 chunks, histórico de 3 turnos) | 🔴 Bloqueante | Implementado; validar que respeita o budget sob carga |
| Vigência de documentos aplicada no retrieval (ADR-0003): versão mais recente priorizada, obsoletos marcados | 🔴 Bloqueante | As 12 contradições pendentes no Compliance precisam estar resolvidas OU os documentos afetados excluídos do índice do piloto |
| Fallback de orquestração: se o Azure AI Search ou o Azure OpenAI falham, retorna mensagem de indisponibilidade (não erro cru) | 🟡 Desejável | Tratamento gracioso pode ser refinado pós-go-live |

## Camada 2 — Verification Loops (verificação automática de outputs)

| Critério | Classe | Estado-alvo para go-live |
|----------|--------|--------------------------|
| **Structured output** obrigatório: resposta validada contra schema Zod `{ answer, source_document, confidence_score }` antes de seguir | 🔴 Bloqueante | Resolve diretamente o problema de campos faltantes — sem isso a resposta sem fonte chega ao atendente |
| Verificação de fonte: `source_document` precisa existir na lista de docs válidos (POL-001, PROC-042, PROC-042-v2, SLA-2024, FAQ-Atendimento); fonte inexistente → resposta marcada suspeita | 🔴 Bloqueante | Pega alucinação de fonte (caso resposta 4 do staging) |
| Regressão contra `golden-queries.json`: a suíte roda sem regressão na taxa de acerto a cada mudança de prompt/índice | 🔴 Bloqueante | É o instrumento que mede se os 12% melhoraram; meta de aceite definida abaixo |
| Verificação de coerência entre `confidence_score` e presença de fonte (baixa confiança sem fonte → não exibe como resposta) | 🟡 Desejável | Reforço; o HITL já cobre o caso crítico |

**Meta de aceite (bloqueante):** taxa de respostas incorretas no golden dataset **≤ 5%** (de 100 perguntas reais validadas pelos especialistas). Os 12% atuais são reprovação automática de go-live.

## Camada 3 — Context & Memory (manutenção de contexto entre interações)

| Critério | Classe | Estado-alvo para go-live |
|----------|--------|--------------------------|
| Context budget da ADR-0002 respeitado: ~4K system + ~8K chunks (5 × ~1.500) + pergunta + histórico de 3 turnos | 🔴 Bloqueante | Não reinventar; é decisão arquitetural fechada. Validar que não estoura sob conversas longas |
| Truncamento de histórico além de 3 turnos sem quebrar a resposta | 🔴 Bloqueante | Previne context rot (risco mapeado no cenário 1) |
| Memória de sessão preserva contexto do atendente dentro de um chamado | 🟡 Desejável | Útil, mas o piloto funciona com perguntas atômicas |

## Camada 4 — Guardrails (limites + human-in-the-loop)

| Critério | Classe | Estado-alvo para go-live |
|----------|--------|--------------------------|
| Guardrail determinístico 1: resposta sem `source_document` é rejeitada e substituída por mensagem padrão segura (bloqueia, não só loga) | 🔴 Bloqueante | Guardrail do cenário 2; precisa **bloquear** de fato |
| Guardrail determinístico 2: "carga perigosa" + "devolução" sem a negativa → resposta bloqueada (não inverter a regra da POL-001) | 🔴 Bloqueante | Inversão de regra em tema sensível é dano grave |
| Guardrail anti-fonte-informal: respostas sobre temas críticos (carga perigosa, sinistro) cuja única fonte é o FAQ informal são sinalizadas para revisão | 🔴 Bloqueante | Pega o caso resposta 6 do staging (FAQ-32 como fonte de carga perigosa) |
| **Ponto de HITL (ver detalhe abaixo)** | 🔴 Bloqueante | Núcleo da governança desta fase |
| Guardrails de produto (DEVE / NÃO DEVE / QUANDO EM DÚVIDA) carregados no system prompt versionado | 🔴 Bloqueante | Já formalizados no cenário 2 |
| Mensagens padrão de abstenção com tom amigável e orientação de escalonamento | 🟡 Desejável | Conteúdo pode ser refinado pós-go-live |

### Ponto de Human-in-the-Loop (HITL) — obrigatório para go-live

**Regra:** uma resposta **não chega ao atendente automaticamente** (vai para fila de revisão de um atendente sênior/supervisor) quando **qualquer** das condições ocorre:

1. `confidence_score` = **baixa** E a pergunta toca **tema sensível** (carga perigosa, sinistro/avaria, valores de frete acima de R$ 100.000, prazos contratuais de SLA).