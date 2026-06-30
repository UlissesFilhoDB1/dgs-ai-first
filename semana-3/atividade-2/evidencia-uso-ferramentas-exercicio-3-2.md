# Evidência de Uso das Ferramentas — Exercício 3.2 (Delivery Manager)
**Projeto:** Assistente de IA NovaTech (RAG) — DB1
**Exercício:** 3.2 — Plano de observabilidade e melhoria contínua
**Ferramentas:** Claude (chat) — plano de observabilidade · Claude Cowork — template de relatório semanal
**Data:** 19/06/2026

> **Arquivos relacionados:**
> - `exercicio-3-2-plano-observabilidade.md` (plano completo)
> - `exercicio-3-2-template-relatorio-semanal.html` (template de relatório — artefato Cowork)

---

## Turno 1 — Execução do Exercício 3.2

**Participante:** Solicitou o Exercício 3.2, com os inputs: as 4 dimensões de observabilidade (uso, qualidade, técnicas, conteúdo) e a expectativa da NovaTech ("relatório semanal que mostre se o assistente está melhorando ou piorando").

**Claude:** Produziu os dois artefatos exigidos.

**1. Plano de observabilidade (Claude):**
- Abertura ligando observabilidade a fechar o ciclo entre produção e melhoria (não só "ver gráficos");
- As 4 dimensões de métricas, cada uma com tabela de métrica × por que importa × frequência:
  - A — Uso: perguntas/dia, atendentes ativos, tempo de resposta, perguntas por tema;
  - B — Qualidade: % feedback negativo, % escalações, acerto no golden dataset, % abstenção correta;
  - C — Técnicas: latência p50/p95, taxa de erro, uptime, custo de tokens;
  - D — Conteúdo: docs mais consultados, perguntas sem resposta, respostas suspeitas, guardrail/HITL por tema;
  - Com a observação de que as 4 dimensões se leem em conjunto (latência ótima + feedback ruim = rápido e errado);
- 5 alertas com threshold + janela + destinatário + ação (AL-1 a AL-5), explicitamente ligados aos gatilhos de rollback do 3.1 (G2, G3, G5);
- Feedback loop completo em 6 passos (captura → triagem → diagnóstico → correção → validação → fechamento), com as 4 alavancas de correção mapeadas (conteúdo, curadoria, pipeline, prompt) e donos por etapa;
- Cadência (diária/semanal/por release/quinzenal) e seção final conectando as 3 métricas-tendência à pergunta da diretoria.

**2. Template de relatório semanal (Claude Cowork):**
- 1 página, leitura executiva em 2 min;
- Veredito da semana em destaque (melhorando/piorando) + 4 KPIs principais com seta de tendência vs. semana anterior;
- Blocos de uso & adoção, saúde técnica, ações planejadas da próxima semana e alertas disparados;
- Ações ancoradas no projeto (formalizar carga danificada hoje no FAQ; reindexar tabelas de frete; resolver contradições do Compliance);
- Entregue como HTML standalone.

---

## Decisões de pensamento crítico (D4)

- **As 4 dimensões se complementam:** o plano argumenta por que nenhuma isolada basta — evita a red flag de "apenas técnicas (latência, uptime)".
- **Perguntas sem resposta como métrica mais valiosa:** destacada como a alavanca de evolução de conteúdo (gaps reais como frete < 500kg e carga danificada, vindos das armadilhas do Anexo B).
- **Feedback loop fecha de fato:** o passo 6 (avisar o atendente que reportou) é deliberado — fecha o loop de confiança e mitiga o risco de adoção (R6 do cenário 1), evitando a red flag de "feedback coletado sem ação".
- **Thresholds calibráveis:** o plano afirma que os números são ponto de partida e serão recalibrados com a linha de base real — postura crítica sobre o próprio instrumento.

---

## Evidência dos critérios de avaliação (skill do papel)

| Critério (score 3) | Onde foi atendido |
|---|---|
| 4 dimensões de métricas — todas presentes | Seção 2: A Uso, B Qualidade, C Técnicas, D Conteúdo, cada uma com tabela própria |
| Alertas com thresholds concretos | Seção 3: AL-1 a AL-5 com número + janela + destinatário + ação (ex.: feedback negativo > 15% em 24h) |
| Feedback loop completo (atendente → investigação → correção) | Seção 4: ciclo de 6 passos com 4 alavancas de correção e donos; fechamento avisa o atendente |
| Template (Cowork) executivo entende em 2 min, mostra tendência | HTML de 1 página: veredito + 4 KPIs com seta melhorou/piorou + ações |
| D5 — conexão com cenários 1 e 2 | golden dataset, ADR-0004 (chunking de tabelas), 12 contradições do Compliance, guardrails do cenário 2, prompt-changelog/validation gates, risco R6 de adoção |
