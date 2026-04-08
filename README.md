# Ian Rios — Product Manager & AI Builder

Product Manager na **Home do Mercado Pago** — tribe de Sellers.

Fora do trabalho, construo produtos de IA do zero: identifico um problema real, defino o espaço de solução, prototipo com IA e itero com dados.

---

## Como trabalho

Seja no Mercado Pago ou nos projetos pessoais, o processo é o mesmo:

```
Problema real → Pesquisa de usuário → Hipótese clara → MVP → Dados → Decisão
```

Nos projetos pessoais sou PM, dev e usuário ao mesmo tempo. Isso força validação rápida, corte do supérfluo e escala do que funciona.

---

## Projetos pessoais

### nutrIAn — Nutricionista de bolso no WhatsApp

**Problema:** Acompanhar nutrição é de alto esforço — apps são complicados, você esquece de registrar no momento certo.

**Insight:** No Brasil, todo mundo está no WhatsApp. Por que pedir que o usuário mude de app?

**O que foi construído:** Bot que analisa refeições via foto ou texto e retorna calorias e macros em ~3 segundos. Sem cadastro, sem download, zero fricção.

**Aprendizado principal:** Engajamento proativo no horário de refeição retém mais do que qualquer feature nova. O canal certo vale mais que a feature perfeita.

`Python · WhatsApp Cloud API · GPT-4o Vision · Supabase · Railway`

---

### EstudaAI — Plataforma de estudos com IA para medicina

**Problema:** Estudantes de medicina têm volume absurdo de conteúdo e método de estudo ainda passivo (ler PDF, sublinhar, repetir).

**Insight:** O conteúdo já existe. O que falta é transformação automática: de conteúdo passivo para aprendizado ativo.

**O que foi construído:** Plataforma que recebe um PDF e gera automaticamente trilhas de estudo, flashcards, quizzes e resumos via IA. Worker serverless para processar documentos longos sem timeout.

**Aprendizado principal:** O real job-to-be-done não é "resumir PDF" — é "me sentir preparado para a prova com o mínimo de esforço".

`Next.js · TypeScript · Supabase · Claude API · Cloudflare Workers`

---

### Flight Monitor — Alertas de passagens GRU → Europa

**Problema:** Encontrar passagens baratas para a Europa exige checar sites manualmente todo dia — e o preço certo aparece por janelas curtas, sem aviso.

**Insight:** O usuário não precisa ficar procurando o voo — precisa ser avisado quando o voo certo aparecer. E não precisa criar conta para isso.

**O que foi construído:** Produto web onde qualquer pessoa define origem, destino, período e teto de preço e recebe alerta por e-mail quando encontrar voo dentro do limite. Cancelamento com um clique, sem login. Monitoramento diário automatizado via GitHub Actions.

**Aprendizado principal:** Remover a necessidade de conta foi a decisão de conversão mais importante — a barreira de cadastro mata antes de o usuário ver valor.

`Next.js · Supabase · Python · GitHub Actions · Vercel`

---

## Stack de IA

| Para quê | Ferramenta |
|----------|-----------|
| Raciocínio, specs, análise de produto | Claude (Anthropic) |
| Visão computacional | GPT-4o |
| Desenvolvimento assistido | Claude Code |
| Banco + auth sem fricção | Supabase |
| Deploy rápido | Railway · Vercel |

---

## No Mercado Pago

PM da Home — superfície central da experiência de sellers. Trabalho com análise de dados, experimentação (A/B tests), discovery qualitativo e execução de produto. Foco em aumentar a principalidade do MP: fazer do app a ferramenta financeira principal do vendedor.

---

*São Paulo, Brasil*
