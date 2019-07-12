Almuerzo
========


La Vicky, nuestra chef, nos hace almuerzo todos los días. Pero no para todos. Hay 12 cupos diarios, y por lo general hay más de 12 personas que quieren almorzar.

¿Cómo se decide quiénes almuerzan cada día?

Con un bot que funciona en Slack. [Ham](https://github.com/platanus/lita-lunch-reminder).

En un principio se hacía un sorteo al azar entre todos los que querían almorzar. Después nos dimos cuenta que eso podía ser medio injusto para los que venían poco a la oficina. Lo ideal sería que cuando vinieran, pudieran almorzar con el resto. Así se logra más interacción entre el equipo.

En ese contexto se creó el “karma”. Piénsalo como “puntos”.

Todos los días, Ham te pregunta a las 2 o 3 pm si quieres almorzar al otro día en la oficina. Ahí tienes tres opciones: le dices "no", le dices "sí" sin apuesta, o le dices "sí" con una apuesta. Si le dices "sí" sin apuesta, por defecto apuestas 1 karma para el almuerzo de mañana. Si le dices "sí, apuesto X", vas a estar apostando `X`.

A las 8 pm aprox. se hace el sorteo de los almuerzos del día siguiente.

El sorteo tiene la siguiente lógica:

- Se van llenando los cupos por orden de apuesta.
- Si hay N personas que apostaron X karma, y no quedan cupos para todos, entonces se sortean los cupos restantes entre ellos.

Para explicar de manera más fácil cómo funciona, imaginemos que no son 12 cupos, si no que 5. Por ejemplo:

- Las apuestas fueron: 1, 2, 1, 3, 1, 1, 2, 1. Como son 5 cupos y hay 8 personas apostando, hay que hacer el sorteo.
- Se ordenan por apuesta: 3, 2, 2, 1, 1, 1, 1, 1.
- Se llena el primer cupo con el que apostó 3.
- Se llena el segundo y tercer cupo con los que apostaron 2.
- Hay 5 personas que apostaron 1, pero solo quedan 2 cupos. Por lo tanto, se sortean esos dos cupos entre las 5 personas.

Si quedas en el almuerzo, se te descuenta lo que apostaste. Si no quedas en el almuerzo, no se te descuenta nada.

Se puede comprar y vender almuerzos. Si alguien quedó en el almuerzo y se da cuenta que no puede almorzar, puede decirle a ham “vendo almuerzo”. El primero que llegue a comprarlo se lo lleva. Lo mismo al revés. Puedo decirle “compro almuerzo”. Luego, si alguien vende su almuerzo, se lo compro automáticamente. Se respeta el orden de llegada.

Todo el karma que se apuesta no se va gastando. Ham lo va recolectando. Cada 30 días Ham lo redistribuye entre todos. Pero ojo! Hay un límite de riqueza de 40 karmas. Esto es para incentivar que la gente gaste su karma almorzando en la oficina. Si tienes 40 o más, Ham no te va a repartir karma, se va a repartir solo entre los más pobres.

## Comandos útiles

```
@ham compro almuerzo
@ham vendo almuerzo
@ham quienes almuerzan hoy
@ham quienes cooperaron con el almuerzo hoy
@ham cuanto karma tiene @pepito
@ham cuanto karma tengo
```