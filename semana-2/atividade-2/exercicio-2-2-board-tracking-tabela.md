# Board de Tracking de Specs (SDD) — versão tabela
**Projeto:** NovaTech Assistant — DB1 · Fase de Estruturação
**Uso:** importável em Azure DevOps / qualquer ferramenta de kanban. Versão visual em `exercicio-2-2-board-tracking-specs.html`.

Estados do ciclo: **Rascunho → Em Revisão → Aprovada → Em Implementação → Validada**

## Visão por módulo e artefato

| Módulo | requirements.md (PS) | plan.md (TL) | tasks.md (Dev+Copilot) |
|--------|----------------------|--------------|------------------------|
| pipeline-ingestao | Aprovada | Aprovada | Em Implementação |
| query-endpoint | Aprovada | Em Revisão | — (Rascunho) |
| feedback-api | Rascunho | — | — |
| teams-bot | Rascunho | — | — |
| painel-web | Rascunho | — | — |

> O estado acima é um ponto de partida ilustrativo do início da fase. O `pipeline-ingestao` está mais à frente por ser o módulo-base do qual os demais dependem; `query-endpoint` é o segundo na fila.

## Visão por coluna (kanban)

| Coluna | Itens |
|--------|-------|
| Rascunho | feedback-api (R), teams-bot (R), painel-web (R) |
| Em Revisão | query-endpoint (P) |
| Aprovada | query-endpoint (R) |
| Em Implementação | pipeline-ingestao (R+P+T) |
| Validada | — |

## Campos sugeridos para cada item no board real
- Módulo · Artefato (R/P/T) · Status · Versão · Autor · Aprovador · Data da última transição · Link para o PR · CRs abertos (se houver)
