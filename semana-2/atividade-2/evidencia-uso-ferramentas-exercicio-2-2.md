# Evidência de Uso das Ferramentas — Exercício 2.2 (Delivery Manager)
**Projeto:** Assistente de IA NovaTech (RAG) — DB1
**Exercício:** 2.2 — Governança de specs no modelo SDD
**Ferramentas:** Claude (chat) — documento de governança e change management · Claude Cowork — board de tracking
**Data:** 12/06/2026

> **Arquivos relacionados:**
> - `exercicio-2-2-governanca-specs-sdd.md` (processo de governança)
> - `exercicio-2-2-change-management-specs.md` (processo de change management)
> - `exercicio-2-2-board-tracking-specs.html` (board visual — artefato Cowork)
> - `exercicio-2-2-board-tracking-tabela.md` (board em tabela, importável em ferramenta de kanban)

---

## Turno 1 — Execução do Exercício 2.2

**Participante:** Solicitou o Exercício 2.2 — Governança de specs SDD, com os inputs: estrutura do repositório (Anexo C), fluxo SDD simplificado (requirements → plan → tasks, cada transição um checkpoint humano) e a lista dos 5 módulos (pipeline de ingestão, query endpoint, feedback API, teams bot, painel web).

**Claude:** Produziu os três artefatos exigidos, reaproveitando a coerência com o Exercício 2.1 (os mesmos validation gates).

**1. Documento de governança (Claude):**
- Princípio da spec como contrato executável e artefato vivo (com a consequência específica do SDD: spec desatualizada gera artefatos errados porque os agentes confiam nela);
- Tabela artefato × autor × aprovador alinhada aos gates do 2.1 (requirements/PS → Gate 1/TL; plan/TL → PS+Dev Sênior; tasks/Dev → Gate 2/TL);
- Localização e nomenclatura conforme o Anexo C (slug kebab-case por módulo; nomes de arquivo fixos para os agentes localizarem pelo caminho);
- Versionamento via Git (PR obrigatório, cabeçalho de metadados, versão semântica leve, changelog, cross-ref com ADRs);
- Rastreio de mudanças em 3 camadas (Git/PR, cabeçalho, board);
- Ciclo de vida de status (Rascunho → Em Revisão → Aprovada → Em Implementação → Validada);
- Tabela de responsabilidades por papel.

**2. Board de tracking (Claude Cowork):**
- Kanban com as 5 colunas de status e os 5 módulos como itens iniciais;
- Cada módulo mostra os 3 artefatos SDD (R/P/T) com etiqueta de estágio por cor;
- Entregue em duas formas: HTML visual e tabela markdown importável (com campos sugeridos para o board real: módulo, artefato, status, versão, autor, aprovador, data, link do PR, CRs abertos).

**3. Change management (Claude):**
- Justificativa do porquê de ser crítico no SDD (efeito cascada sobre tasks e código já gerados);
- Definição de gatilho (o que conta como mudança vs. ajuste menor);
- Fluxo de Change Request em 7 passos (abertura → avaliação de impacto → decisão → congelamento parcial → atualização da spec → re-propagação → comunicação);
- Regra de ouro: nenhum agente gera artefato a partir de spec com CR aberto;
- Matriz "quem pode alterar × quem aprova × reflexo na versão";
- Tratamento explícito de tasks já em andamento (4 situações: não iniciada, em andamento válida, em andamento invalidada, concluída obsoleta);
- Exemplo ancorado no projeto: o Compliance resolver uma das 12 contradições pendentes como gatilho de mudança de escopo.

---

## Evidência dos critérios de avaliação

| Critério do exercício | Onde foi atendido |
|---|---|
| Specs reconhecidas como artefatos vivos que evoluem | Seção 1 da governança (spec viva + risco de spec desatualizada) e todo o documento de change management |
| Board prático, qualquer membro vê o status atual | Kanban de 5 colunas + tabela importável com campos operacionais (versão, autor, aprovador, PR, CRs) |
| Change management explícito (quem altera, quem aprova, efeito nas tasks) | Matriz de aprovação por tipo de mudança + seção 5 (efeito sobre tasks em andamento, 4 situações) |
| Atribuição de responsabilidades coerente com competências | requirements/PS, plan/TL, tasks/Dev — tabela de responsabilidades na governança |
| Evidência do uso das ferramentas | Este registro: governança e change management no Claude + board no Cowork (HTML + tabela), com coerência mantida com os gates do 2.1 |
