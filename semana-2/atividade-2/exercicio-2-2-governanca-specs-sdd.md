# Governança de Specs (SDD) — NovaTech Assistant
**Projeto:** Assistente de IA NovaTech (RAG) — DB1
**Autor:** Delivery Manager
**Fase:** Estruturação do Trabalho
**Data:** 12/06/2026

---

## 1. Princípio: a spec é um contrato executável, não um documento passivo

No Spec Driven Development, a spec é a fonte de verdade do que será construído — os agentes de IA geram código, tasks e testes a partir dela. Isso muda a postura de governança em dois pontos:

1. **Spec é artefato vivo.** Ela evolui com o aprendizado do time; mudar uma spec é um evento normal e governado, não um fracasso de planejamento.
2. **Spec desatualizada é dívida perigosa.** Como agentes confiam na spec, uma spec que não reflete a realidade do código produz artefatos errados com confiança. Manter spec e código sincronizados é parte do trabalho, não um extra.

Cada módulo tem três artefatos SDD, em fases encadeadas por checkpoints humanos (os mesmos gates do workflow 2.1):

| Artefato | Define | Autor | Aprova (gate) |
|----------|--------|-------|---------------|
| `requirements.md` | O **quê** — requisitos e critérios de aceite | Product Specialist | Tech Lead (Gate 1) |
| `plan.md` | O **como** — abordagem técnica, contratos, riscos | Tech Lead | Product Specialist + Dev Sênior |
| `tasks.md` | A **decomposição** — unidades atômicas executáveis | Dev (com Copilot) | Tech Lead (Gate 2) |

A atribuição segue a competência: quem entende o negócio escreve o requisito; quem domina a arquitetura escreve o plano; quem vai implementar decompõe em tasks.

---

## 2. Localização e nomenclatura no repositório

Conforme o Anexo C, cada módulo tem sua pasta sob `/specs/`, com os três arquivos de nome fixo:

```
specs/
├── pipeline-ingestao/      → requirements.md · plan.md · tasks.md
├── query-endpoint/         → requirements.md · plan.md · tasks.md
├── feedback-api/           → requirements.md · plan.md · tasks.md
├── teams-bot/              → requirements.md · plan.md · tasks.md
└── painel-web/             → requirements.md · plan.md · tasks.md
```

- **Slug da pasta** = nome do módulo em kebab-case (ex.: `query-endpoint`).
- **Nomes dos arquivos** são fixos (`requirements.md`, `plan.md`, `tasks.md`) — previsibilidade é essencial para que os agentes encontrem a spec pelo caminho.

---

## 3. Versionamento

As specs são versionadas **pelo próprio Git** — não há sistema paralelo. As regras:

- **Toda alteração de spec é um Pull Request**, nunca um commit direto na main. O PR é o instrumento de aprovação rastreável.
- **Cabeçalho de metadados** no topo de cada arquivo de spec, mantido atualizado:
  ```
  ---
  módulo: query-endpoint
  artefato: requirements
  versão: 1.2
  status: Aprovada
  autor: <papel/nome>
  aprovado-por: <papel/nome>
  data-aprovação: 2026-06-12
  ---
  ```
- **Versão semântica leve:** incremento menor (1.1 → 1.2) para ajustes que não mudam o escopo; incremento maior (1.x → 2.0) quando o escopo muda (ver change management).
- **Histórico de mudanças** no rodapé da spec (data, autor, resumo, motivo) — além do log do Git, para leitura rápida por humanos e agentes.
- **Rastreabilidade cruzada:** decisões técnicas ou de escopo que surjam ao escrever uma spec viram ADR em `/docs/adr/` e são referenciadas pelo número (ex.: "conforme ADR-0002, context budget de ~8K para chunks").

---

## 4. Rastreio de mudanças (quem mudou o quê e por quê)

Três camadas, do mais granular ao mais legível:

1. **Git log / PR** — diff exato, autor, revisor, data. Camada de auditoria.
2. **Cabeçalho de metadados** — estado atual da spec em uma olhada.
3. **Board de tracking** (artefato Cowork, anexo) — visão de portfólio: status de todas as specs ao mesmo tempo, para qualquer membro do time.

Toda mudança aprovada atualiza as três camadas — é responsabilidade do autor da mudança, verificada no gate.

---

## 5. Ciclo de vida do status de uma spec

```
Rascunho ─► Em Revisão ─► Aprovada ─► Em Implementação ─► Validada
   │            │             │              │                 │
 autor       no gate       gate ok       tasks viram        testes da
 escreve     (1 ou 2)      (assinado)     código            spec passam
                  │
                  └─► (reprovada) volta para Rascunho com notas
```

- **Rascunho:** em escrita pelo autor; não consumível por agentes ainda.
- **Em Revisão:** submetida ao gate; aprovador analisa dentro do prazo definido no 2.1.
- **Aprovada:** passou no gate; vira insumo da próxima fase (requirements aprovado → plan pode começar).
- **Em Implementação:** tasks estão sendo executadas; a spec está "congelada" salvo change management.
- **Validada:** o código atende aos critérios de aceite e os testes da spec passam (fecha o Gate 4).

---

## 6. Responsabilidades resumidas

| Papel | Responsabilidade na governança de specs |
|-------|------------------------------------------|
| Product Specialist | Autor dos `requirements.md`; co-aprova `plan.md`; aprova mudanças de escopo |
| Tech Lead | Autor dos `plan.md`; aprova `requirements.md` (Gate 1) e `tasks.md` (Gate 2); guardião da coerência arquitetural |
| Dev Sênior | Co-aprova `plan.md`; apoia decomposição de tasks |
| Desenvolvedor | Autor dos `tasks.md` (com Copilot); mantém spec sincronizada com código |
| QA | Verifica que os critérios de aceite da spec são testáveis; valida no Gate 4 |
| Delivery Manager | Dono do processo e do board; arbitra prazos e escala impedimentos; conduz change management |
