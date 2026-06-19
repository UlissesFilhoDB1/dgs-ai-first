# Change Management de Specs — NovaTech Assistant
**Projeto:** Assistente de IA NovaTech (RAG) — DB1
**Autor:** Delivery Manager
**Quando se aplica:** quando uma spec precisa mudar **depois** de já estar em status "Em Implementação".
**Data:** 12/06/2026

---

## 1. Por que change management de spec é crítico no SDD

Mudar um documento de requisitos em papel é barato. Mudar uma spec que **já gerou tasks e código** tem efeito cascata: tasks em andamento podem ficar obsoletas, código pode precisar ser refeito, testes podem invalidar. Sem processo, o time descobre a divergência tarde — quando o agente já gerou artefatos a partir de uma spec que mudou "informalmente". Por isso a mudança é governada como um evento explícito.

---

## 2. Gatilho: o que conta como mudança de spec

- Novo requisito ou requisito removido.
- Alteração de um critério de aceite.
- Mudança na abordagem técnica do `plan.md` que afeta contratos entre módulos.
- Mudança de escopo provocada por fator externo (ex.: o Compliance resolver uma das 12 contradições pendentes e isso alterar uma regra de negócio).

Correção de typo, reformulação sem mudança de sentido ou detalhamento que não altera escopo **não** disparam o processo — entram como ajuste menor (versão 1.x).

---

## 3. Fluxo de mudança (Change Request)

```
1. ABERTURA          Qualquer membro abre um Change Request (CR) referenciando a spec
                     e o motivo. Vira issue no Azure DevOps, ligada à spec.
        │
2. AVALIAÇÃO         Tech Lead + Product Specialist avaliam impacto: o que muda no
   DE IMPACTO        plan, quais tasks são afetadas, qual código/teste precisa refazer,
                     impacto em prazo. DM registra.
        │
3. DECISÃO           Aprovador conforme o tipo de mudança (ver matriz abaixo) aprova,
                     rejeita ou adia o CR.
        │
4. CONGELAMENTO      As tasks afetadas que ainda não começaram são pausadas;
   PARCIAL           as em andamento são avaliadas caso a caso (terminar x descartar).
        │
5. ATUALIZAÇÃO       A spec é atualizada via PR (incremento de versão), o changelog e o
                     cabeçalho são atualizados, e — se for decisão relevante — um ADR é criado.
        │
6. RE-PROPAGAÇÃO     tasks.md é regenerado/ajustado a partir da spec nova; volta ao
                     Gate 2 antes de a implementação recomeçar.
        │
7. COMUNICAÇÃO       DM comunica o time e atualiza o board; a spec sai de "Em
                     Implementação" e retorna ao status adequado (Em Revisão/Aprovada).
```

**Regra de ouro:** enquanto um CR está aberto sobre uma spec, **nenhum agente deve gerar novos artefatos a partir dela** — a spec está em estado inconsistente até o CR fechar.

---

## 4. Quem pode alterar e quem precisa aprovar

| Tipo de mudança | Quem pode propor | Quem aprova | Reflexo na versão |
|-----------------|------------------|-------------|-------------------|
| Critério de aceite ou requisito (escopo do **quê**) | Qualquer um | **Product Specialist** + ciência do Tech Lead | Maior (→ x.0) |
| Abordagem técnica no `plan.md` (o **como**) | Tech Lead / Dev | **Tech Lead** + ciência do Product Specialist | Maior se mudar contrato; menor caso contrário |
| Decomposição de tasks (sem mudar plan) | Dev | **Tech Lead** (Gate 2) | Menor |
| Mudança de escopo com impacto em prazo/custo | Qualquer um | **Product Specialist + Tech Lead + Delivery Manager** (e sponsor da NovaTech se afetar entrega contratada) | Maior |
| Ajuste menor (typo, clareza) | Qualquer um | Revisor do PR | Patch (não dispara CR) |

---

## 5. Efeito sobre tasks já em andamento

No passo 4 (Congelamento Parcial), cada task afetada recebe uma decisão registrada:

- **Não iniciada** → pausada e regenerada após a atualização da spec.
- **Em andamento, ainda válida** → continua (a mudança não a afeta).
- **Em andamento, invalidada** → interrompida; o trabalho parcial é avaliado (aproveitar parte ou descartar). A decisão e o motivo ficam na issue do CR para não se perder o racional.
- **Concluída, mas agora obsoleta** → vira nova task de ajuste/refactor, ligada ao CR.

---

## 6. Princípios

- Mudar spec é normal; mudar spec **silenciosamente** é o que se proíbe.
- Todo CR é rastreável: issue → PR → versão da spec → ADR (quando aplicável).
- O custo da mudança é tornado visível **antes** da decisão (passo 2), para que aprovar seja uma escolha informada, não uma surpresa de prazo depois.
- A spec e o código nunca divergem por mais de um ciclo: ou a spec é atualizada, ou o CR é rejeitado e o código volta ao que a spec diz.
