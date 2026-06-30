# Plano de Observabilidade e Melhoria Contínua — NovaTech Assistant
**Projeto:** Assistente de IA NovaTech (RAG) — DB1
**Autor:** Delivery Manager
**Fase:** Governança e Validação (pós-go-live)
**Data:** 19/06/2026

---

## 1. Objetivo

Garantir que, depois do go-live, o time **saiba quando algo dá errado antes do cliente perceber** e tenha um processo definido para corrigir. Observabilidade aqui não é só "ver gráficos" — é fechar o ciclo entre o que o assistente faz em produção e a melhoria efetiva do produto. O instrumento de comunicação com a NovaTech é o relatório semanal (template anexo), que responde à pergunta da diretoria: *"o assistente está melhorando ou piorando?"*

---

## 2. Métricas — as 4 dimensões

Monitoramos as quatro dimensões em conjunto, porque cada uma sozinha engana: latência ótima com feedback negativo alto é um sistema rápido e errado; feedback bom com muitas perguntas sem resposta é um sistema querido mas incompleto.

### Dimensão A — Uso (o assistente está sendo adotado?)
| Métrica | Por que importa | Frequência |
|---------|-----------------|------------|
| Perguntas por dia (total e por atendente) | Adoção real; queda pode indicar perda de confiança (risco R6 do cenário 1) | Diária |
| Atendentes ativos / atendentes habilitados | No piloto: 5; mede se todos usam ou só alguns | Diária |
| Tempo médio de resposta percebido pelo atendente | Liga à meta de negócio (de 12 min para < 2 min) | Semanal |
| Perguntas por tema (devolução, frete, SLA, prazo) | Mostra onde o assistente entrega mais valor | Semanal |

### Dimensão B — Qualidade (as respostas são boas?)
| Métrica | Por que importa | Frequência |
|---------|-----------------|------------|
| % de feedback negativo (👎 / total avaliado) | Sinal primário de qualidade percebida | Diária |
| % de escalações ao supervisor (HITL + manual) | Quanto o assistente resolve sozinho vs. precisa de humano | Diária |
| Taxa de acerto no golden dataset (regressão) | Qualidade objetiva, medida contra gabarito | A cada release |
| % de respostas com abstenção correta ("não encontrei") | Saudável: indica que não está alucinando para preencher lacuna | Semanal |

### Dimensão C — Técnicas (o sistema está de pé?)
| Métrica | Por que importa | Frequência |
|---------|-----------------|------------|
| Latência p50 / p95 | Experiência do atendente; p95 pega os piores casos | Tempo real |
| Taxa de erro técnico (5xx, timeouts) | Saúde da API e dos serviços Azure | Tempo real |
| Disponibilidade (uptime) do bot e do endpoint | SLA interno do piloto | Tempo real |
| Custo de tokens por dia | Sustentabilidade financeira (192 consultas/dia projetadas) | Diária |

### Dimensão D — Conteúdo (a base de conhecimento é adequada?)
| Métrica | Por que importa | Frequência |
|---------|-----------------|------------|
| Documentos mais consultados | Onde concentrar curadoria e qualidade | Semanal |
| Perguntas sem resposta / sem cobertura | **A métrica mais valiosa para evolução**: revela gaps na base (ex.: frete < 500kg, carga danificada) | Diária |
| Respostas marcadas suspeitas (fonte fora da lista) | Sinaliza alucinação de fonte ou documento mal indexado | Diária |
| Perguntas que dispararam guardrail/HITL por tema | Mostra onde o conteúdo é sensível e precisa de formalização (ex.: FAQ informal) | Semanal |

---

## 3. Alertas com thresholds concretos

Alertas têm número, janela de tempo, destinatário e ação — não "monitorar se piora". São o elo entre observabilidade e o rollback do 3.1 (gatilhos G1, G2, G5).

