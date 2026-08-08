# Intro-to-STM32

## Overview

This is an introduction to STM32 microcontroller programming project that demonstrates basic GPIO control and UART communication on the **STM32F103RB** microcontroller. The project implements an interactive LED control system triggered by a button press, with UART feedback messages.

## Project Details

### Target Microcontroller
- **Device**: STM32F103RB (ARM Cortex-M3)
- **Family**: STM32F1xx
- **Features**: 
  - 72 MHz system clock
  - 128 KB Flash memory
  - 20 KB SRAM
  - Multiple GPIO ports and peripherals

### Key Features

#### 1. **Button-Controlled LED**
- The user button (B1) on GPIO Port C, Pin 13 controls an LED on GPIO Port A, Pin 5
- **Button Press**: Turns LED OFF and sends UART message "Button Pressed! LED OFF"
- **Button Release**: Turns LED ON and sends UART message "Button Released! LED ON"
- **Debouncing**: 50ms software debounce delay to prevent false triggers

#### 2. **UART Communication**
- **Interface**: USART2 (PA2 TX, PA3 RX)
- **Baud Rate**: 115200
- **Configuration**: 8-bit data, 1 stop bit, no parity
- **Purpose**: Real-time status messages and debugging

#### 3. **System Clock Configuration**
- **Oscillator**: HSI (Internal High-Speed Oscillator)
- **PLL Multiplier**: 16x
- **System Clock**: 64 MHz (HSI ÷ 2 × 16)
- **APB1 Clock**: 32 MHz (HCLK ÷ 2)
- **APB2 Clock**: 64 MHz (same as HCLK)

## Project Structure

```
Intro-to-STM32/
├── Core/
│   ├── Inc/                          # Header files
│   │   ├── main.h                   # Main header with GPIO definitions
│   │   ├── stm32f1xx_hal_conf.h     # HAL configuration
│   │   └── stm32f1xx_it.h           # Interrupt handlers
│   ├── Src/                          # Source files
│   │   ├── main.c                   # Main application logic
│   │   ├── stm32f1xx_hal_msp.c      # HAL MSP (low-level) initialization
│   │   ├── stm32f1xx_it.c           # Interrupt service routines
│   │   ├── syscalls.c               # System calls for newlib
│   │   ├── sysmem.c                 # Memory management
│   │   └── system_stm32f1xx.c       # System initialization
│   └── Startup/                      # Startup assembly files
├── Drivers/
│   ├── CMSIS/                        # Cortex Microcontroller Software Interface
│   └── STM32F1xx_HAL_Driver/        # STM32F1xx HAL drivers
├── Debug/                            # Debug build outputs
├── .cproject                         # STM32CubeIDE C++ project configuration
├── .mxproject                        # STM32CubeMX project file
├── .project                          # Eclipse/STM32CubeIDE project file
├── blink the led.ioc                 # STM32CubeMX device configuration
├── blink the led Debug.launch        # Debug launch configuration
├── STM32F103RBTX_FLASH.ld           # Linker script for Flash memory
└── blink.elf                         # Compiled ELF binary

```

## Hardware Setup

### GPIO Configuration

| Pin | Port | Function | Mode | Direction |
|-----|------|----------|------|-----------|
| PA5 | GPIOA | LED Output | Output Push-Pull | Output |
| PC13 | GPIOC | Button Input | Input | Input |
| PA2 | GPIOA | UART TX | Alternate Function | Output |
| PA3 | GPIOA | UART RX | Alternate Function | Input |

### Connections
- **LED**: Connected to GPIO PA5 (active low - LED ON when pin is LOW)
- **Button**: Connected to GPIO PC13 (active low - pressed when pin is LOW)
- **UART TX**: PA2 (transmit to PC/terminal)
- **UART RX**: PA3 (receive from PC/terminal)

## How It Works

### Main Application Logic (main.c)

1. **Initialization**:
   - HAL library initialization
   - System clock configuration (64 MHz)
   - GPIO initialization
   - USART2 initialization

2. **Main Loop**:
   - Continuously reads the button state
   - Detects state changes (press/release)
   - Toggles LED based on button state
   - Sends status messages via UART
   - Implements debouncing to filter noise

3. **State Machine**:
   ```
   Button Pressed (LOW) → LED OFF (Pin HIGH) → Send "Button Pressed! LED OFF"
   Button Released (HIGH) → LED ON (Pin LOW) → Send "Button Released! LED ON"
   ```

## Development Tools

- **IDE**: STM32CubeIDE
- **Framework**: STM32 HAL (Hardware Abstraction Layer)
- **Compiler**: ARM GCC (arm-none-eabi-gcc)
- **Debugger**: ST-Link/V2 or compatible
- **Configuration Tool**: STM32CubeMX

## Building and Flashing

### Prerequisites
- STM32CubeIDE installed
- STM32F103RB development board
- ST-Link debugger/programmer
- USB cable for power and debugging

### Build Steps
1. Open the project in STM32CubeIDE
2. Right-click project → Build Project
3. Wait for compilation to complete
4. Check the Console for build status

### Flashing Steps
1. Connect ST-Link debugger to board
2. In STM32CubeIDE: Run → Run Configurations
3. Select your device configuration
4. Click "Run" to flash the program

## Testing

### Via UART Terminal
1. Connect a USB-to-UART adapter to PA2 (TX) and PA3 (RX)
2. Open a serial terminal (PuTTY, Tera Term, Arduino IDE Serial Monitor)
3. Set baud rate to **115200**
4. Press the button and observe:
   - LED changes state
   - UART messages appear in terminal

### Expected Output
```
Button Pressed! LED OFF
Button Released! LED ON
Button Pressed! LED OFF
Button Released! LED ON
...
```

## Key Code Sections

### Main Loop (Core/Src/main.c)
- Reads button state
- Detects state changes
- Controls LED output
- Sends UART messages
- Implements 50ms debounce

### System Clock Configuration
- Enables HSI oscillator
- Configures PLL for 64 MHz
- Sets up HCLK and APB clocks

### GPIO Initialization
- PA5 as output (LED)
- PC13 as input (Button)
- PA2, PA3 as UART pins

### UART Configuration
- 115200 baud rate
- 8-bit word length
- 1 stop bit
- No parity

## Features & Extensions

### Current Features
✅ GPIO input/output control
✅ UART communication
✅ Button debouncing
✅ LED control logic
✅ Real-time feedback via serial

### Possible Extensions
- Add PWM for LED brightness control
- Implement interrupt-based button handling
- Add multiple buttons for different LED patterns
- Implement timer-based animations
- Add LCD/OLED display support
- Create a state machine for complex behaviors

## Troubleshooting

| Issue | Solution |
|-------|----------|
| LED doesn't turn on/off | Check PA5 pin connection and verify GPIO configuration |
| No UART output | Verify baud rate (115200), TX/RX connections, and USART2 initialization |
| Button not responsive | Check PC13 pin connection and test with different debounce value |
| Program doesn't flash | Verify ST-Link connection, try "Connect Under Reset" option |
| Compilation errors | Ensure all HAL drivers are properly included and CMSIS is available |

## References

- [STM32F103RB Datasheet](https://www.st.com/en/microcontrollers/stm32f103rb.html)
- [STM32CubeMX Documentation](https://www.st.com/en/development-tools/stm32cubemx.html)
- [STM32 HAL User Manual](https://www.st.com/)
- [STM32CubeIDE Documentation](https://www.st.com/en/development-tools/stm32cubeide.html)

## License

This project follows the license terms provided by STMicroelectronics for the STM32 HAL libraries.

## Author

**anassaahi** - Intro to STM32 Microcontroller Programming

---

