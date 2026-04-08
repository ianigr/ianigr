🇧🇷 Português · 🇦🇷 [Español](README.es-AR.md) · 🇺🇸 [English](README.md)

# Ian Rios — Product Manager & AI Builder

**Product Manager no Mercado Pago** · São Paulo, Brasil

Construo produtos nativos de IA do zero: identifico um problema real de usuário, formulo como hipótese testável, entrego um MVP com IA e itero com dados.

---

## Como trabalho

Decisões de produto precisam ser rastreáveis — da dor do usuário à hipótese, à decisão, ao resultado. Não construo features; testo apostas. E acompanho se elas se pagam.

```
Dor do usuário (observada)
  → Enquadramento do problema (qual é o job to be done real?)
    → Hipótese (se [X], então [métrica Y muda], porque [insight Z])
      → MVP (mínimo para validar a hipótese, não o produto)
        → Dados (moveu? por quê ou por que não?)
          → Decisão (escalar, pivotar ou matar)
```

Uso IA em todo esse ciclo: para síntese na descoberta, para gerar e estressar hipóteses, para acelerar o desenvolvimento e para analisar dados mais rápido do que qualquer processo manual. O objetivo não é usar IA — é comprimir o tempo entre o insight do usuário e a decisão validada.

---

## Projetos pessoais

### nutrIAn — Acompanhamento nutricional com fricção zero

**O problema:** A maioria das pessoas que tenta acompanhar nutrição abandona em menos de duas semanas. Não por falta de vontade — por excesso de fricção. Apps exigem criação de conta, entrada manual, banco de dados de alimentos complexo e mudança de comportamento consistente. A ferramenta briga com o usuário.

**Job to be done:** *"Me ajuda a sentir que estou no controle do que como, com o mínimo de esforço possível."*

**Hipótese:** Se eu encontrar o usuário no WhatsApp — onde ele já passa horas por dia — e reduzir o registro a uma foto ou mensagem de texto, a retenção será significativamente maior do que em apps de nutrição que exigem mudança de comportamento em cima de mudança de hábito.

**O que foi construído:**
- Bot de WhatsApp que aceita foto ou descrição em texto de qualquer refeição
- GPT-4o Vision analisa a refeição e retorna calorias + proteína, carboidrato e gordura em ~3 segundos
- **Camada de qualidade LLM-as-a-Judge:** uma segunda chamada de LLM avalia cada resposta nutricional antes de enviá-la ao usuário — verificando tamanhos de porção realistas, faixas calóricas plausíveis e consistência interna. Funciona como um portão de qualidade automatizado que captura alucinações antes de chegarem ao usuário
- Wizard conversacional de definição de meta calórica (2 passos)
- Histórico do dia com barra de progresso vs. meta pessoal
- Resumo semanal com link para dashboard
- Registro retroativo (adicionar refeições de antes, por data)
- Fluxo de correção: "na verdade era grelhado, não frito" — IA recalcula e atualiza o histórico
- Motor de engajamento proativo: mensagens nos horários de refeição do Brasil, silêncio das 21h às 7h30, máximo 1 mensagem proativa por dia, gatilho após 12h de inatividade do usuário
- Todos os dados armazenados por usuário no Supabase (PostgreSQL), sem criação de conta

**Decisões-chave de produto:**

*Canal > feature:* O WhatsApp tem ~99% de penetração entre usuários de smartphone no Brasil. Uma UX melhor dentro de um app novo não vence uma UX razoável onde o usuário já passa horas. Custo de aquisição é zero; mudança de comportamento exigida é mínima.

*LLM-as-a-Judge para qualidade de output:* Alucinações nutricionais (ex: "500g de frango grelhado = 200 kcal") destroem a confiança mais rápido do que qualquer outro modo de falha. Uma segunda passagem de avaliação captura outputs implausíveis antes de chegarem ao usuário. Este é o mecanismo central de confiabilidade do produto — usuários param de usar uma ferramenta nutricional no momento em que param de confiar nos números.

*Engajamento proativo > reativo:* Testes mostraram que lembretes contextuais no horário de refeição superam esperar que o usuário registre espontaneamente. O gatilho certo é contextual (horário de refeição), não arbitrário (notificação às 9h). O engajamento no momento da decisão — a refeição — é o que forma hábito.

*Paridade texto-foto:* Nem toda refeição pode ser fotografada (café às 7h na correria, almoço no cliente). A análise por texto precisa ser igualmente precisa — senão a vantagem do canal quebra exatamente quando o usuário mais precisa.

*Zero conta:* A fricção de cadastro mata a conversão antes do usuário ver qualquer valor. A identidade no WhatsApp é a conta — sem auth separada necessária.

**Métricas acompanhadas:**
- **Retenção:** usuários ativos D7 e D30 (métrica primária de sucesso)
- **Engajamento:** refeições registradas por usuário ativo por dia (meta: ≥2)
- **Qualidade:** taxa de rejeição do LLM-as-a-Judge (% de respostas sinalizadas para regeração)
- **Motor proativo:** taxa de abertura de mensagens proativas; conversão de mensagem proativa para registro de refeição em até 30 min
- **Taxa de correção:** % de refeições corrigidas pelo usuário (proxy para precisão da análise)