| # | Condição (threshold + janela) | Notifica | Ação disparada |
|---|-------------------------------|----------|----------------|
| AL-1 | Feedback negativo **> 15%** em 24h | Tech Lead + DM (Teams) | Abrir investigação no mesmo dia; classificar as respostas 👎 por tema. Liga ao gatilho G2 do rollback (15–25% → forçar HITL) |
| AL-2 | Perguntas sem resposta **> 20%** em 24h | Product Specialist + DM | Revisar gaps de conteúdo; candidatas a novo documento ou reindexação |
| AL-3 | Latência p95 **> 8s** sustentada por 1h | Tech Lead | Investigar Azure AI Search / OpenAI; preparar mitigação (liga ao G5) |
| AL-4 | Respostas marcadas suspeitas **> 5%** em 24h | Tech Lead + QA | Auditar fontes; possível problema de índice ou prompt |
| AL-5 | Qualquer resposta que **inverta regra de tema sensível** (carga perigosa) | Tech Lead (imediato) | Rollback nível 2 — gatilho G3. Alerta crítico, não esperar janela |

Os thresholds são pontos de partida calibrados para o piloto e serão ajustados com a linha de base real das primeiras 2 semanas (um threshold só é bom depois de calibrado contra o normal observado).

---

## 4. Feedback loop: do atendente até a correção no assistente

O feedback só tem valor se virar mudança. O ciclo abaixo é fechado — coletar sem agir é desperdício (red flag da avaliação).

```
1. CAPTURA        Atendente dá 👎 no Teams + comentário opcional ("disse que dava
                  pra devolver carga perigosa, mas não dá").
        │
2. TRIAGEM        Diária/semanal: DM + QA classificam os 👎 por tipo de erro
   (semanal)      (alucinação, fonte informal, doc desatualizado, chunk errado,
                  idioma, assunção indevida — os mesmos tipos do staging).
        │
3. DIAGNÓSTICO    Para cada padrão, identificar a causa-raiz e a alavanca de correção:
                  ┌─ Falta de documento na base → CONTEÚDO (criar/formalizar doc;
                  │   ex.: política de carga danificada, hoje só no FAQ)
                  ├─ Documento existe mas desatualizado/contraditório → CURADORIA
                  │   (resolver vigência; liga às 12 contradições do Compliance)
                  ├─ Documento existe e está certo, mas não foi recuperado → PIPELINE
                  │   (reindexação, ajuste de chunking — ex.: tabelas, ADR-0004)
                  └─ Recuperou certo mas respondeu mal → PROMPT
                      (ajuste do system prompt, sempre versionado)
        │
4. CORREÇÃO       Aplica a alavanca. Toda mudança de prompt entra em
                  prompts/prompt-changelog.md (data, autor, motivo, resultado esperado).
        │
5. VALIDAÇÃO      Antes de ir a produção: regression contra golden-queries.json
                  (não regrediu?) + guardrails do cenário 2 continuam respeitados.
                  Passa pelos validation gates (cenário 2).
        │
6. FECHAMENTO     A correção entra em produção; o atendente que reportou é avisado
                  ("seu feedback sobre carga perigosa foi corrigido"). Fecha o loop
                  de confiança — o atendente vê que reportar vale a pena (mitiga R6).
        │
        └──────► volta ao monitoramento (a métrica do erro corrigido cai?)
```

**Quem é dono de quê:** DM conduz a triagem e o relatório; QA valida a regressão; Product Specialist decide alavancas de conteúdo/prompt; Tech Lead aprova mudanças que tocam pipeline ou prompt (ponto de HITL de evolução — ver harness de produto do PS no 3.2 do papel dele).

---

## 5. Cadência

- **Diário (automático):** alertas AL-1 a AL-5; painel técnico (latência, erro, uptime).
- **Semanal (DM):** triagem dos 👎, atualização do relatório à NovaTech (template anexo), decisão sobre as correções da semana.
- **A cada release:** regressão no golden dataset; entrada no prompt-changelog.
- **Quinzenal:** recalibração de thresholds com a linha de base observada.

---

## 6. Como isso responde à pergunta da diretoria

A NovaTech quer saber se o assistente "melhora ou piora". A resposta vem de três métricas-tendência acompanhadas semana a semana: **% de feedback negativo** (caindo = melhor), **taxa de acerto no golden dataset** (subindo = melhor) e **% de perguntas sem resposta** (caindo = base mais completa). O relatório semanal mostra essas três com seta de tendência — é o instrumento que transforma observabilidade técnica em decisão de gestão.