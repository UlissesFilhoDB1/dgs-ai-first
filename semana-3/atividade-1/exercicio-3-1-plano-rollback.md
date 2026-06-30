# Plano de Rollback — NovaTech Assistant (Go-Live do Piloto)
**Projeto:** Assistente de IA NovaTech (RAG) — DB1
**Autor:** Delivery Manager · Fase de Governança e Validação · Data: 19/06/2026

---

## Princípio

Rollback não é "avaliar a situação". É um conjunto de **gatilhos objetivos** que, ao dispararem, acionam um **responsável nomeado** que executa uma **ação pré-definida** — sem reunião, sem deliberação no momento da crise. A decisão difícil é tomada agora, com calma; na hora do incidente só se executa.

O piloto tem 5 atendentes, o que torna o rollback barato: desligar afeta poucas pessoas e elas voltam ao processo manual (que ainda existe). Isso favorece um gatilho conservador — na dúvida, desliga.

---

## Níveis de resposta

Há dois níveis, para não tratar tudo como catástrofe:

- **Nível 1 — Mitigar (degradação parcial):** forçar todas as respostas para a fila de HITL (assistente vira "sugestão que humano valida", em vez de resposta direta). Mantém valor parcial sem desligar.
- **Nível 2 — Rollback total (desligar):** bot do Teams sai do ar para os pilotos; volta o processo manual. Usado quando o problema é de segurança, dado ou erro sistêmico.

---

## Matriz de gatilhos

| # | Gatilho (sinal objetivo) | Nível | Quem decide | Ação imediata |
|---|--------------------------|-------|-------------|---------------|
| G1 | Taxa de feedback negativo **> 25%** em qualquer janela de 24h | Nível 2 | Delivery Manager (Tech Lead aciona) | Desligar bot; comunicar pilotos; abrir investigação |
| G2 | Taxa de feedback negativo entre **15% e 25%** em 24h | Nível 1 | Tech Lead | Forçar todas as respostas para HITL; investigar causa |
| G3 | Qualquer resposta que **inverta regra de tema sensível** (ex.: afirmar que carga perigosa pode ser devolvida) chega a um atendente | Nível 2 | Tech Lead (autonomia para agir sem aguardar DM) | Desligar bot imediatamente; é falha de guardrail — bug bloqueante |
| G4 | **Vazamento de dado pessoal** em log ou resposta (e-mail/nome de atendente ou cliente) | Nível 2 | Tech Lead + DM (notificar Compliance NovaTech) | Desligar; conter o log; acionar resposta a incidente de segurança |
| G5 | Latência p95 **> 10s** sustentada por 1h, ou erro técnico **> 5%** das requisições | Nível 1 → 2 se persistir 2h | Tech Lead | Nível 1: avisar pilotos de lentidão; Nível 2 se não estabilizar |
| G6 | Azure AI Search ou Azure OpenAI indisponível | Nível 2 (automático) | Sistema (fallback) + Tech Lead confirma | Mensagem de indisponibilidade; pilotos ao processo manual |
| G7 | Golden dataset em produção regride para **> 10%** de erro após alguma mudança | Nível 2 | Tech Lead | Reverter a última mudança (prompt/índice) para a versão anterior conhecida |

---

## Procedimento de execução (qualquer Nível 2)

1. **Desligar:** o Tech Lead desabilita o bot no Bot Framework (ou via feature flag) — ação de minutos.
2. **Comunicar:** DM avisa os 5 atendentes-piloto e o ponto focal da NovaTech por canal pré-combinado (Teams), com mensagem-modelo já redigida.
3. **Reverter (se aplicável):** se o gatilho foi uma mudança (G7), reverter para a última versão estável registrada (prompt versionado em `prompts/system-prompt.md` + changelog; índice da reindexação anterior).
4. **Registrar:** abrir incidente com o gatilho que disparou, horário, dados do momento. Sem caça ao culpado — foco em causa-raiz.
5. **Investigar e corrigir:** causa-raiz → correção → nova passagem pelos validation gates (cenário 2) antes de religar.
6. **Religar:** somente após a correção passar no golden dataset (≤ 5%) e o critério de go-live correspondente voltar ao verde.

---

## Quem é quem

- **Tech Lead** — autoridade técnica para desligar/reverter; pode agir sozinho em G3, G4, G6 (segurança e regra sensível não esperam).
- **Delivery Manager** — decisão de negócio em G1, comunicação com pilotos e NovaTech, condução do pós-incidente.
- **QA** — reexecuta o golden dataset antes de religar.
- **Compliance NovaTech** — notificado em G4 (dado pessoal).

---

## Mensagem-modelo aos pilotos (pré-redigida)

> "Pessoal, o assistente está temporariamente fora do ar para um ajuste de qualidade. Por favor, sigam o processo manual de consulta à documentação até novo aviso. Avisaremos assim que voltar. Obrigado pela paciência — o feedback de vocês é o que está tornando a ferramenta melhor."

O fato de a mensagem já existir é parte do plano: na hora do incidente, ninguém precisa pensar em como comunicar.