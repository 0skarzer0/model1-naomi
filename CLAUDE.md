# model1-naomi — instrucciones para Claude

## Qué es
Estudio para llevar **Virtua Racing** (Sega Model 1) a la **NAOMI 2** del mueble Crazy Taxi. Estado: midiendo si es viable; aún no hay nada jugable.

## Qué leer primero
1. [HANDOFF.md](HANDOFF.md): lo averiguado, caminos y decisiones.
2. [README.md](README.md): por qué es difícil.

## Reglas que no se saltan
- **Los juegos del usuario (ROMs, discos, imágenes) y su `info.txt` están en `C:\Users\Usuario\Documents\dc2naomi` y no se suben nunca a GitHub** (ni se copian al repo, ni se citan sus datos en commits o documentos). Tampoco BIOS ni binarios que lleven ROMs dentro.
- **Las contraseñas van en variables de entorno** (la de las Raspberry, `PIPASS`), **nunca en un archivo** del repo ni en un commit.
- "Probado" = funciona en la placa real; Flycast es solo el primer paso.
- Raspberry (WiPi Netbooter): mueble Crazy Taxi **192.168.68.251** (los documentos antiguos dicen **.60**), mueble estándar **192.168.68.250** (antes .59).
- Al terminar: HANDOFF y docs al día, commit y push.

# Equipo de agentes (Claude + Gemini)

Este proyecto puede usar Gemini vía Antigravity CLI (`agy`) como ayudante para ahorrar tokens.
- Activa el modo equipo con `/equipo <tarea>`; las reglas completas están en `.claude/commands/equipo.md`.
- Encargos y respuestas: `.agy-tasks/` (plantilla en `_plantilla.md`). Script: `scripts/agy-task.ps1` (estado del equipo en `.agy-tasks/_estado.md`).
- `agy` debe usar la API key de Google AI Studio (`GEMINI_API_KEY`), nunca el login de la suscripción.
- Los ayudantes solo leen; los cambios en archivos los hace Claude tras revisar.
