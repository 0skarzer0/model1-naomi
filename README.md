# Sega Model 1 en placa NAOMI

Trabajo para llevar **Virtua Racing** (Sega Model 1, 1992) a una placa **Sega NAOMI 2** con NetDIMM,
dentro de un mueble Crazy Taxi con volante. Índice general de ese mueble y de los demás juegos:
[0skarzer0/naomi-wheel-games](https://github.com/0skarzer0/naomi-wheel-games).

Estado: **midiendo si es viable**. Todavía no hay nada que se pueda jugar.

## Por qué no es sencillo

| | Sega Model 1 (1992) | Sega NAOMI 2 (2000) |
| --- | --- | --- |
| CPU principal | NEC V60 @ 16 MHz | Hitachi SH-4 @ 200 MHz |
| Geometría | Fujitsu MB86233 (TGP) @ 16 MHz | la hace la CPU (el PowerVR2 no transforma vértices) |
| Sonido | 68000 + YM3438 + 2× MultiPCM | ARM7 + AICA |
| Dibujo | polígonos planos, sin texturas | PowerVR2 |

Emular Model 1 pide emular **dos** procesadores (V60 y TGP) y, además, hacer toda la geometría en la
misma CPU, porque el PowerVR2 solo rasteriza. La NAOMI tiene un único núcleo a 200 MHz. A favor:
el SH-4 trae instrucciones de matrices muy rápidas (`FTRV`, `FIPR`), Virtua Racing son polígonos
planos sin texturas (barato para el PowerVR2), y del port nativo de Daytona ya tenemos resueltos el
sonido por AICA, los controles JVS, el guardado en SRAM y la carga desde la NetDIMM
([daytona-arcade-recomp, rama naomi-native](https://github.com/0skarzer0/daytona-arcade-recomp/tree/naomi-native)).

## Lo que ya existe (noviembre de 2026)

Nada de esto es nuestro; se cita aquí porque es el punto de partida y porque sin ello no habría proyecto.

- **[frangarcj/geometrizer](https://github.com/frangarcj/geometrizer)** — emulador de Model 1 en C11
  centrado en Virtua Racing, para macOS y PS Vita. Trae V60 y TGP con intérprete y recompilador
  dinámico, sonido completo y un renderizador que en Vita mete la geometría en *shaders*. Es la base
  candidata. **No publica licencia**, así que antes de construir nada encima hay que pedir permiso.
- **[matiaszanolli/sega-vr-disasm](https://github.com/matiaszanolli/sega-vr-disasm)** — descompilación
  completa y compilable de *Virtua Racing Deluxe* de 32X (código SH-2). Plan B: el SH-2 es de la misma
  familia que el SH-4 de la NAOMI.
- **alphanu1** — núcleo FPGA de Model 1 (MiSTer) al 99% de velocidad, y la recompilación de Daytona USA
  de la que salió nuestro port anterior.
- **wanszai** — emuladores de Virtua Racing para PC y Xbox; solo binarios, sin código.
- **MAME** — la referencia de documentación del hardware de Model 1.

## Plan

1. **Medir antes de portar.** Compilar solo el V60 y el TGP para SH-4 y ejecutarlos en la placa con una
   carga de prueba, para saber cuántas instrucciones por segundo da. De ahí sale si el camino es viable.
2. Si lo es: renderizador PowerVR, sonido por AICA, controles JVS, empaquetado para la NetDIMM.
3. Si no lo es: plan B (32X) o esperar a una recompilación estática de Model 1.

Las mediciones se harán en la placa real leyendo su memoria por la NetDIMM, como en el Daytona.

## Documentación

- [HANDOFF.md](HANDOFF.md) — estado al día, para retomar el trabajo (persona o IA).
