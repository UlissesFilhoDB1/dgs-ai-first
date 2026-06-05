# Histórico da Conversa com o Claude — Exercício 1.1 (Delivery Manager)
**Projeto:** Assistente de IA NovaTech (RAG) — DB1
**Ferramenta:** Claude (chat)
**Objetivo do registro:** Demonstrar o uso da ferramenta e o processo de refinamento iterativo do output, conforme exigido no entregável do Exercício 1.1.
**Data:** 05/06/2026

> **Nota:** Este arquivo reconstitui a conversa a partir do próprio histórico. O documento final refinado encontra-se em arquivo separado: `exercicio-1-1-avaliacao-riscos-novatech.md` (versão 2.0).

---

## Turno 1 — Estabelecimento do contexto

**Participante:**
> "Analise esse contexto, e após a sua análise, vamos realizar algumas atividades, tudo bem?"

**Claude:** Confirmou disponibilidade e pediu o material.

**Participante:** Enviou os arquivos do cenário:
- `anexo-a-documentacao-simulada-novatech.md` (5 documentos: POL-001, PROC-042 v1, PROC-042-v2, SLA-2024, FAQ-Atendimento)
- `anexo-b-chunks-referencia-rag.md` (chunks do pipeline de RAG + mapa pergunta→chunks + armadilhas)
- Cenário-Âncora 1 (briefing NovaTech: 1.200 funcionários, ~1.250 fontes documentais, 45 atendentes, 320 chamados/dia, meta de reduzir busca de 12 para <2 min, 3 meses de projeto, stack Microsoft/Azure)

**Claude:** Leu os dois anexos na íntegra e devolveu uma síntese estruturada do contexto, destacando:
- A natureza distinta dos 5 documentos (normativo, contratual, informal não validado, versões coexistentes);
- As 5 armadilhas pedagógicas do material: contradição entre versões (PROC-042 v1 vs v2), fonte informal tratada como oficial (FAQ-32/38), alucinação por entidade inexistente (tier Platinum), inversão de regra (devolução de carga perigosa) e pergunta sem cobertura (frete <500kg);
- O gap de governança (3 áreas atualizando documentação sem processo unificado) como causa-raiz das contradições.

---

## Turno 2 — Definição do papel

**Participante:**
> "Certo, agora vamos fazer as atividades como um Delivery Manager / Gerente de Projetos que assumiu esse projeto desse cliente, segue as atividades:"

**Claude:** Assumiu a lente de DM/GP e sinalizou que a lista de atividades não havia sido anexada, pedindo o conteúdo.

---

## Turno 3 — Prompt principal (Exercício 1.1)

**Participante:** Enviou o enunciado completo do Exercício 1.1 — Avaliação de viabilidade com fundamentos de IA, incluindo a tabela de fontes (SharePoint ~800 docs, Confluence ~400 páginas, pasta de rede ~50 planilhas), a tarefa (riscos com probabilidade/impacto/mitigação + 3 perguntas ao Tech Lead) e os critérios de avaliação.

**Claude:** Produziu a **versão 1.0** do documento "Avaliação de Riscos — Assistente de IA NovaTech", contendo:
- Sumário executivo com a tese central: a viabilidade depende da governança da base documental, não do modelo; o pior cenário é o assistente "funcionar com confiança aparente e responder errado";
- 5 riscos ancorados no cenário:
  - **R1** Alucinação (gatilhos: tier Platinum, frete <500kg sem cobertura);
  - **R2** Contradição PROC-042 v1 vs v2 (resposta híbrida misturando multiplicadores e prazos);
  - **R3** Dependência da qualidade das fontes (FAQ informal como única fonte de temas críticos; parsing de XLSX);
  - **R4** Expectativa da diretoria vs realidade da tecnologia (meta <2 min; suposição implícita de 100% de acerto);
  - **R5** Context rot (~1.250 fontes; orçamento de atenção limitado; top-k inflado);
