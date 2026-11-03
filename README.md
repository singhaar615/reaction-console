# STM32-Based Cognitive Training Console

An interactive embedded game console exploring reaction timing, sequence memory, and event-driven firmware.

**Platform:** STM32 NUCLEO-L432KC  
**Interfaces:** GPIO · Hardware timers · SPI · I²C · SWD  
**Firmware:** Embedded C · Finite state machines · Interrupts · Internal Flash  
**Status:** Planning and prototyping. Specifications are design targets, not measured results.

## Overview

This project combines physical controls, LED cues, and an LCD interface into a tabletop console with three games and persistent personal bests.

It builds on the display and hardware integration work from my STM32 oscilloscope project, adding game logic, timestamped input handling, and Flash storage.

“Cognitive training” describes the game theme; the project does not assess clinical cognitive function or claim demonstrated cognitive improvement.

## Game Modes

| Mode | Gameplay | Scoring |
| --- | --- | --- |
| **Reaction Time** | Respond to LED 1 after a random 2–5 second delay. Complete five attempts with a 2-second response limit. | Displays fastest and mean valid response times. A personal best requires five valid attempts and uses the session mean. |
| **Go / No-Go** | Complete 20 trials: 14 Go cues requiring button 1 and six No-Go cues requiring no response. Each has a 1-second response window. | Ranked by accuracy, then mean correct-Go response time when available. |
| **Sequence Memory** | Repeat LED sequences that grow from three to 12 positions. Each cue lasts 500 ms with a 250 ms gap. | Longest completed sequence. A wrong entry or 5-second input timeout ends the session. |

Early presses and incorrect responses are penalized according to each game's rules. Invalid Reaction Time attempts consume an attempt but are excluded from timing statistics.

## Hardware and Controls

- **STM32 NUCLEO-L432KC:** Main controller and debugging platform.
- **SPI graphical LCD:** Menus, instructions, progress, and results.
- **Four response buttons:** Connected directly to MCU GPIO for timestamped input.
- **Four cue LEDs:** Driven through a 3.3 V-compatible I²C GPIO expander.
- **Center button:** Start, confirm, and exit.
- **USB power:** Development-board supply.

The LCD variant, expander, pin assignments, pull-ups, and LED resistor values are pending final selection and verification.

| Context | Controls |
| --- | --- |
| Main menu | Button 1: previous; button 4: next; center: select |
| Instructions | Center: start; hold center: return |
| Gameplay | Buttons 1–4 respond to cues |
| Results | Center: replay; button 4: menu |
| Active game | Hold center for 1 second to abort without saving |
| Settings | View or reset records; reset requires confirmation |

## Firmware Design

A bare-metal cooperative event loop coordinates game states, input events, display updates, and storage.

- **Input handling:** Short GPIO interrupt handlers capture button edges and timestamps; debounce qualification runs outside interrupts.
- **Timing:** Hardware timers schedule cues and deadlines without blocking gameplay.
- **State machines:** Manage menus, preparation, cues, responses, results, and faults.
- **Display:** Event-driven SPI updates, with no full-screen redraw during timed response windows.
- **Cue control:** I²C transactions drive LEDs; failed transactions invalidate the affected trial.
- **Error handling:** Detect queue overflow, held buttons, invalid inputs, and communication failures.

Accepted button events retain their initial edge timestamps after debounce qualification. Cue latency and timestamp accuracy will be measured against physical LED and button edges.

## Design Targets

| Parameter | Target |
| --- | --- |
| Timer tick | 1 µs |
| Displayed response resolution | 1 ms |
| End-to-end timing error | ≤2 ms |
| Initial debounce qualification | 10 ms, adjusted after testing |
| Menu feedback | Within 100 ms of qualified input |
| Initial I²C clock | 100 kHz |
| Persistent storage | Personal best for each game |

Timer resolution does not guarantee measurement accuracy. These targets require bench validation.

## Persistent Scores

Personal bests will be stored in internal Flash and loaded at startup.

The planned storage design uses versioned records, integrity checks, commit markers, and two reserved erase units to support recovery from interrupted writes. Writes occur outside timed gameplay, only for improved records or confirmed resets.

Power-cycle retention, interrupted-save recovery, and behavior during firmware reprogramming will be tested and documented.

## Planned Repository Structure

| Folder | Contents |
| --- | --- |
| `firmware/` | CubeMX configuration, C source, linker files, and dependencies |
| `hardware/` | Prototype schematic, pin map, and BOM |
| `docs/` | Architecture, build guide, and design decisions |
| `tests/` | Game-logic tests, hardware results, and analyzer captures |
| `media/` | Prototype photos, UI images, and gameplay video |

## Development Checklist

- [ ] Finalize components, pin assignments, and electrical compatibility.
- [ ] Bring up the LCD, buttons, and cue LEDs.
- [ ] Implement timestamps, debouncing, event handling, and state machines.
- [ ] Complete and validate all three games.
- [ ] Implement Flash records and test interrupted-save recovery.
- [ ] Verify timing accuracy, timer rollover, and interface behavior.
- [ ] Test communication faults, queue overflow, and repeated sessions.
- [ ] Publish wiring, reproducible build instructions, results, and a demo.

The first release focuses on a Nucleo-based prototype. Custom PCB fabrication, wireless connectivity, mobile apps, and an RTOS are outside scope.

## References and Attribution

Hardware documentation and tool versions will accompany the implementation. Reused LCD code, third-party drivers, and generated STM32 support files will be identified, with original attribution and license notices preserved.