**Status:** MVP online desde março/2026 · Recrutando beta fechado (10 usuários) · Validando retenção D7 antes de escalar

`Python · WhatsApp Cloud API · GPT-4o Vision · Supabase · Railway (CI/CD)`

---

### EstudaAI — Aprendizado ativo a partir de conteúdo passivo

**O problema:** Estudantes de medicina têm muito conteúdo e pouco tempo. O método de estudo padrão ainda é passivo: ler, sublinhar, repetir. O conteúdo existe — mas não está em um formato que produza conhecimento durável. Criar materiais de estudo ativo (flashcards, quizzes, resumos estruturados) a partir de PDFs brutos leva horas que poderiam ir para a prática em si.

**Job to be done:** *"Me ajuda a me sentir preparado para a prova com o mínimo de esforço da minha parte."*

**Hipótese:** Se a IA puder transformar automaticamente qualquer PDF em materiais de estudo ativo — flashcards, quizzes, resumos estruturados —, estudantes se sentirão mais preparados em menos tempo e o gap entre "ter o conteúdo" e "estar pronto" colapsa.

**O que foi construído:**
- Plataforma web onde estudantes fazem upload de qualquer PDF (capítulo de livro, artigo, apostila)
- **Pipeline RAG (Retrieval-Augmented Generation):** documentos são divididos em chunks, embedados e armazenados em banco vetorial. A geração de cada tipo de output (flashcards, quiz, resumo) recupera os chunks semanticamente mais relevantes por tópico — garantindo cobertura de todo o documento e possibilitando targeting mais preciso do que abordagens de full-context naive
- Worker de processamento assíncrono — processa documentos longos sem timeout ou bloqueio
- Geração automática de quatro tipos de output por documento:
  - Resumo estruturado por tópico
  - Sets de flashcards (pares pergunta/resposta) sobre conceitos-chave por chunk
  - Quiz de múltipla escolha com gabarito e explicações
  - Sequência de estudo sugerida ordenada por complexidade de tópico (derivada de clustering de similaridade de embeddings)
- Arquitetura desacoplada: upload → fila → worker processa → usuário notificado quando pronto
- Worker no Cloudflare Workers — serverless, sem cold start para jobs assíncronos

**Decisões-chave de produto:**

*RAG em vez de injeção full-context:* Passar um PDF de 200 páginas inteiro no contexto é caro, lento e produz outputs de menor qualidade — o modelo atende tudo igualmente. RAG com chunking + recuperação semântica garante que cada flashcard e questão de quiz seja fundamentado na seção mais relevante do documento, não na média do documento completo. Também viabiliza queries entre múltiplos documentos em versões futuras (estudar por vários materiais enviados).

*Processamento assíncrono:* PDFs de medicina podem ter centenas de páginas. Processamento síncrono gera timeout e experiência quebrada (usuário olhando tela de loading). O worker desacopla upload de entrega — usuário faz upload, fecha a aba, recebe notificação quando o material está pronto. Também permite processar múltiplos documentos em paralelo.

*Quatro formatos, não um:* Momentos de estudo diferentes pedem formatos diferentes. Revisão rápida → flashcard. Simulado → quiz. Entender contexto → resumo. Planejamento de estudo → sequência por complexidade. Oferecer os quatro elimina o meta-trabalho de escolher a ferramenta certa para o momento certo.

*Medicina como segmento de entrada:* Alto volume de conteúdo, métodos de estudo inalterados há décadas, alta disposição a pagar por ferramentas que economizam tempo, usuários motivados com necessidade clara e recorrente (provas). Dor clara, disposição validada de mudar comportamento. A plataforma generaliza para qualquer área com alto volume de conteúdo — medicina é a cabeça de praia.

*Claude API para geração:* Janela de contexto longa processa capítulos inteiros sem hacks de chunking na etapa de geração. Qualidade de geração de flashcards e quizzes é significativamente melhor que modelos GPT-3.5-class — e qualidade é o núcleo do produto; um flashcard errado é pior do que nenhum flashcard.

**Métricas acompanhadas:**
- **Tempo-para-primeiro-valor:** tempo do upload até o primeiro output pronto (meta: <5 min para capítulo padrão)
- **Qualidade de output:** taxa de precisão reportada pelo usuário em flashcards e questões geradas (survey após primeira sessão)
- **Resultados de aprendizado:** scores do quiz vs. confiança autoavaliada pré-quiz (mede se o material realmente ajuda)
- **Profundidade de engajamento:** % dos flashcards gerados revisados; % do quiz completado (não apenas aberto)
- **Taxa de retorno:** retorno D7 após o primeiro upload (métrica primária de retenção)
- **Cobertura RAG:** % das seções do documento representadas nos outputs gerados (verificação automatizada contra chunks de origem)

**Status:** Em desenvolvimento ativo desde fevereiro/2026

