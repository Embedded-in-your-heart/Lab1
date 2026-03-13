# Embedding Lab - Lab 1: FreeRTOS Synchronization & Communication

This project demonstrates the use of **FreeRTOS** on the **STM32L475 Discovery Kit (B-L475E-IOT01A)**. It showcases core RTOS concepts including task management, inter-task communication via message queues, and synchronization using mutexes and semaphores.

## Project Overview

Lab 1 implements a system where user input (button presses) and periodic timers control an LED. The logic is distributed across multiple concurrent tasks to ensure responsiveness and proper resource management.

### Key Features
- **Deterministic Button Handling**: Uses EXTI interrupts and a message queue to process short and long button presses.
- **Resource Protection**: A **Mutex** (`ledMutex`) protects the LED (LD2) from concurrent access by different tasks.
- **Periodic Execution**: A **Software Timer** and **Semaphore** (`timerSem`) trigger a background task at a fixed 10-second interval.
- **Inter-Task Communication**: A **Message Queue** (`buttonQueue`) decouples the interrupt service routine from the processing logic.

## System Architecture

### Tasks & Threads
1.  **`defaultTask` (Priority: Normal)**:
    - Waits for messages from `buttonQueue`.
    - Controls LED2 patterns based on button press duration:
        - **Short Press (< 1s)**: Toggles LED2 at 1Hz for 5 seconds.
        - **Long Press (≥ 1s)**: Toggles LED2 at 10Hz for 5 seconds.
2.  **`myTask02` (Priority: High)**:
    - Waits for `timerSem`.
    - Toggles LED2 at 10Hz for 2 seconds every synchronization interval.

### RTOS Objects
- **Mutex (`ledMutex`)**: Ensures that only one task can control the LED at a time, preventing flickering or inconsistent states.
- **Semaphore (`timerSem`)**: Binary semaphore released by the `PeriodicTimerCallback` to notify `myTask02`.
- **Message Queue (`buttonQueue`)**: Stores button press events (short/long) for the `defaultTask`.
- **Software Timer (`periodicTimer`)**: Configured for a 10,000ms (10s) period.

## Hardware Requirements
- **Board**: STM32L475E-IOT01A (Cortex-M4)
- **Peripherals Used**: 
  - GPIO (LED2, User Button PC13)
  - USART1/3 (Debugging/Tracing)
  - DFSDM, I2C, SPI, QSPI (Configured but idle)

## How to Build & Flash

1.  **Environment**: Open the project in **STM32CubeIDE**.
2.  **Configuration**: Double-click `lab1.ioc` if you need to modify peripheral settings (STM32CubeMX).
3.  **Build**: Click the **Build** (Hammer icon) or press `Ctrl+B`.
4.  **Flash**: Connect your board via USB and click **Run** (Play icon).

## Usage
- **Blue User Button (PC13)**:
  - **Press briefly**: LED2 will blink slowly for 5 seconds.
  - **Hold for > 1 second**: LED2 will blink rapidly for 5 seconds.
- **Automatic Pattern**: Every 10 seconds, the high-priority task will override the LED to perform a brief rapid blink.

---

*This lab is part of the Embedding Lab curriculum, focusing on real-time embedded systems development.*
