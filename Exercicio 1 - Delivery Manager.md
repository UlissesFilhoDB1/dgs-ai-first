# Avaliação de Riscos — Assistente de IA NovaTech
**Projeto:** Assistente de consulta à documentação (RAG) — NovaTech Logística
**Autor:** Delivery Manager — DB1
**Finalidade:** Análise inicial de viabilidade para kickoff interno
**Versão:** 2.0 (refinada após revisão crítica)
**Data:** 05/06/2026

---

## 1. Sumário executivo

O projeto é **viável** com a tecnologia atual: assistentes baseados em RAG (Retrieval-Augmented Generation) são um caso de uso maduro, e o stack proposto (Azure AI Search + LLM, integrado a Teams/SharePoint) é adequado. Porém, a viabilidade **não depende do modelo de IA, e sim da qualidade e governança da base documental** da NovaTech — que hoje apresenta contradições entre versões, documentos informais sem validação e lacunas de cobertura. Os sete riscos abaixo decorrem das características de LLMs aplicadas a essa base e ao contexto organizacional do cliente. Sem as mitigações propostas, o risco maior não é o assistente "não funcionar", e sim ele **funcionar com confiança aparente e responder errado** — o pior cenário para um time de atendimento que fala com cliente em tempo real.

---

## 2. Matriz de riscos

### R1 — Alucinação: o assistente inventa procedimentos, valores ou entidades que não existem

LLMs geram texto plausível mesmo sem base factual. No contexto NovaTech há dois gatilhos **já confirmados na própria base** — não são hipóteses: (a) **perguntas sem cobertura documental** — frete padrão abaixo de 500kg não está documentado em nenhuma fonte; sem instrução de abstenção, o assistente tende a "completar" com uma fórmula inventada; (b) **entidades inexistentes mencionadas pelo cliente** — cliente que se diz "tier Platinum"; o assistente pode gerar SLAs fictícios para um tier que a SLA-2024 afirma explicitamente não existir. Agravante: a SLA-2024 é documento **contratual** — um SLA inventado vira compromisso indevido com cliente.

- **Probabilidade:** Alta — justificativa: os gatilhos existem hoje na base; é certeza de ocorrência sem mitigação, não possibilidade.
- **Impacto:** Qualidade (crítico) e risco contratual/jurídico. Dimensionamento: com 320 chamados/dia e ~60% envolvendo consulta a documentação (~192/dia), uma taxa de alucinação de apenas 5% significaria **~9-10 respostas incorretas por dia** chegando a clientes.
- **Mitigação (dono / fase):** (1) Grounding estrito via prompt: o assistente só responde com base nos chunks recuperados e cita a fonte (documento + seção) em toda resposta — *Tech Lead, design da solução*; (2) resposta padrão de abstenção ("não encontrei essa informação na documentação oficial — escalar para X") quando o retrieval não traz cobertura — *Tech Lead, design*; (3) **golden dataset de testes** com perguntas-armadilha (tier inexistente, frete <500kg, inversão de regra de carga perigosa), construído no discovery — *QA + Product Specialist* — e executado como regressão a cada mudança de prompt, modelo ou índice. **Gatilho de bloqueio: nenhum go-live com alucinação registrada no conjunto de teste.**

### R2 — Documentação contraditória gera respostas que misturam regras de versões diferentes

PROC-042 v1 e PROC-042-v2 coexistem no SharePoint sem marcação de obsolescência, com multiplicadores regionais (Sudeste 1.0 vs 1.1), fatores de peso (1.2 vs 1.15) e prazos (+2 vs +3 dias) diferentes. Como os dois documentos têm mesmo título e mesma estrutura, a similaridade semântica entre eles é máxima: o retrieval trará **chunks das duas versões em praticamente toda pergunta sobre frete especial**, e o LLM pode compor uma resposta híbrida — multiplicador da v2 com prazo da v1 — **sem que o atendente tenha como perceber o erro**.

