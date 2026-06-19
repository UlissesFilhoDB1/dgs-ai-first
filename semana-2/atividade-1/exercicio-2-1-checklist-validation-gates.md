# Checklist de Validation Gates — NovaTech Assistant
**Projeto:** Assistente de IA NovaTech (RAG) — DB1
**Artefato:** template operacional de gates (gerado via Claude Cowork)
**Uso:** anexado a cada item no Azure DevOps; o gate só é marcado "aprovado" quando todos os critérios objetivos estão satisfeitos.
**Fase:** Estruturação do Trabalho · Data: 12/06/2026

---

## Como usar

Cada módulo passa por 4 gates ao longo do ciclo Spec → Plan → Tasks → Implement → Review → Deploy. Um gate é **bloqueante**: a etapa seguinte não inicia sem a aprovação registrada. Marque cada critério como atendido (☑) antes de aprovar. Reprovação devolve o artefato ao autor com os itens não atendidos anotados.

---

## GATE 1 — Spec → Plan
*A spec de requisitos está pronta para virar plano técnico?*

- **Quem aprova:** Tech Lead (o consumidor da spec; é ele quem vai escrever o plan a partir dela).
- **Prazo:** até 1 dia útil após a submissão do `requirements.md`.
- **Critérios de aprovação (todos obrigatórios):**
  - ☐ Cada requisito tem ao menos um **critério de aceite verificável** (testável objetivamente, não "deve funcionar bem").
  - ☐ Os requisitos estão dentro do escopo do módulo e não invadem outro módulo.
  - ☐ Guardrails de produto aplicáveis estão referenciados (ex.: resposta sempre com citação de fonte; abstenção quando não há cobertura).
  - ☐ Dependências de outros módulos ou de decisões pendentes estão explícitas (ex.: as 12 contradições no Compliance, se afetarem o módulo).
  - ☐ Casos de erro e exceção previstos (ex.: pergunta sem cobertura documental, tier inexistente).
- **Se reprovar:** retorna ao **Product Specialist** com os requisitos ambíguos marcados; novo ciclo de revisão em até 1 dia útil. O plan **não** é iniciado.

---

## GATE 2 — Tasks → Implement
*As tasks geradas fazem sentido e são executáveis?*

- **Quem aprova:** Tech Lead.
- **Prazo:** até meio dia útil após a submissão do `tasks.md`.
- **Critérios de aprovação (todos obrigatórios):**
  - ☐ Cada task é **atômica** (entregável por um agente/dev em uma sessão) e tem critério de pronto explícito.
  - ☐ As tasks cobrem 100% do `plan.md` — nenhum componente do plano ficou sem task.
  - ☐ A ordem e as dependências entre tasks estão corretas (nada que dependa de algo ainda não construído).
  - ☐ Cada task referencia a skill aplicável (`/skills/...`) e os caminhos corretos do repositório (Anexo C).
  - ☐ Tasks que tocam segurança/dados sensíveis (ex.: ACLs, security trimming) estão sinalizadas para atenção extra no review.
- **Se reprovar:** retorna ao **Desenvolvedor** que gerou as tasks (com Copilot); re-decomposição. A implementação **não** inicia.

---

## GATE 3 — Code → Merge
*O código gerado por agente pode ser mesclado?*

- **Quem aprova:** Tech Lead (code review) — PR exige **1 approval** obrigatório.
- **Prazo:** até 1 dia útil após o PR ser aberto.
- **Critérios de aprovação (todos obrigatórios):**
  - ☐ CI verde: lint + build + testes unitários passando.
  - ☐ Código segue o AGENTS.md e as convenções TypeScript (`strict: true`); comments em inglês.
  - ☐ A task de origem está integralmente atendida e nada fora do escopo dela foi incluído.
  - ☐ Tratamento de erros conforme a skill `error-handling` (sem engolir exceções, logs estruturados via pino).
  - ☐ Sem segredos hardcoded, sem credenciais; configuração via ambiente.
  - ☐ Revisão crítica do código gerado por IA: o revisor entende cada linha e não há "código plausível mas errado".
- **Se reprovar:** PR volta com comentários; o **Desenvolvedor** corrige. Merge bloqueado até novo approval.

---

## GATE 4 — Tests → Deploy
*Os testes são suficientes para liberar em produção?*

- **Quem aprova:** QA valida testes/cobertura → Tech Lead aprova o deploy (dupla assinatura).
- **Prazo:** até 1 dia útil após o merge na branch de release.
- **Critérios de aprovação (todos obrigatórios):**
  - ☐ Cobertura dos critérios de aceite da spec: cada critério tem ao menos um teste correspondente.
  - ☐ Cenários de falha mapeados pelo QA estão cobertos (alucinação, contradição de versões, pergunta sem cobertura, tier inexistente).
  - ☐ Testes de integração com mocks (msw) passando; e2e crítico executado (com parcimônia de tokens).
  - ☐ Para mudanças no system prompt: rodada de avaliação contra o `golden-queries.json` sem regressão.
  - ☐ Validação de segurança: usuário sem permissão não recebe conteúdo restrito (security trimming).
  - ☐ Runbook/rollback documentado para o que está sendo liberado.
- **Se reprovar:** deploy bloqueado; QA registra os gaps; volta para **Desenvolvedor** (novos testes/correção) ou para revisão de escopo. Sem deploy até a suíte cumprir os critérios.

---

## Resumo dos gates

| Gate | Transição | Aprova | Prazo | Se reprovar, volta para |
|------|-----------|--------|-------|-------------------------|
| 1 | Spec → Plan | Tech Lead | 1 dia útil | Product Specialist |
| 2 | Tasks → Implement | Tech Lead | ½ dia útil | Desenvolvedor (autor das tasks) |
| 3 | Code → Merge | Tech Lead (1 approval no PR) | 1 dia útil | Desenvolvedor |
| 4 | Tests → Deploy | QA valida + Tech Lead aprova | 1 dia útil | Desenvolvedor / revisão de escopo |

**Regra geral:** nenhum gate é "pulável" por pressão de prazo. Se um gate vira gargalo recorrente, o tema vai para a retrospectiva — não se afrouxa o critério no caso a caso.
