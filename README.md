# JACK GRAVITY

Videojuego desarrollado en lenguaje Jack sobre la arquitectura Hack (plataforma de 16 bits del ecosistema Nand2Tetris). Demuestra que es posible construir una experiencia jugable fluida, con física calculada en tiempo real, incluso con los recursos mínimos de una CPU didáctica.

## Concepto

El jugador controla un cuerpo que orbita de forma constante alrededor de un núcleo central fijo en la pantalla. Desde los bordes convergen fragmentos hacia ese núcleo, y la supervivencia depende de desplazarse por la órbita para esquivarlos a tiempo.

**Ambientación:** la partida se desarrolla en las profundidades de un abismo oceánico. El núcleo actúa como el ojo de un remolino abisal y los fragmentos son escombros arrastrados por la corriente. Anillos de corriente concéntricos se contraen constantemente hacia el centro, simulando la succión del vórtice.

## Mecánicas

- Movimiento orbital continuo del jugador alrededor del núcleo, controlado con flecha izquierda/derecha, con captura de teclado en tiempo real sin pausas bloqueantes.
- Escombros dirigidos: aparecen apuntando a la posición actual del jugador, cada uno a una velocidad distinta y en momentos impredecibles (probabilidad aleatoria por frame, no un reloj fijo).
- Partículas latentes: aparecen quietas en pantalla, esperan un tiempo al azar y luego caen sin previo aviso a una velocidad también aleatoria.
- Detección de colisión angular (ver sección siguiente): si un elemento cruza la órbita a ±1 posición del jugador, termina la partida; si no, se esquivó y suma al marcador.
- Dificultad progresiva: la velocidad de caída y la frecuencia de aparición aumentan con el tiempo de juego.
- Condición de victoria: alcanzar 15 puntos termina la partida con un mensaje de triunfo.

## Condición de colisión

La colisión no se calcula por contacto en píxeles, sino en **unidades angulares**. El vórtice se divide en 24 posiciones (una cada 15°) y un escombro o partícula se evalúa en el momento en que cruza la órbita del jugador (radio 80, con una ventana de 77 a 83):

- **Choque (fin de la partida):** si en ese cruce la distancia angular al jugador es de **0 o 1 posición** (3 posiciones de peligro en total).
- **Esquivado (+1 punto):** si la distancia angular es de **2 o más posiciones**.

La distancia angular es **circular**: las posiciones 23 y 0 se consideran vecinas.

Esto es una decisión de diseño, no una limitación:

- **Dificultad sin aumentar la velocidad.** Con una zona de un solo slot, esquivar sería trivial (un paso y listo). Con tres, el jugador debe anticipar el escombro y moverse con antelación.
- **Campo de influencia.** Temáticamente, el planeta arrastra con su gravedad lo que pasa cerca, por lo que la zona de peligro es más ancha que su sprite.
- **Coherencia con el sistema.** Posiciones y colisiones usan la misma unidad discreta (24 ángulos), lo que evita cálculos de distancia en píxeles que serían costosos para la plataforma Hack.

## Cumplimiento de requisitos técnicos

| Requisito | Implementación |
|---|---|
| Captura de eventos en tiempo real sin bloqueo | `Keyboard.keyPressed()` dentro del bucle principal, sin lecturas bloqueantes |
| Actualización activa / desplazamiento visual continuo | Anillos de corriente que se contraen cada frame, simulando el vórtice |
| Cinemática calculada matemáticamente | Posición orbital vía tabla de seno/coseno (Jack no tiene trigonometría nativa); trayectorias radiales de los fragmentos |
| Marcador dinámico | Actualizado en cada esquive exitoso, visible durante toda la partida |

## Diferenciador

A diferencia de la mayoría de los proyectos Jack existentes (con movimiento rectilíneo sobre un solo eje y fondo estático), JACK GRAVITY introduce movimiento orbital calculado matemáticamente, combinado con fondo animado y múltiples tipos de amenaza (dirigidas y latentes) conviviendo en la misma partida.

## Estado del proyecto

Funcional.

## Cómo compilar y ejecutar

Requiere las herramientas oficiales de Nand2Tetris.

1. Coloca `Main.jack` en una carpeta propia.
2. Compílalo con `JackCompiler` (genera `Main.vm`).
3. Abre esa carpeta en el `VM Emulator` y ejecuta.

**Controles:** flecha izquierda / flecha derecha para moverte por la órbita.

## Estructura del repositorio

```
.
├── Main.jack
└── README.md
```