- **Probabilidade:** Alta (quase certa no estado atual da base) — justificativa: não depende de azar do retrieval; a colisão entre versões é estrutural.
- **Impacto:** Custo e qualidade, com perda financeira calculável: a diferença Sudeste 1.0 → 1.1 representa **10% no valor do frete**. Errar para menos é prejuízo direto na receita; errar para mais gera reclamação e crédito compensatório via penalidade de SLA (5-10% por violação, conforme SLA-2024 §4).
- **Mitigação (dono / fase):** (1) **Curadoria pré-ingestão bloqueante** (semanas 1-2 do discovery) com as 3 áreas para decidir o status de cada versão duplicada antes de indexar — *DM facilita, NovaTech decide*. Sem essa decisão, nenhuma solução técnica resolve: o pipeline só prioriza o que alguém marcou como vigente; (2) metadados obrigatórios de **vigência e versão** na ingestão, com filtro que exclui ou rebaixa documentos substituídos — *Tech Lead, pipeline*; (3) instrução no prompt: quando duas versões aparecerem no contexto, usar a mais recente e sinalizar a divergência ao atendente — *Tech Lead*; (4) workstream de **governança documental com dono nomeado na NovaTech** — sem ele, o problema retorna a cada ciclo mensal de atualização — *DM negocia no kickoff*.

### R3 — Dependência da qualidade das fontes: o FAQ informal contamina respostas críticas

A qualidade de um RAG tem teto na qualidade do corpus. O FAQ-Atendimento não é validado por Compliance, mas é a **única fonte** para temas relevantes (carga danificada, seguro de carga, carga perigosa em frete expresso). O assistente não distingue sozinho documento normativo de anotação informal: tratará ambos com a mesma autoridade. Há ainda o desafio técnico das ~50 planilhas XLSX: tabelas mal extraídas viram texto sem sentido no índice.

- **Probabilidade:** Alta — porém com característica distinta dos demais riscos: é **eliminável por decisão de produto, não por engenharia**.
- **Impacto:** Compliance e qualidade — condicionado à decisão sobre o FAQ. O trade-off deve ser explicitado à NovaTech: **incluir o FAQ rotulado = risco de autoridade indevida em resposta crítica; excluir o FAQ = gap de cobertura em carga danificada e seguro, que recai no R1 (abstenção em temas reais do dia a dia)**. A terceira via — formalizar os conteúdos do FAQ via Compliance — é a melhor, mas consome prazo do cliente.
- **Mitigação (dono / fase):** (1) Levar a decisão incluir/excluir/formalizar como **item de pauta do discovery com prazo definido** — *DM + Product Specialist*; (2) se incluído: metadado de **nível de autoridade** por documento (contratual > normativo > procedimento > informal), usado no ranking e exibido na resposta ("fonte: FAQ informal, não validado") — *Tech Lead*; (3) piloto de ingestão com amostra real de cada formato (PDF, DOCX, wiki, XLSX) nas 2 primeiras semanas para medir qualidade de extração antes de comprometer o cronograma — *Tech Lead + Devs*.

### R4 — Expectativa da diretoria vs. o que a tecnologia entrega hoje

A meta "de 12 para <2 minutos por chamado" foi definida pela diretoria **antes de qualquer baseline técnico ou piloto** — o padrão clássico de projeto que entrega valor real e ainda assim "fracassa" na percepção. Além disso, a diretoria pode estar assumindo implicitamente resposta correta em 100% dos casos — e nenhum sistema RAG entrega isso. Há também risco de escopo implícito: o briefing fala em "responder dúvidas", mas pode haver expectativa de que o assistente **calcule** fretes, o que exige tratamento determinístico (fórmulas), não apenas geração de texto.

- **Probabilidade:** Alta (revisada de Média) — justificativa: a expectativa já está fixada e comunicada sem validação técnica; o desalinhamento é o estado de partida, não um evento futuro incerto.
- **Impacto:** Prazo (retrabalho de alinhamento), custo e percepção de valor — podendo invalidar o projeto na visão do sponsor mesmo com entrega tecnicamente boa.
- **Mitigação (dono / fase):** (1) **Acordo de definição de sucesso** assinado no kickoff — artefato de 1 página com métricas objetivas (groundedness ≥95% no golden dataset, taxa de abstenção correta, tempo médio de resposta), taxa de erro tolerada e papel do atendente como validador (human-in-the-loop) — *DM, kickoff*; esse documento vira o instrumento de gestão de expectativa durante todo o projeto; (2) medir o **baseline real dos 12 minutos** no discovery para provar o ganho depois — *DM + NovaTech*; (3) go-live faseado: piloto com 5-8 atendentes e subconjunto curado de documentos antes de abrir para os 45 — *DM, plano de release*.

