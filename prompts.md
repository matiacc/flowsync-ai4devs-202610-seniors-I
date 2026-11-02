# Prompts

Aquí van **todos los prompts que lanzaste** para hacer el ejercicio, en el orden en que los
lanzaste, con el modelo y la herramienta de cada uno.

Esto no es papeleo. Lo que se revisa es **cómo pediste las cosas**, no solo lo que salió: un
resultado flojo con un prompt bueno y un resultado flojo con un prompt vago necesitan feedback
distinto, y sin este archivo no se distinguen.

## Cómo rellenarlo

- Un apartado `## Prompt N` por cada prompt.
- **Pega el prompt tal cual lo lanzaste**, dentro del bloque de código, aunque ocupe diez líneas
  y aunque tenga faltas. No lo reescribas para que quede bien: el que arreglaste mentalmente
  después no es el que lanzaste.
- Incluye también los que **no funcionaron**. Suelen ser los más útiles de leer.
- `Modelo` y `Herramienta` en todos. Si cambiaste de una a otra a mitad, se nota aquí.

Borra el ejemplo de abajo cuando escribas el primero.

---
Para todos los prompts:
**Modelo:** Sonnet 5.5
**Herramienta:** Claude Code

## Prompt 1

```
Necesito que me definas las capabilities del sistema, es decir las cosas que el producto ya sabe hacer hoy. En pocas lineas. Un listado de features de la aplicacion. Y tambien que me digas como es el modelo de datos actual. Un resumen de todo esto, nada muy detallado.
```

## Prompt 2

```
Quiero que FlowSync sea una herramienta para que los equipos remotos sepan en qué está trabajando cada uno sin tener que hacer reuniones de sincronización. Algo tipo tareas compartidas pero más en tiempo real y menos rollo que Jira.
Haceme las 5 preguntas antes de proponer un cambio, que más reducirían la incertidumbre sobre el problema, los usuarios y el alcance. 5 preguntas, una sola ronda, sin bajar al modelo de datos ni a endpoints.
```

## Prompt3

```
Con todo lo que definimos, armá el alcance del MVP en cinco bloques:
problema, usuarios, propuesta de valor, alcance y NO-alcance.
Sé agresiva recortando: preferí una vertical fina terminada a varias
cosas a medias. Justificá cada exclusión del NO-alcance. Es un
documento de producto: sin tablas de base de datos, sin endpoints,
sin arquitectura.
```


