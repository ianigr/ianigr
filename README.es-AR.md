🇧🇷 [Português](README.pt-BR.md) · 🇦🇷 Español · 🇺🇸 [English](README.md)

# Ian Rios — Product Manager & AI Builder

**Product Manager en Mercado Pago** · São Paulo, Brasil

Construyo productos nativos de IA desde cero: identifico un problema real del usuario, lo formulo como hipótesis testeable, lanzo un MVP usando IA e itero con datos.

---

## Cómo trabajo

Las decisiones de producto tienen que ser trazables — desde el dolor del usuario hasta la hipótesis, la decisión y el resultado. No construyo features; pruebo apuestas. Y le doy seguimiento a si se pagan.

```
Dolor del usuario (observado)
  → Encuadre del problema (¿cuál es el job to be done real?)
    → Hipótesis (si [X], entonces [métrica Y se mueve], porque [insight Z])
      → MVP (mínimo para validar la hipótesis, no el producto)
        → Datos (¿se movió? ¿por qué o por qué no?)
          → Decisión (escalar, pivotar o matar)
```

Uso IA en todo este ciclo: para síntesis en discovery, para generar y estresar hipótesis, para acelerar el desarrollo y para analizar datos más rápido que cualquier proceso manual. El objetivo no es usar IA — es comprimir el tiempo entre el insight del usuario y la decisión validada.

---

## Proyectos personales

### nutrIAn — Seguimiento nutricional, fricción cero

**El problema:** La mayoría de las personas que intenta hacer seguimiento de nutrición abandona en menos de dos semanas. No por falta de voluntad — por exceso de fricción. Las apps requieren creación de cuenta, carga manual, bases de datos complejas y cambio consistente de comportamiento. La herramienta pelea contra el usuario.

**Job to be done:** *"Ayudame a sentir que tengo el control de lo que como, con el mínimo esfuerzo posible."*

**Hipótesis:** Si encuentro a los usuarios en WhatsApp — donde ya pasan horas por día — y reduzco el registro a una foto o mensaje de texto, la retención será significativamente mayor que en apps de nutrición que exigen cambio de comportamiento encima del cambio de hábito.

**Qué se construyó:**
- Bot de WhatsApp que acepta foto o descripción en texto de cualquier comida
- GPT-4o Vision analiza la comida y devuelve calorías + proteína, carbohidratos y grasa en ~3 segundos
- **Capa de calidad LLM-as-a-Judge:** una segunda llamada de LLM evalúa cada respuesta nutricional antes de enviarla al usuario — verificando tamaños de porción realistas, rangos calóricos plausibles y consistencia interna. Funciona como un gate de calidad automatizado que captura alucinaciones antes de que lleguen al usuario
- Wizard conversacional de definición de meta calórica (2 pasos)
- Historial del día con barra de progreso vs. meta personal
- Resumen semanal con link al dashboard
- Registro retroactivo (agregar comidas de antes, por fecha)
- Flujo de corrección: "en realidad era a la plancha, no frito" — la IA recalcula y actualiza el historial
- Motor de engagement proactivo: mensajes en las ventanas de horario de comida de Brasil, silencio de 21h a 7h30, máximo 1 mensaje proactivo por día, disparo tras 12h de inactividad del usuario
- Todos los datos almacenados por usuario en Supabase (PostgreSQL), sin creación de cuenta

**Decisiones clave de producto:**

*Canal > feature:* WhatsApp tiene ~99% de penetración entre usuarios de smartphone en Brasil. Una mejor UX dentro de una nueva app no le gana a una UX razonable donde el usuario ya pasa horas. El costo de adquisición es cero; el cambio de comportamiento requerido es mínimo.

*LLM-as-a-Judge para calidad de output:* Las alucinaciones nutricionales (ej: "500g de pollo a la plancha = 200 kcal") destruyen la confianza más rápido que cualquier otro modo de falla. Una segunda pasada de evaluación captura outputs implausibles antes de que lleguen al usuario. Este es el mecanismo central de confiabilidad del producto — los usuarios dejan de usar una herramienta nutricional en el momento en que dejan de confiar en los números.

*Engagement proactivo > reactivo:* Las pruebas mostraron que los recordatorios contextuales en horario de comida superan esperar que el usuario registre espontáneamente. El disparador correcto es contextual (horario de comida), no arbitrario (notificación a las 9am). El engagement en el momento de la decisión — la comida — es lo que forma el hábito.

*Paridad texto-foto:* No toda comida se puede fotografiar (café a las 7am apurado, almuerzo en un cliente). El análisis por texto tiene que ser igual de preciso — si no, la ventaja del canal se rompe exactamente cuando el usuario más lo necesita.

*Cero cuenta:* La fricción de registro mata la conversión antes de que el usuario vea valor. La identidad en WhatsApp es la cuenta — sin auth separada necesaria.