### R5 — Context rot: degradação de qualidade com volume excessivo de contexto

LLMs têm um **orçamento de atenção limitado**: com contexto muito grande, informações no meio são ignoradas, instruções se diluem e a chance de misturar chunks contraditórios aumenta (amplificando o R2). Este risco se desdobra em dois sub-riscos de natureza diferente:

- **R5a — Contexto inflado por consulta:** a tentação de "recuperar bastante para garantir" (top-k grande) degrada a qualidade da geração, especialmente em perguntas multi-domínio ("prazo de devolução + carga perigosa + frete especial"). *Probabilidade: Baixa-Média* — mitigável por engenharia padrão, se o Tech Lead seguir boas práticas.
- **R5b — Crescimento e degradação do índice no tempo:** a base de ~1.250 fontes cresce com atualizações mensais de 3 áreas sem processo unificado. Esse sub-risco **não aparece no go-live — aparece no mês 4**, quando versões novas começam a colidir com antigas no índice. *Probabilidade: Alta sem governança.*

- **Impacto:** Qualidade (silencioso e difícil de diagnosticar) e custo por consulta (tokens × 192 consultas/dia).
- **Mitigação (dono / fase):** (1) Top-k enxuto (3-5 chunks) com **reranking** semântico após o retrieval inicial — *Tech Lead, design*; (2) chunking por tipo de documento (seções normativas ≠ linhas de planilha), preservando cabeçalhos e metadados — *Devs, pipeline*; (3) avaliar **retrieval separadamente da geração** (recall@k e precisão sobre o mapa pergunta→chunks) para diagnosticar se erros vêm da busca ou da geração — *QA, discovery em diante*; (4) para o R5b: rotina de re-indexação com deduplicação de versões, acoplada ao workstream de governança do R2 — *Tech Lead + dono de governança na NovaTech*.

### R6 — Adoção: o time de atendimento abandona a ferramenta após os primeiros erros visíveis

45 atendentes que hoje resolvem ambiguidade "perguntando para quem sabe" precisam **confiar** na ferramenta para mudar de hábito. A confiança em assistentes de IA é assimétrica: constrói-se devagar e se perde rápido — bastam 2-3 erros visíveis na primeira semana para o time voltar silenciosamente ao processo antigo. Nesse cenário, o ROI morre **mesmo com o sistema funcionando**: a meta de <2 minutos não se realiza porque ninguém usa, e a diretoria conclui que "a IA não funcionou".

- **Probabilidade:** Média — justificativa: depende diretamente de quanto os riscos R1-R3 se materializam no piloto; com eles mitigados, cai para baixa.
- **Impacto:** Percepção de valor e ROI do projeto inteiro — é o risco que converte falhas técnicas pontuais em fracasso de negócio.
- **Mitigação (dono / fase):** (1) Piloto com **atendentes influentes como early adopters**, escolhidos com a gestão de atendimento da NovaTech — *DM + Product Specialist, fase de piloto*; (2) canal de feedback dentro do fluxo (botão "resposta incorreta" no Teams) com **loop visível de correção** — o atendente precisa ver que o erro reportado foi corrigido — *Tech Lead + QA*; (3) comunicar desde o início o papel da ferramenta: apoio com fonte citada para o atendente validar, não oráculo — *DM, plano de comunicação*; (4) acompanhar métrica de adoção (% de chamados com uso do assistente) como KPI do projeto, não só métricas técnicas — *DM*.

### R7 — Permissionamento e segurança da informação: o índice ignora as ACLs do SharePoint

O SharePoint da NovaTech contém documentos de Compliance e Comercial com permissões de acesso distintas. Se o pipeline de ingestão indexar tudo num índice único sem **security trimming**, o assistente pode expor conteúdo restrito (ex.: condições comerciais, documentos de compliance em revisão como a PROC-043) a qualquer um dos 45 atendentes — um vazamento interno via ferramenta corporativa. Esse tipo de falha é arquitetural: corrigir depois do go-live exige reindexação e redesenho, não um patch.

