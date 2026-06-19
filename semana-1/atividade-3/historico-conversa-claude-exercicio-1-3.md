# Evidência de Uso das Ferramentas — Exercício 1.3 (Delivery Manager)
**Projeto:** Assistente de IA NovaTech (RAG) — DB1
**Exercício:** 1.3 — Planejamento de discovery com IA (AI First SDLC · fase de Intent)
**Ferramentas:** Claude (chat) — elaboração do plano e construção do cronograma visual
**Data:** 05/06/2026

> **Arquivos relacionados:**
> - `exercicio-1-3-plano-discovery-novatech.md` (plano textual de discovery)
> - `cronograma-discovery-novatech.html` (cronograma visual de 2 semanas, A4 paisagem, exportável em PDF via Imprimir → Salvar como PDF)

---

## Turno 1 — Prompt do exercício + carga de contexto adicional

**Participante:** Enviou o enunciado completo do Exercício 1.3, incluindo a descrição da fase de Intent do AI First SDLC:

> *"Antes de entrevistas com stakeholders, agentes especializados analisam documentação existente, contexto de negócio e restrições para gerar um mapa priorizado de fontes, dependências e gaps."*

Em seguida, anexou o pacote completo do projeto (`Prática_1.zip`) com os anexos A e B, os 5 documentos individuais da NovaTech e o arquivo-mestre dos exercícios.

**Claude:** Listou o conteúdo do zip antes de extrair, confirmou os arquivos já conhecidos e **extraiu um dado novo do arquivo-mestre** para enriquecer o plano: a distribuição real de dúvidas do atendimento (prazos 35%, frete 25%, devolução 20%, outros 20%, com 15% de escalação ao supervisor) — usada como critério de priorização no mapa de temas da fase de Intent.

---

## Turno 2 — Elaboração do plano de discovery

**Claude:** Produziu o plano textual estruturado em 6 seções:

**1. Princípio organizador** — a regra de divisão de trabalho que sustenta todo o plano:
- *Agentes fazem o que IA faz bem em escala:* catalogar, comparar, agrupar, detectar duplicatas e contradições, resumir — inviável para humanos sobre ~1.250 fontes em 2 semanas;
- *Humanos fazem o que IA não faz:* validar com quem vive o processo, decidir, priorizar, negociar governança e assumir compromissos.

**2. Fase de Intent (dias 1-3) — 6 atividades de agentes**, cada uma com produto e justificativa de por que IA faz bem:
- A1 Inventário automatizado das 3 fontes (~800 SharePoint + ~400 Confluence + ~50 planilhas);
- A2 Detecção de duplicatas e versões coexistentes (ex.: PROC-042 v1 × v2);
- A3 Análise de contradições de conteúdo (multiplicadores, prazos, regras divergentes);
- A4 Clusterização temática + mapa de cobertura cruzado com os volumes reais de chamados → gaps explícitos (frete <500kg, carga danificada);
- A5 Classificação de autoridade documental (normativo/contratual/procedimento/informal);
- A6 Teste de parseabilidade técnica por formato (PDF, DOCX, wiki, XLSX).
Saída: **Dossiê de Intent** — mapa priorizado de fontes, dependências e gaps. Com a ressalva de supervisão humana: "agentes produzem hipóteses, não verdades".

**3. Discovery humano (dias 4-10) — 7 atividades**, cada uma indicando como o Dossiê a alimenta: review do Dossiê (H1), entrevistas com roteiro derivado dos achados (H2), workshop de adjudicação de contradições com decisores **com mandato** (H3), validação técnica de ACLs/security trimming (H4), priorização do escopo do piloto (H5), golden dataset de 100 perguntas validadas (H6) e consolidação com go/no-go (H7).

**4. Sequência e dependências** — diagrama de precedência com 3 regras críticas:
- Nenhuma entrevista antes do Dossiê (entrevista "em branco" desperdiça stakeholder);
- Nenhum golden dataset antes da adjudicação (gabarito contra documentos contraditórios é gabarito contraditório);
- Nenhum go/no-go sem dono de governança nomeado.

**5. O que a NovaTech fornece e quando** — tabela com 10 itens, prazo e criticidade (acessos como bloqueante pré-D1; decisores com mandato em D7-8; dono de governança até D9; diretoria no D10).

**6. Riscos do próprio discovery** — atraso de acessos, decisores sem mandato e agenda da operação, cada um com mitigação.

---

## Turno 3 — Construção do cronograma visual

**Claude:** Transformou o plano em um Gantt de 10 dias úteis (A4 paisagem), com:
- Linhas agrupadas por natureza: pré-requisitos NovaTech / fase de Intent (agentes) / discovery humano;
- Código de cores por responsável (agentes de IA, humanos DB1+NovaTech, fornecimentos do cliente, marcos);
- 3 marcos de decisão sinalizados: **Dossiê ◆** (D3-4), **Adjudicação H3 ◆** (D7-8) e **Go/No-Go ◆** (D10);
- Painéis laterais com as dependências críticas e a linha do tempo de fornecimentos da NovaTech.

---

## Evidência dos critérios de avaliação

| Critério do exercício | Onde foi atendido |
|---|---|
| Intent antecede e alimenta o discovery humano | Estrutura do plano (Intent D1-3 → Dossiê → atividades humanas D4-10) + coluna "como o Dossiê alimenta" em cada atividade humana + regra "nenhuma entrevista antes do Dossiê" |
| Atividades de agentes realistas (catalogar, comparar, resumir) | A1-A6: inventário, detecção de duplicatas, comparação de contradições, clusterização temática, classificação e teste de extração — todas com justificativa de adequação à IA |
| Atividades humanas focam no que IA não faz | H1-H7: validar, decidir (workshop com mandato), priorizar, negociar governança, ratificar go/no-go |
| Cronograma realista para 2 semanas | 10 dias úteis com paralelismo onde possível (A2-A5 simultâneos; H2 ∥ H4), folgas implícitas e caminho crítico explícito (acessos → Intent → Dossiê → decisões) |
| Identificação do que a NovaTech fornece e quando | Tabela dedicada com 10 itens datados e classificados por criticidade (bloqueante/alta) + painel no cronograma |
| Evidência do uso das ferramentas | Este registro: leitura seletiva do zip → extração de dado novo (distribuição de chamados) → plano → cronograma visual derivado do plano |
