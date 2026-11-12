# Handoff

Repositorio nuevo (2026-11-12). Qué es y de dónde viene: [README.md](README.md).

## Estado

Nada construido todavía. El paso 1 es la medición descrita abajo.

## Paso 1: ¿da el SH-4 para emular V60 + TGP?

La pregunta que decide el proyecto. Model 1 corre a 16 MHz el V60 y a 16 MHz el TGP; la NAOMI tiene un
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
