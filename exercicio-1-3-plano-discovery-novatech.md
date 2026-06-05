# Plano de Discovery — Assistente de IA NovaTech
**Modelo:** AI First SDLC — fase de Intent (agentes de IA) alimentando o Discovery humano
**Autor:** Delivery Manager — DB1
**Duração:** 2 semanas (10 dias úteis)
**Data:** 05/06/2026

---

## 1. Princípio organizador

No modelo AI First, o discovery não começa com entrevistas — começa com **agentes de IA pré-analisando a base documental** para que os humanos cheguem às entrevistas com hipóteses, evidências e perguntas específicas, em vez de descobrirem os problemas "ao vivo". A divisão de trabalho segue uma regra simples:

- **Agentes fazem o que IA faz bem em escala:** catalogar, comparar, agrupar, detectar duplicatas e contradições, resumir — sobre ~1.250 fontes, algo inviável para humanos em 2 semanas.
- **Humanos fazem o que IA não faz:** validar com quem vive o processo, decidir qual versão de documento vale, priorizar escopo, negociar governança e assumir compromissos.

O produto da fase de Intent é o **Dossiê de Intent**: um mapa priorizado de fontes, dependências e gaps que se torna o insumo de todas as atividades humanas da semana 2.

---

## 2. Fase de Intent — atividades dos agentes de IA (dias 1-3)

| # | Atividade do agente | O que produz | Por que IA faz bem |
|---|--------------------|--------------|--------------------|
| A1 | **Inventário automatizado** das 3 fontes: ~800 docs do SharePoint, ~400 páginas do Confluence, ~50 planilhas da pasta de rede | Catálogo com metadados por item: título, tipo, área responsável, data de criação/atualização, formato | Catalogação em escala é trabalho mecânico de leitura |
| A2 | **Detecção de duplicatas e versões coexistentes** (similaridade semântica entre documentos de mesmo tema) | Relatório de colisões — ex.: PROC-042 v1 × v2 coexistindo sem marcação de obsolescência | Comparação par a par de 1.250 fontes é impraticável manualmente |
| A3 | **Análise de contradições de conteúdo** entre documentos relacionados (valores, prazos, regras divergentes) | Lista de conflitos com trecho lado a lado — ex.: multiplicador Sudeste 1.0 × 1.1; prazo +2 × +3 dias | IA compara regras específicas em texto com alta cobertura |
| A4 | **Clusterização temática + mapa de cobertura**, cruzando os temas da documentação com a distribuição real de dúvidas do atendimento (prazos 35%, frete 25%, devolução 20%, outros 20%; 15% de escalação) | Mapa "tema mais perguntado × documentação existente" com **gaps explícitos** — ex.: frete padrão <500kg e carga danificada sem documento formal | Agrupar e resumir temas é tarefa nativa de LLM |
| A5 | **Classificação de autoridade e saúde documental**: normativo / contratual / procedimento / informal; documentos sem dono, sem data ou sem versão | Ranking de confiabilidade por documento — ex.: FAQ-Atendimento marcado como informal não validado | Classificação por critérios definidos escala bem com IA |
| A6 | **Teste de parseabilidade técnica**: extração de amostra real de cada formato (PDF, DOCX, wiki, XLSX) | Relatório de qualidade de extração por formato, com destaque para as tabelas das planilhas de frete | Antecipação de risco de pipeline antes do desenvolvimento |

**Saída consolidada (dia 3-4):** Dossiê de Intent = catálogo + colisões + contradições + mapa de cobertura/gaps + ranking de autoridade + laudo de parseabilidade, **priorizado pelos temas de maior volume de chamados**.

> Supervisão humana na fase de Intent: o Tech Lead opera e ajusta os agentes; o Product Specialist e o DM revisam os outputs por amostragem — agentes produzem hipóteses, não verdades.

---

## 3. Discovery humano (dias 4-10) — alimentado pelo Dossiê

| # | Atividade humana | Participantes | Como o Dossiê alimenta |
|---|------------------|---------------|------------------------|
| H1 | **Review do Dossiê de Intent** com a NovaTech: validação do catálogo, das colisões e dos gaps encontrados | DM + Product + ponto focal de cada área | A reunião discute evidências concretas, não impressões |
| H2 | **Entrevistas com o atendimento** (6-8 sessões: atendentes e supervisores) para validar dores, temas e o fluxo real dos 12 minutos de busca | Product + DM | Roteiro construído sobre o mapa de temas e os gaps (ex.: "como vocês respondem hoje sobre carga danificada, que não tem documento formal?") |
| H3 | **Workshop de adjudicação de contradições**: decidir versão vigente de cada colisão (ex.: PROC-042) e o destino do FAQ informal (incluir rotulado / excluir / formalizar) | Decisores de Operações, Compliance e Comercial — **com mandato para decidir** | A pauta é a lista de conflitos do A2/A3, item a item |
| H4 | **Validação técnica e de segurança** com a TI: acessos, ACLs do SharePoint, security trimming, provisionamento Azure | Tech Lead + TI NovaTech | O laudo de parseabilidade (A6) e o inventário (A1) definem o que verificar |
| H5 | **Priorização do escopo do piloto**: quais temas/documentos entram no go-live faseado (critério: volume de chamados × saúde documental) | DM + Product + sponsor NovaTech | Cruzamento direto do mapa de cobertura (A4) com o ranking de autoridade (A5) |
| H6 | **Construção do golden dataset inicial**: 100 perguntas reais do atendimento com respostas validadas pelos especialistas | QA + Product + especialistas NovaTech | Perguntas extraídas dos temas mais frequentes; respostas validadas contra os documentos adjudicados no H3 |
| H7 | **Consolidação e apresentação executiva**: plano de governança documental (com dono nomeado), backlog do desenvolvimento, critérios de sucesso ratificados, go/no-go | DM + diretoria NovaTech | Síntese de todas as decisões da semana |