- **Probabilidade:** Baixa — justificativa: o Azure AI Search suporta security trimming nativamente; o risco é de omissão no design, não de limitação tecnológica.
- **Impacto:** Severo — incidente de segurança da informação, dano de confiança com o cliente e potencial implicação de compliance; custo alto de correção tardia (retrofit arquitetural).
- **Mitigação (dono / fase):** (1) **Security trimming por usuário desde a arquitetura** (propagação das ACLs do SharePoint para o índice e filtro por identidade do atendente via Teams SSO) — *Tech Lead, design da solução, decisão registrada antes do desenvolvimento*; (2) incluir no discovery um levantamento com a TI da NovaTech sobre classificação de sensibilidade dos ~800 documentos do SharePoint — *DM + TI NovaTech*; (3) teste de segurança específico no QA: usuário sem permissão não recebe conteúdo restrito nem o vê citado como fonte — *QA, critério de aceite do go-live*.

---

## 3. Visão consolidada

| # | Risco | Prob. | Impacto principal | Mitigação-chave | Dono |
|---|-------|-------|-------------------|-----------------|------|
| R1 | Alucinação (procedimentos/tiers inventados) | Alta | Qualidade / contratual (~9-10 erros/dia a 5%) | Grounding + citação + golden dataset bloqueante | Tech Lead + QA |
| R2 | Contradição entre versões (PROC-042 v1/v2) | Alta (quase certa) | Custo (±10% no frete) / qualidade | Curadoria bloqueante + metadados de vigência + governança | DM + NovaTech |
| R3 | Fonte informal sem validação (FAQ) | Alta (eliminável por decisão) | Compliance / cobertura | Decisão incluir/excluir/formalizar + metadado de autoridade | DM + Product |
| R4 | Expectativa vs. realidade da tecnologia | Alta | Percepção de valor / prazo | Acordo de definição de sucesso assinado + baseline + piloto | DM |
| R5 | Context rot (por consulta e no tempo) | Média | Qualidade silenciosa / custo | Top-k + reranking + avaliação de retrieval isolada | Tech Lead + QA |
| R6 | Adoção pelo time de atendimento | Média | ROI do projeto inteiro | Early adopters + loop de feedback visível + KPI de adoção | DM + Product |
| R7 | Permissionamento / segurança (ACLs) | Baixa | Severo (vazamento interno) | Security trimming desde a arquitetura + teste de acesso no QA | Tech Lead |

**Leitura da matriz:** R1-R3 são riscos técnicos com raiz organizacional (qualidade da base); R4 e R6 são riscos de gestão e mudança que determinam a percepção de sucesso; R5 e R7 são riscos de arquitetura que custam pouco para prevenir e muito para corrigir depois.

---

## 4. Três perguntas ao Tech Lead antes de confirmar os 3 meses

1. **Pipeline de ingestão:** Qual a estratégia de extração e chunking para cada formato da base (PDF, DOCX, páginas Confluence e, principalmente, as planilhas XLSX com tabelas de frete), e como vamos anexar metadados de vigência, versão e nível de autoridade a cada chunk? Quanto do cronograma você reserva para validar a qualidade do parsing com amostras reais antes de indexar tudo?

2. **Avaliação de qualidade:** Como vamos medir retrieval e geração separadamente antes do go-live — golden dataset, recall@k, groundedness — e qual o critério objetivo de "pronto para produção"? Conseguimos automatizar essa suíte para rodar a cada mudança de prompt, índice ou modelo?

3. **Atualização contínua e acesso:** Considerando que a documentação muda mensalmente (e a wiki semanalmente) por 3 áreas sem processo unificado, como o pipeline fará re-indexação incremental, deduplicação de versões e **propagação das permissões do SharePoint (security trimming)**? Esse custo recorrente de operação está dimensionado, ou os 3 meses cobrem só a construção inicial?

---

## 5. Recomendação

Confirmar viabilidade condicionada a: (a) duas semanas de discovery com piloto de ingestão sobre amostra real; (b) workstream de governança documental com dono nomeado na NovaTech; (c) **acordo de definição de sucesso** assinado com a diretoria antes do desenvolvimento; (d) decisão formal da NovaTech sobre o destino do FAQ informal (incluir rotulado, excluir ou formalizar via Compliance); (e) security trimming como requisito de arquitetura, não item de backlog. Sem (b), o projeto entrega um assistente que automatiza a confusão existente — mais rápido, porém igualmente inconsistente.
