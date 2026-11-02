# Alcance MVP FlowSync

> Documento de producto: sin tablas de base de datos, sin endpoints, sin arquitectura.
> Si aparece algo de eso, subilo de nivel o sacalo.

---

## 1. El terreno que ya existe

Hoy el producto es solo una base de autenticación. Todavía no tiene nada de gestión de tareas.

- Registro de usuario con email, contraseña y nombre completo opcional. Valida que el email sea único y que la confirmación de contraseña coincida.
- Login que devuelve un access token opaco.
- Logout, que revoca el token.
- Perfil del usuario autenticado (GET /account/profile).
- Frontend con tres pantallas: login, registro y perfil. Tiene rutas protegidas y rutas solo para anónimos. El token se guarda en localStorage y se rehidrata contra el perfil al arrancar. Los errores de la API se muestran en castellano y por campo.

Modelo de datos

Hay dos tablas, y la única relación es que un token pertenece a un usuario.

- users: id, email, fullName (opcional), password (hasheada, nunca se serializa), createdAt, updatedAt.
- auth_access_tokens: id, tokenableId (referencia al users.id), type, name, hash, abilities, lastUsedAt, expiresAt, createdAt, updatedAt.

No existen todavía entidades de equipos, proyectos ni tareas.

## 2. El interrogatorio

<!-- Una sola ronda. Las 5 preguntas que hizo la IA, con las respuestas
     (tomadas de la ficha de hechos). Si alguna no estaba cubierta,
     marcá el supuesto que declaró la IA. -->

Pregunta de la IA: ¿Quién es el usuario inicial y qué equipo tenés en mente?
Respuesta: Equipos remotos pequeños de 3 a 10 personas, con roles planos (todos ven y editan lo mismo). El primer caso es un
equipo de 6 personas de producto SaaS en 3 husos horarios. Hoy usa un gestor de tareas pesado y una daily de 15 minutos. Es un
 caso de estudio, no un cliente real. Hay un único espacio compartido y no existe la entidad "equipo". Varios equipos, o gente
 en más de uno, queda fuera del MVP.
¿Supuesto?: No
────────────────────────────────────────
#: 2
Pregunta de la IA: ¿Qué es "saber en qué está trabajando cada uno"? ¿Qué pregunta debería poder contestar un compañero en cinco
segundos?
Respuesta: "¿Quién está con qué y qué está libre?" El estado es de la tarea, no de la persona, y lo teclea quien hace la tarea.
No hay "quién está conectado" ni indicadores de actividad, y no se deriva de Git, PRs, CI ni calendario. Una tarea tiene
título, responsable, estado y fecha de vencimiento. La lista se filtra por estado. La decisión que cambia es no empezar algo
que otra persona ya está tocando y elegir lo siguiente sabiendo qué está libre. Caso concreto: dos personas tocaron el mismo
módulo la misma semana y perdieron dos días.
¿Supuesto?: No
────────────────────────────────────────
#: 3
Pregunta de la IA: ¿Qué significa "tiempo real" y qué tan en serio va? ¿Cuál es el mínimo que justifica la herramienta?
Respuesta: Ver los cambios de estado de las tareas sin refrescar ni preguntar. No es chat, ni videollamada, ni edición
simultánea de un documento. Es un resumen que espera, no un aviso que interrumpe: llegás por la mañana o volvés de una reunión
 y ves qué se movió. No hay notificaciones push. Si el beneficio fuera solo "sentirse informado", el tiempo real no valdría lo
 que cuesta.
¿Supuesto?: No
────────────────────────────────────────
#: 4
Pregunta de la IA: ¿Qué es "menos rollo que Jira" en concreto? ¿Qué es lo mínimo que no negociás?
Respuesta: Crear una tarea y cambiarle el estado en segundos, sin flujos de configuración ni campos obligatorios. Lo mínimo es
título, responsable, estado y fecha de vencimiento. Se renuncia a sprints, estimaciones, épicas, backlog priorizado e
informes. FlowSync sustituye al gestor de tareas, no convive con él. Crea sus propias tareas y no lee las de otro sitio, para
no obligar a una doble actualización. Se sostiene porque actualizar cuesta dos clics, y quien lo hace cobra en el momento: esa
 lista es su cola de trabajo y deja de recibir interrupciones.
