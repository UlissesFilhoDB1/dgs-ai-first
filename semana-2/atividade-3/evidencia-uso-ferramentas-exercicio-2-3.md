# Evidência de Uso das Ferramentas — Exercício 2.3 (Delivery Manager)
**Projeto:** Assistente de IA NovaTech (RAG) — DB1
**Exercício:** 2.3 — Participação na construção do AGENTS.md (seção Project Management Rules)
**Ferramenta:** Claude (chat)
**Data:** 12/06/2026

> **Arquivo relacionado:**
> - `exercicio-2-3-agents-md-project-management-rules.md` (seção pronta para adicionar ao AGENTS.md do repositório)

---

## Turno 1 — Execução do Exercício 2.3

**Participante:** Solicitou a redação da seção "Project Management Rules" do AGENTS.md, com os inputs: estrutura do repositório (Anexo C), estrutura proposta do AGENTS.md pelo Tech Lead e os 4 validation gates simulados (Gate 1 PS→Plan; Gate 2 Tasks→Implement; Gate 3 Code→Merge; Gate 4 Tests→Deploy).

**Claude:** Observou que o leitor-alvo do artefato é um agente de IA (não um humano), e por isso mudou o registro de escrita: de prosa explicativa (usada nos documentos 2.1 e 2.2) para **regras prescritivas e machine-readable**. Produziu a seção com 7 blocos:

- **PM-1 Task & issue naming:** formato de título `[<module>] <imperative summary>`, labels obrigatórias em 3 eixos (type/module/sdd), vínculo da task com o caminho da spec, regra de fallback (`needs-triage`).
- **PM-2 Decision records (ADRs):** obrigatoriedade de ADR para decisões, formato de nome `NNNN-titulo.md`, seções do template, referência por id, proibição de o agente marcar ADR como `Accepted`.
- **PM-3 Validation gates (machine-readable):** os 4 gates expressos em bloco YAML parseável (id, name, transition, approver, blocks, on_reject), idênticos aos gates simulados fornecidos; regra de não ultrapassar gate e de não gerar artefato a partir de spec com CR aberto.
- **PM-4 Authoritative paths:** caminhos exatos do Anexo C para specs, ADRs, runbooks, system prompt + changelog obrigatório, eval, src, tests, infra; cabeçalho de metadados obrigatório nas specs.
- **PM-5 Spec status lifecycle:** valores de status em ordem, limite do que o agente pode fazer (até `Em Revisão`), regra de versionamento.
- **PM-6 Communication & language:** código/comentários/commits em inglês; specs/ADRs/relatórios/conteúdo de cliente em pt-BR; convenção de branch e Conventional Commits; proibição de conteúdo que prometa capacidade além do que o sistema entrega (coerência com o grounded-RAG).
- **PM-7 Things agents must NOT do:** lista negativa consolidada (não auto-aprovar gate, não dar merge, não inventar número de ADR, não criar arquivo fora dos caminhos, parar e marcar `needs-triage` em caso de incerteza).

---

## Decisões de redação (demonstração de uso crítico da ferramenta)

- **Machine-readable sobre prosa:** os gates foram expressos em YAML, e as regras em sentenças imperativas curtas com MUST/MUST NOT, para que um agente parseie e siga sem ambiguidade.
- **Prescritivo, não descritivo:** cada item diz o que fazer/não fazer (ex.: "Do not generate plan.md until requirements.md status is Aprovada"), não descreve conceitos.
- **Consistência com os gates fornecidos:** o bloco PM-3 reproduz exatamente os 4 gates simulados do enunciado (que, por sua vez, são os mesmos definidos no Exercício 2.1).
- **Especificidade NovaTech:** referências concretas — slugs dos 5 módulos, ADR-0002 (context budget), golden-queries, security trimming, regra anti-"o assistente sabe tudo", caminhos reais do Anexo C.

---

## Evidência dos critérios de avaliação

| Critério do exercício | Onde foi atendido |
|---|---|
| Seção machine-readable (agente consegue parsear e seguir) | Gates em YAML (PM-3); regras imperativas com MUST/MUST NOT; caminhos exatos (PM-4); valores de status enumerados (PM-5) |
| Regras prescritivas, não descritivas | Todos os blocos usam comandos ("use", "do not", "must"), não definições conceituais |
| Consistente com os validation gates fornecidos | PM-3 reproduz os 4 gates (approver, transition, on_reject) idênticos ao enunciado e ao Exercício 2.1 |
| Não genérica — referências específicas ao NovaTech | Slugs dos 5 módulos, ADR-0002, golden-queries, security trimming, política anti-alucinação, paths do Anexo C |
| Evidência do uso do Claude | Este registro: mudança consciente de registro (prosa → machine-readable) + decisões de redação documentadas |
