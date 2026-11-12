# Handoff

Repositorio nuevo (2026-11-12). Qué es y de dónde viene: [README.md](README.md).

## Estado

Terreno estudiado (2026-11-12, madrugada). Todavía no hay nada construido, y la medición en placa está
pendiente, pero el estudio de `geometrizer` ya cambia bastante el panorama: ver abajo.

## Lo que se ha averiguado de geometrizer (2026-11-12)

Clonado y compilado su núcleo en Linux (no se redistribuye nada suyo). Lo relevante para la NAOMI:

**A favor.**
- El **TGP tiene emulación de alto nivel** (`src/machine/model1/tgp_hle_vr.c`): en vez de emular el
  microcódigo instrucción a instrucción, ejecuta manejadores en C que reproducen el mismo resultado,
  validados *bit a bit* contra el TGP emulado a bajo nivel. Eso quita de encima uno de los dos
  procesadores y deja la geometría como código C normal, que es justo lo que el SH-4 hace bien
  (`FTRV`, `FIPR`).
- El "DRC" del V60 **no es solo** código máquina: hay un decodificador de bloques a IR, con operandos
  predecodificados y análisis de banderas vivas, que es C portable.
- El código está organizado por capas (`src/hal`, `src/platform/{sdl,vita}`), así que añadir una capa
  para KallistiOS y PowerVR es trabajo acotado, no cirugía.

**En contra, y es serio.**
- El JIT del V60 usa **sljit** (505 referencias en `src/cpu/v60/v60_drc.c`), y **sljit no tiene soporte
  para SH-4**: x86, ARM, PowerPC, MIPS, RISC-V, s390x, LoongArch. Portar el camino rápido a la NAOMI
  significaría escribir un backend SH-4 para sljit, o un JIT propio V60→SH-4.
- Su propio documento de diseño (`docs/drc_v60_plan.md`) dice que, **con intérprete, no llegan a 60 Hz
  en PS Vita**, y que por eso planean el DRC. La Vita tiene un Cortex-A9 a 444 MHz, fuera de orden,
  con 32+32 KB de caché. La NAOMI tiene un SH-4 a 200 MHz, en orden, con menos caché.
- Además, en Vita **la geometría la hace la GPU** en un *vertex shader*. El PowerVR2 no puede: en la
  NAOMI esa carga vuelve a la CPU, encima de emular el V60.
- Estiman la ganancia de un DRC sobre un intérprete ajustado en **3-6×**.

**Lectura de todo esto (estimación, no medición).** Si un A9 a 444 MHz con la geometría descargada en
la GPU no llega a 60 Hz con intérprete, un SH-4 a 200 MHz que además tiene que hacer la geometría queda
lejos. Un DRC lo acercaría, pero hay que escribirlo desde cero para SH-4. No es imposible; es un
proyecto grande y con riesgo real de no llegar.

**Lo que falta para confirmarlo:** la medición en placa del paso 1. El propio proyecto reconoce que
tampoco tiene su número de referencia en Vita ("the Vita number is the only one that matters and we
don't have it yet"), así que aquí nadie tiene datos: hay que medir.

## Obstáculo de entorno

El contenedor donde se trabaja **bloquea ejecutar binarios compilados de código de terceros**, así que
la medición en el ordenador no se pudo hacer. Compilar sí se puede. La medición tendrá que hacerse
directamente en la placa NAOMI, que es de todas formas el número que importa.

## Caminos alternativos

1. **Recompilación estática de Model 1**, al estilo de lo que alphanu1 hizo con Model 2 para Daytona.
   Es el camino que de verdad funcionó la vez anterior: convierte el programa del V60 en código C, y
   de ahí se puede hacer un port nativo. alphanu1 además tiene un núcleo FPGA de Model 1, así que
   conoce la placa. Merece la pena preguntarle si lo tiene en mente.
2. **Virtua Racing Deluxe de 32X** (`matiaszanolli/sega-vr-disasm`): descompilación completa y
   compilable, código SH-2, misma familia que el SH-4. Ahí la NAOMI iría sobrada y podría incluso
   mejorar el original. No es el arcade, pero tiene más circuitos.

## Paso 1 (pendiente): ¿da el SH-4 para emular el V60?

La pregunta que decide el proyecto. Con el TGP resuelto por emulación de alto nivel, el V60 es el
tentpole. Model 1 corre a 16 MHz el V60 y a 16 MHz el TGP; la NAOMI tiene un
SH-4 a 200 MHz que además tendrá que hacer la geometría entera (el PowerVR2 no transforma vértices).

Medición propuesta:

1. Clonar `frangarcj/geometrizer` en local (sin redistribuir: no tiene licencia).
2. Aislar los intérpretes del V60 y del TGP, compilarlos con `kos-cc` para `KOS_SUBARCH=naomi`.
3. Ejecutar en la placa una carga sintética representativa (un fotograma de Virtua Racing: lo que el
   V60 y el TGP ejecutan entre dos *vblanks*) y contar instrucciones emuladas por segundo.
4. Comparar con lo que pide el original: 16 MHz × 2 procesadores, 60 fps.

Herramientas que ya tenemos del Daytona y sirven aquí tal cual:
- lectura de la placa en vivo por la NetDIMM (`naomi_dimmcomms.c` y `live.py` de
  `daytona-arcade-recomp`, rama `naomi-native`);
- empaquetado para la NetDIMM y arranque por el WiPi;
- toolchain: KallistiOS `368a2eb9` con `KOS_SUBARCH=naomi`, construida desde los tarballs de Ubuntu
  porque los servidores de GNU están bloqueados en el entorno de trabajo (receta en el HANDOFF de
  `daytona-arcade-recomp/platform/dreamcast-native/naomi/`).

## Pendiente antes de escribir código

- **Licencia de geometrizer**: abrir un issue preguntando al autor si permite un port y con qué
  condiciones. Sin respuesta, el proyecto se queda en medición privada.
- **ROM**: el set `vr` de MAME (Virtua Racing de Model 1), con las ROM de la placa de sonido.

## Decisiones tomadas

- Repositorio propio en vez de un *fork* de geometrizer, porque de momento aquí solo vive lo nuestro
  (banco de pruebas y documentación). Si el autor da permiso, ya se hará el *fork*.
