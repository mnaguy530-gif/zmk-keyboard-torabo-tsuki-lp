# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a ZMK (Zephyr Mechanical Keyboard) firmware project for the **torabo-tsuki LP** split keyboard with trackball functionality. The project builds multiple firmware variants using ZMK's build system and includes advanced power management features.

## Commands

### Building Firmware
The project uses ZMK's build system with predefined configurations in `build.yaml`:

```bash
# Build all firmware variants
zmk build

# Build specific variant (examples):
zmk build -p torabo_tsuki_lp_left_central
zmk build -p torabo_tsuki_lp_right_peripheral
zmk build -p torabo_tsuki_lp_double_ball_right_central
```

### Development Environment
The project includes a Docker development environment:

```bash
# Build and run the development container
docker-compose up -d
docker exec -it claude-codebox-for-trabo-tsuki-lp bash
```

### Dependency Management
```bash
# Update ZMK and component dependencies
west update

# Initialize workspace (if needed)
west init -l config
```

## Architecture

### Split Keyboard Design
- **Left/Right Halves**: Separate firmware builds with central/peripheral roles
- **Communication**: Bluetooth-based split communication between halves
- **Role Assignment**: One side acts as central (USB/Bluetooth host), other as peripheral

### Build Variants
The `build.yaml` defines 7 firmware variants:
1. `torabo_tsuki_lp_left_central` - Left side as central
2. `torabo_tsuki_lp_right_peripheral` - Right side as peripheral  
3. `torabo_tsuki_lp_left_peripheral` - Left side as peripheral
4. `torabo_tsuki_lp_right_central` - Right side as central
5. `torabo_tsuki_lp_double_ball_left_peripheral` - Left with trackball, peripheral
6. `torabo_tsuki_lp_double_ball_right_central` - Right with trackball, central
7. `settings_reset` - Settings reset utility

### Power Management System
The custom power management in `src/board.c` implements a multi-stage sleep system:
- **SLEEP1** (5s idle): Reduces scan frequency
- **SLEEP2** (30s idle): Further power reduction
- **SLEEP3** (120s idle): Deep sleep mode
- **USB Power Detection**: Automatically adjusts power states based on USB connection
- **BLE Optimization**: Dynamic connection parameter adjustment

### Hardware Components
- **Board**: BMP Boost (RP2040-based with advanced power features)
- **Trackball**: PAW3222 sensor with dedicated driver
- **Features**: Status LED, CDC ACM bootloader trigger, non-LiPo battery management

### Keymap Structure
- **4 Layers**: Base QWERTY (0), Numbers/Nav (1), Symbols (2), Function keys (3)
- **Special Features**: Combo for Bluetooth clearing, mod-tap keys, mouse button support
- **Configuration**: Device tree format in `config/keymap.keymap`
- **Visual Editing**: Compatible with keymap-editor and ZMK Studio

### ZMK Dependencies
Custom components from sekigon-gonnoc:
- `zmk-component-bmp-boost`: Board support package
- `zmk-driver-paw3222`: Trackball sensor driver
- `zmk-feature-status-led`: LED status indicators  
- `zmk-feature-cdc-acm-bootloader-trigger`: Bootloader activation
- `zmk-feature-non-lipo-battery-management`: Battery monitoring

## Flashing Process
Per README instructions (Japanese):
- Flash `_central` firmware to the trackball side
- Flash `_peripheral` firmware to the opposite side
- Use keymap-editor or ZMK Studio for keymap modifications

## File Organization
- `config/`: ZMK workspace configuration and keymap
- `boards/shields/torabo_tsuki_lp/`: Hardware shield definitions
- `src/board.c`: Custom power management implementation
- `snippets/`: Trackball support modules
- `build.yaml`: Build target definitions