¿Supuesto?: No
────────────────────────────────────────
#: 5
Pregunta de la IA: ¿Cómo sabrías que funciona y qué dejamos fuera de la primera versión?
Respuesta: A una semana de uso real, el equipo cancela la ronda de "¿en qué estás?" de la daily y nadie pide que vuelva. La
daily no desaparece entera: la parte de bloqueos sigue y este MVP no la resuelve. El riesgo #1 a validar es que la información
 se quede vieja. Queda fuera: integraciones (Git, Slack, calendario), OAuth de terceros, permisos por roles, varios equipos,
presencia, notificaciones y reportes. Se construye una vertical fina y terminada de punta a punta, no varias capabilities a
medias.
¿Supuesto?: Parcial. La lista de exclusiones sale de los hechos. Agrupar "app móvil" y "reportes" como fuera de alcance es
supuesto mío.

**Supuestos que declaró la IA (leerlos al terminar, ahí se cuelan los huecos):**
- Móvil: la app móvil queda fuera del MVP. La ficha no la menciona.
- Comentarios: una tarea no lleva comentarios ni etiquetas, porque la ficha solo pide título, responsable, estado y fecha de vencimiento.

---

## 3. El alcance

1. Problema

Los equipos remotos no pueden ver en qué está cada persona sin interrumpir a alguien. Hoy eso se resuelve con una daily de 15 minutos, donde la mitad se va en la ronda de "¿en qué estás?", y con preguntas constantes por Slack. El costo concreto: dos personas tocaron el mismo módulo la misma semana sin saberlo y perdieron dos días.

Los gestores de tareas pesados no lo resuelven. Tienen tanto proceso que el estado se queda desactualizado, y nadie confía en él.

2. Usuarios

- Quién: equipos remotos pequeños, de 3 a 10 personas, con roles planos. Todos ven y editan lo mismo.
- Quién cobra el valor: los pares, no un lead. Son los devs que descubren tarde que iban a lo mismo y quienes interrumpen a otros para preguntar. No hay reporte hacia arriba.
- Caso de estudio: un equipo de 6 personas de producto SaaS en 3 husos horarios. Hoy usa un gestor pesado y una daily por videollamada. No es un cliente real.
- No es nuestro usuario: quien necesita sprints, estimaciones, épicas o informes.

3. Propuesta de valor

Una lista compartida de tareas donde el estado de cada una se ve de un vistazo y se actualiza en dos clics. Sustituye al gestor de tareas, no convive con él.

- Para quien lo escribe: esa lista es su cola de trabajo. La mira para decidir qué coge y, de paso, deja de recibir interrupciones. Cobra en el momento.
- Para los demás: al llegar por la mañana o volver de una reunión, ven qué se movió sin preguntar a nadie.
- Decisión que cambia: no empezar algo que otra persona ya está tocando, y elegir lo siguiente sabiendo qué está libre.
- "Tiempo real": es frescura de la tarea, no presencia de la persona. Los cambios se ven sin refrescar ni preguntar.

Criterio de éxito. A una semana de uso real, el equipo cancela la ronda de "¿en qué estás?" y nadie pide que vuelva. La daily no desaparece entera: la parte de bloqueos sigue y este MVP no la resuelve.

4. Alcance (lo que entra)

Una sola vertical, terminada de punta a punta: ver y mover el estado de las tareas del equipo.

- Cuenta: registro, login, logout y perfil. Ya existen y no se tocan.
- Un único espacio compartido. Todos los usuarios registrados ven y editan las mismas tareas.
- Crear tarea con cuatro datos: título, responsable, estado y fecha de vencimiento. Sin campos obligatorios más allá del título.
- Cambiar el estado de una tarea en dos clics.
- Ver la lista del equipo con responsable, estado y vencimiento. Las tareas pasadas de plazo se distinguen a simple vista.
- Filtrar por estado, para centrarse en lo pendiente.
- Frescura sin refrescar: los cambios de estado de otros aparecen en la lista sin recargar la página.

5. NO-alcance (lo que se queda fuera)