---

## 4. Sequência e dependências (o que precisa acontecer antes do quê)

```
Acessos liberados (pré-requisito absoluto)
   └─► A1 Inventário ─► A2 Duplicatas ─► A3 Contradições ─┐
   └─► A4 Temas/Gaps (precisa do A1 + dados de chamados) ──┤
   └─► A5 Autoridade (precisa do A1) ──────────────────────┼─► Dossiê de Intent
   └─► A6 Parseabilidade (amostra do A1) ──────────────────┘        │
                                                                    ▼
                              H1 Review do Dossiê (valida antes de usar)
                                   ├─► H2 Entrevistas (roteiro vem do Dossiê)
                                   ├─► H3 Workshop de contradições (pauta vem do A2/A3)
                                   └─► H4 Validação técnica (insumo do A6)
                                              │
                    H5 Priorização do piloto (precisa de H2 + H3)
                                              │
                    H6 Golden dataset (precisa do H3 — só valida resposta
                       contra documento já adjudicado)
                                              │
                    H7 Consolidação + go/no-go (precisa de tudo)
```

**Regras de dependência críticas:**
1. Nenhuma entrevista antes do Dossiê — entrevista "em branco" desperdiça o tempo do stakeholder com perguntas que o agente já respondeu.
2. Nenhum golden dataset antes da adjudicação (H3) — validar respostas contra documentos contraditórios produz gabarito contraditório.
3. Nenhum go/no-go sem dono de governança nomeado — sem isso, as contradições retornam no primeiro ciclo mensal de atualização.

---

## 5. O que a NovaTech precisa fornecer — e quando

| Quando | Item | Para quê | Criticidade |
|--------|------|----------|-------------|
| **Antes do dia 1** | Acesso de leitura ao SharePoint, Confluence e pasta de rede; conta/subscription Azure provisionada; acordos de confidencialidade e segurança assinados | Sem acesso, a fase de Intent não começa — é o caminho crítico do cronograma | Bloqueante |
| **Antes do dia 1** | Lista de stakeholders com papéis (atendentes, supervisores, decisores das 3 áreas, TI, sponsor) | Agendamento antecipado das sessões da semana 2 | Alta |
| **Dia 1** | Ponto focal de TI disponível (suporte a acessos e dúvidas de ambiente) | Destravar problemas de acesso em horas, não dias | Alta |
| **Dia 1-2** | Export/relatório do sistema de chamados com categorias e volumes | Alimentar o mapa de cobertura (A4) com dados reais | Alta |
| **Dias 4-5** | 1h de cada ponto focal de área para o review do Dossiê (H1) | Validar os achados dos agentes antes do uso | Alta |
| **Dias 5-8** | Agenda de 6-8 entrevistas de 45min (atendentes/supervisores) | H2 — sem afetar a operação do atendimento | Alta |
| **Dias 7-8** | Meio período dos decisores de Operações, Compliance e Comercial, **com mandato para decidir** versões e destino do FAQ | H3 — decisões, não opiniões | Bloqueante |
| **Dia 8-9** | Nomeação do **dono da governança documental** | Condição do go/no-go | Bloqueante |
| **Dias 8-9** | 2-3 especialistas para validar as 100 respostas do golden dataset | H6 | Alta |
| **Dia 10** | 1h da diretoria/sponsor para apresentação executiva e go/no-go | H7 | Alta |

---

## 6. Riscos do próprio discovery

- **Atraso de acessos** (risco nº 1 em projetos com TI corporativa): mitigação — checklist de acessos enviado na assinatura do contrato, com follow-up diário até o dia 1; dia 1 só conta com acesso confirmado.
- **Decisores sem mandato no workshop H3:** mitigação — alinhamento prévio com o sponsor de que o workshop produz decisões registradas em ata, não recomendações.
- **Agenda do atendimento (operação não para):** mitigação — entrevistas de 45min distribuídas em 4 dias, no máximo 2 por dia por equipe.