`Next.js · TypeScript · Supabase · Claude API · Cloudflare Workers`

---

### Flight Monitor — Seja notificado. Não fique procurando.

**O problema:** Encontrar passagens baratas exige checar sites manualmente todo dia, em vários lugares. O preço certo aparece por janelas curtas — às vezes horas — e desaparece. Monitorar não cria nenhum valor; é pura repetição mecânica de uma verificação trivial: *o preço caiu abaixo do meu limite?*

**Job to be done:** *"Me avisa quando o voo certo aparecer. Não me faz ficar procurando."*

**Hipótese:** Se o usuário puder definir um limite de preço e receber um e-mail quando ele for atingido — sem precisar criar conta e com cancelamento em um clique — a adoção será significativamente maior do que alertas que forçam cadastro antes de mostrar valor.

**O que foi construído:**
- Produto web onde qualquer pessoa define: aeroportos de origem, destinos, janela de datas, duração mínima/máxima da viagem e preço máximo em reais
- Monitoramento automático diário de preços via GitHub Actions cron job
- Alerta por e-mail com voos específicos encontrados, preços em BRL (com câmbio + IOF aplicados) e links diretos para compra
- Cancelamento em um clique via token UUID embutido em todos os e-mails — sem login, sem conta
- Expansão multi-aeroporto automática: "São Paulo" monitora automaticamente GRU + CGH + VCP — usuários pensam em cidades, não em códigos IATA
- Câmbio aplicado automaticamente: taxa diária USD→BRL via API aberta + 6,38% de IOF — o limite que o usuário define é o número que aparece na fatura do cartão
- Criação de conta opcional: usuários que queiram gerenciar múltiplos alertas têm acesso a um dashboard para visualizar, pausar e deletar todos os alertas ativos em um lugar — conta é opcional, não obrigatória para usar o produto
- Tabela de histórico de alertas no Supabase com trilha completa de notificações enviadas

**Decisões-chave de produto:**

*Sem conta = mais conversão:* Fricção de cadastro mata a adoção antes do usuário ver qualquer valor. Tokens de cancelamento resolvem identidade sem auth — o usuário gerencia o alerta a partir da caixa de entrada. Também elimina pedidos de suporte sobre senhas esquecidas.

*Preço em reais, custo final:* Usuários brasileiros pensam em reais. Converter USD manualmente adiciona fricção cognitiva e imprecisão (pessoas subestimam o IOF). O produto aplica a cotação do dia e o IOF automaticamente — o limite que o usuário define é o número que aparece na fatura.

*GitHub Actions em vez de servidor contínuo:* O monitor precisa rodar uma vez por dia. Um servidor 24/7 para job diário é desperdício operacional puro. GitHub Actions resolve com cron job — zero custo de infraestrutura, zero overhead de manutenção.

*Multi-IATA por cidade:* Usuários pensam em cidades, não em códigos de aeroporto. O produto expande para todos os IATAs relevantes automaticamente e reporta por aeroporto específico no e-mail de alerta — especificidade no output, simplicidade no input.

**Métricas acompanhadas:**
- **Taxa de criação de alertas:** % de visitantes que completam o formulário de alerta (conversão)
- **Taxa alerta→notificação:** % de alertas ativos que dispararam pelo menos um e-mail (efetividade do produto — estamos realmente encontrando voos?)
- **Engajamento de e-mail:** taxa de abertura e clique no link de compra por e-mail de alerta enviado
- **Lifetime do alerta:** dias médios que um alerta fica ativo antes do cancelamento (proxy de satisfação — cancelamentos rápidos indicam que os alertas não são úteis)

**Status:** Em uso · 4 usuários ativos em fase de testes · Validando taxa de conversão alerta→notificação

`Next.js · Supabase · Python · GitHub Actions · Vercel`

---

## Como uso IA no trabalho de produto

IA não está só nos produtos que construo — está em como trabalho como PM.

| Etapa | Como IA acelera |
|-------|----------------|
| **Discovery** | Síntese de pesquisa qualitativa em escala, identificação de padrões entre entrevistas, geração de explicações alternativas para comportamento observado |
| **Enquadramento do problema** | Framings alternativos, estresse de pressupostos, identificação de quais hipóteses são mais críticas de validar primeiro |
| **Geração de hipóteses** | Hipóteses explícitas no formato se/então/porque, métrica mais sensível a cada hipótese, levantamento de casos extremos antes de chegarem à engenharia |
| **Escrita de specs** | PRDs com critérios de sucesso e modos de falha explícitos, casos extremos que o time não considerou, requisitos desafiados contra objetivos reais do usuário |
| **Análise de dados** | Interpretação de funil, análise de coorte, detecção de anomalias, hipóteses de segmentação, design e leitura de experimentos |
| **Desenvolvimento** | Claude Code para implementação de MVPs — entrego protótipos e ferramentas internas sem esperar sprint de engenharia |
| **Tomada de decisão** | Raciocínio estruturado com pressupostos explícitos, níveis de confiança e avaliação de reversibilidade antes de escalar para stakeholders |

---

*São Paulo, Brasil*