┌────────────────────────────────────────┬─────────────────────────────────────────────────────────────────────────────────┐
│               Exclusión                │                               Por qué se recorta                                │
├────────────────────────────────────────┼─────────────────────────────────────────────────────────────────────────────────┤
│ Entidad "equipo", varios equipos,      │ El MVP valida con un solo equipo. Multiequipo añade pertenencias y permisos sin │
│ gente en más de uno                    │  ayudar a probar la hipótesis. Queda anotado como supuesto.                     │
├────────────────────────────────────────┼─────────────────────────────────────────────────────────────────────────────────┤
│ Permisos y roles jerárquicos           │ Los roles planos son parte de la propuesta. Los permisos añadirían trabajo sin  │
│                                        │ cambiar la pregunta que queremos responder.                                     │
├────────────────────────────────────────┼─────────────────────────────────────────────────────────────────────────────────┤
│ Presencia ("quién está conectado") e   │ Es vigilancia y lo rechazamos a propósito. El estado es de la tarea, no de la   │
│ indicadores de actividad               │ persona.                                                                        │
├────────────────────────────────────────┼─────────────────────────────────────────────────────────────────────────────────┤
│ Notificaciones push y avisos           │ La señal es un resumen que espera, no una interrupción. Un aviso reintroduce    │
│                                        │ justo el problema que queremos quitar.                                          │
├────────────────────────────────────────┼─────────────────────────────────────────────────────────────────────────────────┤
│ Estado derivado de Git, PRs, CI o      │ Es otro producto, con integraciones y OAuth de terceros. El estado lo teclea    │
│ calendario                             │ quien hace la tarea.                                                            │
├────────────────────────────────────────┼─────────────────────────────────────────────────────────────────────────────────┤
│ Leer o sincronizar tareas de otro      │ Convivir exige doble actualización, que es como muere esta categoría. FlowSync  │
│ gestor                                 │ crea sus propias tareas.                                                        │
├────────────────────────────────────────┼─────────────────────────────────────────────────────────────────────────────────┤
│ Sprints, estimaciones, épicas, backlog │ Es el "rollo" de Jira que queremos evitar. Quien lo necesite no es nuestro      │
│  priorizado e informes                 │ usuario.                                                                        │
├────────────────────────────────────────┼─────────────────────────────────────────────────────────────────────────────────┤
│ Chat, videollamada y edición           │ "Tiempo real" aquí es ver cambios de estado, nada más.                          │
│ simultánea                             │                                                                                 │
├────────────────────────────────────────┼─────────────────────────────────────────────────────────────────────────────────┤
│ Comentarios y etiquetas en tareas      │ La ficha pide solo cuatro datos. Es supuesto nuestro, no un hecho.              │
├────────────────────────────────────────┼─────────────────────────────────────────────────────────────────────────────────┤
│ App móvil                              │ Supuesto nuestro. Una web usable cubre la validación y una app móvil duplica el │
│                                        │  trabajo de interfaz.                                                           │
├────────────────────────────────────────┼─────────────────────────────────────────────────────────────────────────────────┤
│ Resolver los bloqueos de la daily      │ El MVP quita la ronda de "¿en qué estás?" y no pretende reemplazar la           │
│                                        │ conversación sobre bloqueos.                                                    │
└────────────────────────────────────────┴─────────────────────────────────────────────────────────────────────────────────┘

Riesgo principal a validar

Si la información se queda vieja, el producto pierde el sentido. No se mitiga obligando a nadie a actualizar, sino haciendo que actualizar cueste dos clics. Es lo primero que hay que observar en el uso real del caso de estudio.

## Las tres líneas (Parte B)

**1. Los dos números** (tal cual salieron, sin redondear ni explicar)

- Cosas que propuso la IA meter en el alcance:
- Cosas que quedaron dentro después de mi recorte:

**2. Tres cosas que dejé fuera, y por qué** (qué hipótesis no ayudan a validar)

1.
2.
3.

**3. La exclusión de la que menos seguro estoy**

- Cuál es:
- Qué tendría que pasar para que entrara:
- Qué dos cosas se contradecían (lo que me pedían vs. lo que veía, lo barato vs. lo que valida, lo que enamora vs. lo que se sostiene):

<!-- Opcional: si la IA me señaló una incoherencia mía y tenía razón, anotala acá. -->