- Probabilidade, impacto e mitigações acionáveis para cada risco (grounding com citação de fonte, golden dataset de regressão, metadados de vigência e autoridade, curadoria pré-ingestão, acordo de métricas, top-k com reranking, avaliação de retrieval separada da geração);
- Tabela consolidada;
- 3 perguntas ao Tech Lead (pipeline de ingestão/chunking/metadados; avaliação de qualidade com golden dataset e critério de "pronto"; re-indexação incremental e operação contínua);
- Recomendação de viabilidade condicionada (discovery com piloto de ingestão, governança com dono nomeado, métricas acordadas).

Ao final, o Claude propôs proativamente uma rodada de refinamento, sugerindo ângulos: calibragem das probabilidades, riscos ausentes (adoção, segurança/permissionamento, custo de tokens) e tom da recomendação.

---

## Turno 4 — Refinamento (iteração 1)

**Participante:**
> "Certo, agora me ajude a refinar esses riscos"

**Claude:** Fez uma revisão crítica do próprio rascunho, apontando 3 fragilidades:
1. Probabilidades sem justificativa explícita ("alta com base em quê?");
2. Impactos sem quantificação com os números do briefing;
3. Mitigações sem dono e fase.

E propôs, risco a risco:
- **R1:** justificar "Alta" (gatilhos já confirmados na base) e quantificar (~9-10 respostas erradas/dia a 5% de alucinação sobre 192 consultas/dia); golden dataset como gatilho de bloqueio do go-live;
- **R2:** elevar a leitura para "quase certa" (similaridade semântica máxima entre v1 e v2 torna a colisão estrutural); quantificar (Sudeste 1.0→1.1 = 10% no frete); curadoria pré-ingestão como atividade bloqueante;
- **R3:** reposicionar como decisão de produto com trade-off explícito (incluir FAQ rotulado vs excluir vs formalizar via Compliance);
- **R4:** subir de Média para Alta (expectativa fixada antes de qualquer baseline) e criar o artefato "acordo de definição de sucesso" assinado no kickoff;
- **R5:** desdobrar em R5a (contexto inflado por consulta — mitigável por engenharia) e R5b (degradação do índice ao longo dos meses — risco que aparece no mês 4);
- **Novos riscos propostos:** R6 (adoção pelo time de atendimento — 2-3 erros visíveis matam a confiança e o ROI) e R7 (permissionamento/ACLs do SharePoint — security trimming desde a arquitetura).

O Claude encerrou devolvendo a decisão ao participante: aplicar tudo (incluindo R6 e R7) ou manter o escopo mínimo de 5 riscos.

---

## Turno 5 — Decisão e versão final

**Participante:**
> "Pode incluir o R6 e R7, por gentileza"

**Claude:** Gerou a **versão 2.0** do documento, com:
- Probabilidades justificadas em todos os riscos;
- Impactos quantificados (erros/dia, % no frete, penalidades de SLA);
- Mitigações com dono e fase, e dois gatilhos de bloqueio explícitos;
- R5 desdobrado (R5a/R5b), R3 com trade-off de decisão, R4 elevado a Alta;
- R6 (adoção) e R7 (security trimming) incluídos;
- Tabela consolidada com coluna de dono e leitura executiva agrupando riscos por natureza (técnicos com raiz organizacional / gestão e mudança / arquitetura);
- Pergunta 3 ao Tech Lead ampliada para cobrir security trimming;
- Recomendação final expandida para 5 condições (a-e).

---

## Evidência dos critérios de avaliação

| Critério do exercício | Onde foi atendido |
|---|---|
| Riscos não genéricos, com compreensão real de LLMs | R1-R5 ancorados em artefatos do cenário (PROC-042 v1/v2, tier Platinum, FAQ-32/38, frete <500kg) |
| Ao menos um risco de contexto/volume | R5 (context rot, orçamento de atenção, ~1.250 fontes, desdobrado em consulta × tempo) |
| Mitigações acionáveis | Golden dataset bloqueante, metadados de vigência/autoridade, curadoria pré-ingestão, acordo de sucesso assinado, security trimming arquitetural — todas com dono e fase |
| Perguntas ao Tech Lead focadas no pipeline de dados | As 3 perguntas tratam de ingestão/chunking/metadados, avaliação de retrieval e operação contínua — não do modelo |
| Refinamento iterativo (não aceitação acrítica) | Turnos 3→4→5: rascunho → crítica estruturada do próprio output → decisão do participante → versão 2.0 |