**Métricas que se siguen:**
- **Retención:** usuarios activos D7 y D30 (métrica primaria de éxito)
- **Engagement:** comidas registradas por usuario activo por día (meta: ≥2)
- **Calidad:** tasa de rechazo del LLM-as-a-Judge (% de respuestas marcadas para re-generación)
- **Motor proactivo:** tasa de apertura de mensajes proactivos; conversión de mensaje proactivo a registro de comida en 30 min
- **Tasa de corrección:** % de comidas corregidas por el usuario (proxy de precisión del análisis)

**Estado:** MVP en vivo desde marzo de 2026 · Reclutando beta cerrado (10 usuarios) · Validando retención D7 antes de escalar

`Python · WhatsApp Cloud API · GPT-4o Vision · Supabase · Railway (CI/CD)`

---

### EstudaAI — Aprendizaje activo desde contenido pasivo

**El problema:** Los estudiantes de medicina tienen demasiado contenido y poco tiempo. El método de estudio estándar sigue siendo pasivo: leer, subrayar, repetir. El contenido existe — pero no está en un formato que produzca conocimiento durable. Crear materiales de estudio activo (flashcards, quizzes, resúmenes estructurados) desde PDFs crudos lleva horas que podrían ir a la práctica real.

**Job to be done:** *"Ayudame a sentirme preparado para el examen con el mínimo esfuerzo de mi parte."*

**Hipótesis:** Si la IA puede transformar automáticamente cualquier PDF en materiales de estudio activo — flashcards, quizzes, resúmenes estructurados —, los estudiantes se sentirán más preparados en menos tiempo y la brecha entre "tener el contenido" y "estar listo" colapsa.

**Qué se construyó:**
- Plataforma web donde los estudiantes suben cualquier PDF (capítulo de libro, artículo, apunte)
- **Pipeline RAG (Retrieval-Augmented Generation):** los documentos se dividen en chunks, se embedan y almacenan en una base de datos vectorial. La generación de cada tipo de output (flashcards, quiz, resumen) recupera los chunks semánticamente más relevantes por tema — garantizando cobertura de todo el documento y permitiendo targeting más preciso que enfoques naive de full-context
- Worker de procesamiento asíncrono — maneja documentos largos sin timeout ni bloqueo
- Generación automática de cuatro tipos de output por documento:
  - Resumen estructurado por tema
  - Sets de flashcards (pares pregunta/respuesta) sobre conceptos clave por chunk
  - Quiz de opción múltiple con clave de respuestas y explicaciones
  - Secuencia de estudio sugerida ordenada por complejidad de tema (derivada de clustering de similitud de embeddings)
- Arquitectura desacoplada: upload → cola → worker procesa → usuario notificado cuando está listo
- Worker en Cloudflare Workers — serverless, sin cold start para jobs asíncronos

**Decisiones clave de producto:**

*RAG en lugar de inyección full-context:* Pasar un PDF de 200 páginas completo en el contexto es caro, lento y produce outputs de menor calidad — el modelo atiende todo por igual. RAG con chunking + recuperación semántica garantiza que cada flashcard y pregunta de quiz esté fundamentado en la sección más relevante del documento. También habilita queries entre múltiples documentos en versiones futuras.

*Procesamiento asíncrono:* Los PDFs de medicina pueden tener cientos de páginas. El procesamiento síncrono genera timeouts y experiencia rota. El worker desacopla upload de entrega — el usuario sube, cierra la pestaña, recibe notificación cuando el material está listo.

*Cuatro formatos, no uno:* Momentos de estudio diferentes piden formatos diferentes. Repaso rápido → flashcard. Simulacro → quiz. Entender contexto → resumen. Planificación → secuencia por complejidad. Ofrecer los cuatro elimina el meta-trabajo de elegir la herramienta correcta para el momento correcto.

*Medicina como segmento de entrada:* Alto volumen de contenido, métodos de estudio sin cambios en décadas, alta disposición a pagar por herramientas que ahorran tiempo, usuarios motivados con necesidad clara y recurrente (exámenes). La plataforma generaliza a cualquier campo con alto volumen de contenido — medicina es la cabeza de playa.

*Claude API para generación:* La ventana de contexto larga procesa capítulos completos sin hacks de chunking en la etapa de generación. La calidad de generación de flashcards y quizzes es significativamente mejor que modelos GPT-3.5-class — y la calidad es el núcleo del producto; un flashcard incorrecto es peor que ningún flashcard.

**Métricas que se siguen:**
- **Tiempo-para-primer-valor:** tiempo desde el upload hasta el primer output listo (meta: <5 min para capítulo estándar)
- **Calidad de output:** tasa de precisión reportada por el usuario en flashcards y preguntas generadas (survey tras primera sesión)
- **Resultados de aprendizaje:** scores del quiz vs. confianza autoevaluada pre-quiz (mide si el material realmente ayuda)
- **Profundidad de engagement:** % de flashcards generados revisados; % del quiz completado (no solo abierto)
- **Tasa de retorno:** retorno D7 tras el primer upload (métrica primaria de retención)
- **Cobertura RAG:** % de secciones del documento representadas en los outputs generados (verificación automatizada contra chunks de origen)

**Estado:** En desarrollo activo desde febrero de 2026

`Next.js · TypeScript · Supabase · Claude API · Cloudflare Workers`

