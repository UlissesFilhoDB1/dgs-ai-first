# Histórico da Conversa com o Claude — Exercício 1.2 (Delivery Manager)
**Projeto:** Assistente de IA NovaTech (RAG) — DB1
**Exercício:** 1.2 — Comunicação de expectativas com o cliente
**Ferramentas:** Claude (chat) — redação do e-mail e construção do one-pager
**Objetivo do registro:** Evidenciar o uso das ferramentas e o processo de decisão, conforme exigido no entregável.
**Data:** 05/06/2026

> **Arquivos relacionados:**
> - `exercicio-1-2-email-diretor-novatech.md` (e-mail final — versão escolhida)
> - `one-pager-assistente-ia-novatech.html` (one-pager A4, exportável em PDF via Imprimir → Salvar como PDF)

---

## Turno 1 — Prompt do exercício

**Participante:** Enviou o enunciado completo do Exercício 1.2, incluindo o e-mail fictício do diretor de operações da NovaTech:

> *"Estamos animados com o projeto. Nosso CEO viu uma demo do Copilot e quer algo parecido. A expectativa é que em 3 meses nosso time de atendimento não precise mais procurar nada manualmente. O assistente vai saber tudo."*

A tarefa: (1) e-mail de resposta que valide o entusiasmo, explique respostas probabilísticas e RAG em linguagem não-técnica e proponha 2-3 critérios de sucesso mensuráveis; (2) one-pager visual anexável.

---

## Turno 2 — Geração do e-mail em duas abordagens estratégicas

**Claude:** Em vez de um rascunho único, gerou **duas variantes com estratégias distintas**, para o Delivery Manager escolher conforme a relação com o interlocutor:

**Variante A — "Pedagógico e entusiasmado"**
- Assunto: *"Assistente de IA NovaTech — como vamos transformar essa expectativa em resultado"*
- Investia mais na explicação conceitual, com duas analogias:
  - IA probabilística = "funcionário brilhante e absurdamente rápido — mas que responde com a confiança de quem tem certeza, mesmo nas raras vezes em que está errado";
  - RAG = "bibliotecária ultrarrápida: ela não inventa o conteúdo do livro, ela encontra a página certa e resume para você, dizendo de qual livro veio".

**Variante B — "Direto ao ponto executivo"**
- Assunto: *"Projeto Assistente NovaTech — 3 compromissos mensuráveis para o go-live"*
- Mais curta e assertiva, com a frase-síntese: *"automatizar uma base contraditória só entregaria respostas contraditórias mais rápido"* e o reposicionamento da expectativa: *"a meta real é melhor que essa — o atendente continua no comando, mas valida em segundos em vez de procurar por 12 minutos"*.

**Elementos comuns às duas variantes (exigências do exercício):**
- Validação do entusiasmo do CEO/diretoria logo na abertura;
- Explicação não-técnica do caráter probabilístico da IA generativa;
- Explicação de RAG precisa e acessível (busca na documentação oficial antes de responder + fonte citada);
- Vínculo explícito entre qualidade das respostas e qualidade da documentação-fonte (exemplo concreto: duas versões do procedimento de frete especial coexistindo);
- 3 critérios de sucesso mensuráveis:
  1. **≥95% de precisão** em conjunto de 100 perguntas reais validado pelos especialistas da NovaTech antes do go-live;
  2. **100% das respostas com fonte citada e verificável** — incluindo abstenção explícita ("não encontrado na base") em vez de inventar;
  3. **Tempo médio de consulta < 2 minutos no piloto**, medido contra baseline de 12 minutos.

---

## Turno 3 — Geração do one-pager

**Claude:** Construiu o one-pager em formato A4 (HTML imprimível em PDF), estruturado para leitura executiva em menos de 2 minutos:

- **Fluxo em 4 passos:** Pergunta → Busca na documentação oficial → Resposta baseada apenas nos trechos encontrados → Fonte citada (documento + seção) com validação do atendente;
- **Nota de fundamento:** explicação em 2 linhas de por que a IA é probabilística e por que ancorar na documentação com fonte citada é o que torna o assistente confiável;
- **Quadros lado a lado "O que ele faz bem / O que ele não faz"**, incluindo: admite quando não sabe; não resolve contradições sozinho; não substitui o julgamento do atendente; não acerta 100% — por isso há medição contínua;
- **Os 3 critérios de sucesso em destaque numérico** (≥95% / 100% / <2 min);
- **"Regra de ouro" em destaque no rodapé:** "o assistente é tão bom quanto a documentação que ele lê" — conectando o projeto ao workstream de organização das fontes com Operações, Compliance e Comercial.

---

## Turno 4 — Decisão do participante

**Participante:**
> "Gostei da elaboração do E-mail B. E o one-pager ficou ótimo."

**Claude:** Registrou a escolha da **Variante B ("Direto ao ponto executivo")** como versão final e a salvou como arquivo de entregável.

---

## Evidência dos critérios de avaliação

| Critério do exercício | Onde foi atendido |
|---|---|
| Linguagem acessível, sem jargão técnico | Analogias (funcionário brilhante, bibliotecária); zero termos como "embedding", "chunk", "vector store" no e-mail e no one-pager |
| Explicação de RAG precisa sem ser acadêmica | "Cada pergunta primeiro busca os trechos relevantes na documentação oficial; a resposta é construída só a partir deles, sempre com a fonte citada" |
| Critérios de sucesso mensuráveis e derivados da tecnologia | ≥95% de precisão em golden set validado; 100% de respostas com fonte verificável (inclui abstenção correta); <2 min vs baseline de 12 min |
| One-pager compreensível por executivo em 2 minutos | 1 página A4: fluxo visual em 4 passos, faz/não faz em quadros espelhados, 3 métricas em destaque numérico, regra de ouro em 1 frase |
| Evidência do uso das ferramentas | Este registro: geração de 2 variantes estratégicas → análise dos trade-offs de tom → decisão do participante → versão final salva |
