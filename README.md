# STM32-Based Cognitive Training Console

An STM32-based reaction-time console using physical buttons, LED cues, and an LCD for interactive gameplay.

**Platform:** STM32 NUCLEO-L432KC  
**Interfaces:** GPIO · Hardware timers · SPI · I²C  
**Firmware:** Embedded C · Finite state machines · Internal Flash  
**Status:** Planning and prototyping. Features below describe the intended implementation.

## Overview

This project focuses on developing firmware for a simple reaction-time game while integrating an SPI LCD, I²C-controlled LEDs, and nonvolatile score storage.

The initial version will support one game, basic display feedback, and a saved personal best.

## Gameplay

1. Press the button to start.
2. Wait for an LED cue after a randomized delay.
3. Press the button as soon as the LED turns on.
4. View the response time and personal best on the LCD.
5. Press the button to play again.

Pressing before the cue displays an early-press message and restarts the round. A response timeout ends the round without saving a score.

## Hardware

- **STM32 NUCLEO-L432KC:** Runs the game firmware.
- **SPI LCD:** Displays instructions, response times, and the best score.
- **Push button:** Connected to GPIO for starting rounds and responding to cues.
- **Cue and status LEDs:** Controlled through an I²C GPIO expander.
- **USB power:** Powers the development board.

Component selections, wiring, and pin assignments will be documented during prototyping.

## Firmware Design

- **GPIO:** Reads button input with software debouncing.
- **Hardware timers:** Manage cue delays and measure response time.
- **Finite state machine:** Controls idle, waiting, cue, and result states.
- **SPI:** Updates the LCD with game instructions and results.
- **I²C:** Controls cue and status LEDs.
- **Internal Flash:** Stores the fastest valid response time and loads it at startup.

Flash writes occur after a round finishes and only when a new personal best is achieved. Display updates occur outside the response-timing window.

## Development Checklist

- [ ] Connect and test the button, LCD, and I²C-controlled LEDs.
- [ ] Implement button debouncing and timer-based response measurement.
- [ ] Build the reaction-time game state machine.
- [ ] Display instructions, early presses, timeouts, and results.
- [ ] Save and restore the personal best using internal Flash.
- [ ] Test repeated rounds, response timing, and score retention after a power cycle.
- [ ] Document wiring, setup instructions, and a gameplay demo.

## Scope

The first version is a Nucleo-based prototype with one reaction-time game. Additional games, advanced menus, custom PCBs, wireless connectivity, and an RTOS are outside the initial scope.

“Cognitive training” describes the game theme; no clinical assessment or demonstrated cognitive improvement is claimed.