---

### Flight Monitor — Que te avisen. No busques vos.

**El problema:** Encontrar pasajes baratos requiere revisar sitios manualmente todos los días. El precio correcto aparece en ventanas cortas — a veces horas — y desaparece. Monitorear no crea ningún valor; es pura repetición mecánica de una verificación trivial: *¿bajó el precio de mi límite?*

**Job to be done:** *"Avisame cuando aparezca el vuelo correcto. No me hagas buscarlo."*

**Hipótesis:** Si el usuario puede definir un límite de precio y recibir un mail cuando se cumpla — sin necesidad de crear cuenta y con cancelación en un clic — la adopción será significativamente mayor que alertas que obligan a registrarse antes de mostrar valor.

**Qué se construyó:**
- Producto web donde cualquier persona define: aeropuertos de origen, destinos, ventana de fechas, duración mínima/máxima del viaje y precio máximo en reales brasileños
- Monitoreo automático diario de precios vía GitHub Actions cron job
- Alerta por mail con vuelos específicos encontrados, precios en BRL (con tipo de cambio + IOF aplicados) y links directos de compra
- Cancelación en un clic vía token UUID embebido en todos los mails — sin login, sin cuenta
- Expansión multi-aeropuerto automática: "São Paulo" monitorea automáticamente GRU + CGH + VCP — los usuarios piensan en ciudades, no en códigos IATA
- Tipo de cambio aplicado automáticamente: tasa diaria USD→BRL vía API abierta + 6,38% de IOF — el límite que define el usuario es el número que aparece en el resumen de tarjeta
- Creación de cuenta opcional: los usuarios que quieran gestionar múltiples alertas tienen acceso a un dashboard para ver, pausar y eliminar todas sus alertas activas en un solo lugar — la cuenta es opcional, no requerida para usar el producto
- Tabla de historial de alertas en Supabase con trazabilidad completa de notificaciones enviadas

**Decisiones clave de producto:**

*Sin cuenta = más conversión:* La fricción de registro mata la adopción antes de que el usuario vea valor. Los tokens de cancelación resuelven identidad sin auth — el usuario gestiona la alerta desde su bandeja de entrada. También elimina pedidos de soporte por contraseñas olvidadas.

*Precio en reales, costo final:* Los usuarios brasileños piensan en reales. Convertir USD manualmente agrega fricción cognitiva e imprecisión (subestiman el IOF). El producto aplica el tipo de cambio del día y el IOF automáticamente — el límite que define el usuario es el número que aparece en la factura.

*GitHub Actions en lugar de servidor continuo:* El monitor necesita correr una vez por día. Un servidor 24/7 para un job diario es desperdicio operacional puro. GitHub Actions lo resuelve con un cron job — cero costo de infraestructura, cero overhead de mantenimiento.

*Multi-IATA por ciudad:* Los usuarios no piensan en códigos de aeropuerto. El producto expande a todos los IATAs relevantes automáticamente y reporta por aeropuerto específico en el mail de alerta — especificidad en el output, simplicidad en el input.

**Métricas que se siguen:**
- **Tasa de creación de alertas:** % de visitantes que completan el formulario (conversión)
- **Tasa alerta→notificación:** % de alertas activas que dispararon al menos un mail (efectividad del producto — ¿estamos encontrando vuelos?)
- **Engagement de mail:** tasa de apertura y clics en el link de compra por mail de alerta enviado
- **Lifetime de la alerta:** días promedio que una alerta sigue activa antes de la cancelación (proxy de satisfacción)

**Estado:** En uso · 4 usuarios activos en fase de prueba · Validando tasa de conversión alerta→notificación

`Next.js · Supabase · Python · GitHub Actions · Vercel`

---

## Cómo uso IA en el trabajo de producto

La IA no está solo en los productos que construyo — está en cómo trabajo como PM.

| Etapa | Cómo la IA lo acelera |
|-------|----------------------|
| **Discovery** | Síntesis de investigación cualitativa a escala, identificación de patrones entre entrevistas, generación de explicaciones alternativas para comportamiento observado |
| **Encuadre del problema** | Framings alternativos, estreso de supuestos, identificación de cuáles hipótesis son más críticas de validar primero |
| **Generación de hipótesis** | Hipótesis explícitas en formato si/entonces/porque, métrica más sensible a cada hipótesis, levantamiento de edge cases antes de que lleguen a ingeniería |
| **Escritura de specs** | PRDs con criterios de éxito y modos de falla explícitos, edge cases que el equipo no consideró, requisitos cuestionados contra objetivos reales del usuario |
| **Análisis de datos** | Interpretación de embudo, análisis de cohorte, detección de anomalías, hipótesis de segmentación, diseño y lectura de experimentos |
| **Desarrollo** | Claude Code para implementación de MVPs — lanzo prototipos y herramientas internas sin esperar sprint de ingeniería |
| **Toma de decisión** | Razonamiento estructurado con supuestos explícitos, niveles de confianza y evaluación de reversibilidad antes de escalar a stakeholders |

---

*São Paulo, Brasil*
