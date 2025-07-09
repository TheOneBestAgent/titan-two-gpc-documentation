# Titan Two GPC Understanding Guide
*Complete Documentation Based on Official ConsoleTuner Resources*

## Table of Contents
1. [Device Overview](#device-overview)
2. [GPC Language Reference](#gpc-language-reference)
3. [Complete Function Reference](#complete-function-reference)
4. [Real-World Script Examples](#real-world-script-examples)
5. [Development Environment](#development-environment)
6. [Advanced Features](#advanced-features)
7. [Best Practices](#best-practices)

---

## Device Overview

The **Titan Two** is an advanced gaming controller adapter/converter device manufactured by ConsoleTuner. It serves as an all-in-one gaming device that combines features of dozens of products in a single form factor.

### Key Features
- **Cross-Platform Compatibility**: Works with PS5, Xbox Series X|S, PS4, Xbox One, Nintendo Switch, and PC
- **Custom Scripting**: Full GPC2 programming language support
- **Ultra-Low Latency**: Less than 1ms input lag
- **Advanced MODs**: Built-in support for rapid fire, anti-recoil, macros, and more
- **Mouse & Keyboard Support**: Use K&M on consoles
- **Wireless Controller Support**: Connect any wireless controller to any console

### Hardware Specifications
- **Real CPU**: Atmel Microcontroller @ 16MHz
- **Real Memory**: 8,184 bytes
- **Real Flash**: 130,944 bytes
- **VM Max Bytecode**: 8,192 bytes (Titan Two) vs 4,096 bytes (Titan One)
- **VM Stack**: 256 bytes
- **VM Heap**: 1,024 bytes

---

## GPC Language Reference

### Basic Syntax

GPC (Game Pad Controller) is a C-like scripting language specifically designed for the Titan Two device. The language uses familiar C syntax with gaming-specific functions.

#### Program Structure
```gpc
// Optional includes and defines
#define BUTTON_A    PS4_CROSS
#define RAPID_FIRE  100

// Optional remapping
remap PS4_CROSS -> PS4_TRIANGLE;

// Optional data segment
data (
    10, 20, 30, 40,  // Array values
    50, 60, 70, 80
);

// Optional initialization
init {
    // Initialization code
}

// Main procedure (required)
main {
    // Main script logic
}

// Optional combos
combo RapidFire {
    set_val(PS4_R1, 100);
    wait(40);
    set_val(PS4_R1, 0);
    wait(40);
}

// Optional functions
function myFunction(param1, param2) {
    return param1 + param2;
}
```

### Variables, Data Types and Arrays

#### Data Types
- **int**: 32-bit signed integer (-2,147,483,648 to 2,147,483,647)
- **Arrays**: One-dimensional arrays of integers

#### Variable Declaration
```gpc
int variable_name;
int array_name[10];
int initialized_var = 100;
```

#### Special Constants
```gpc
FALSE:        0
TRUE:         !FALSE
NOT_USE:      0        // Used on sensitivity function
ALL_REMAPS:   -1       // Used on unmap statement
DZ_CIRCLE:    101      // Used on deadzone function
```

### Flow Control

#### if/else Statements
```gpc
if (condition) {
    // statements
} else if (other_condition) {
    // statements
} else {
    // statements
}
```

#### while Loops
```gpc
while (condition) {
    // statements
    if (break_condition) {
        break;
    }
}
```

### Operators

#### Arithmetic Operators
- `+` Addition
- `-` Subtraction
- `*` Multiplication
- `/` Division
- `%` Modulus

#### Comparison Operators
- `==` Equal to
- `!=` Not equal to
- `<` Less than
- `>` Greater than
- `<=` Less than or equal to
- `>=` Greater than or equal to

#### Logical Operators
- `&&` Logical AND
- `||` Logical OR
- `!` Logical NOT

#### Bitwise Operators
- `&` Bitwise AND
- `|` Bitwise OR
- `^` Bitwise XOR
- `~` Bitwise NOT
- `<<` Left shift
- `>>` Right shift

---

## Complete Function Reference

### I/O Functions

#### get_val(identifier)
Returns the current value of a controller entry.
```gpc
int button_value = get_val(PS4_CROSS);
if (get_val(PS4_R1) > 50) {
    // Right trigger is pressed more than halfway
}
```

#### set_val(identifier, value)
Sets the value of a controller entry.
```gpc
set_val(PS4_CROSS, 100);     // Press Cross button fully
set_val(PS4_LX, 50);         // Move left stick right halfway
```

#### get_lval(identifier)
Returns the previous value of a controller entry.
```gpc
if (get_val(PS4_CROSS) && !get_lval(PS4_CROSS)) {
    // Cross button was just pressed
}
```

#### event_press(identifier)
Returns TRUE if the button was just pressed.
```gpc
if (event_press(PS4_CROSS)) {
    // Cross button was just pressed this frame
}
```

#### event_release(identifier)
Returns TRUE if the button was just released.
```gpc
if (event_release(PS4_CROSS)) {
    // Cross button was just released this frame
}
```

#### get_ptime(identifier)
Returns how long a button has been pressed in milliseconds.
```gpc
if (get_ptime(PS4_CROSS) > 1000) {
    // Cross has been held for more than 1 second
}
```

#### block(identifier, time)
Blocks a button for a specified time in milliseconds.
```gpc
block(PS4_CROSS, 500);  // Block Cross button for 500ms
```

#### swap(identifier1, identifier2)
Swaps the values of two controller entries.
```gpc
swap(PS4_LX, PS4_RX);  // Swap left and right stick X axes
```

#### deadzone(x_axis, y_axis, x_deadzone, y_deadzone)
Applies deadzone to analog sticks.
```gpc
deadzone(PS4_LX, PS4_LY, 15, 15);  // 15% deadzone on left stick
```

#### sensitivity(axis, NOT_USE, sensitivity_value)
Adjusts sensitivity of an analog axis.
```gpc
sensitivity(PS4_RX, NOT_USE, 80);  // Reduce right stick X sensitivity to 80%
```

#### stickize(identifier)
Converts a button to behave like an analog stick.
```gpc
stickize(PS4_CROSS);  // Make Cross button analog
```

### Combo Functions

#### combo_run(combo_name)
Starts execution of a combo.
```gpc
combo_run(RapidFire);
```

#### combo_stop(combo_name)
Stops execution of a combo.
```gpc
combo_stop(RapidFire);
```

#### combo_running(combo_name)
Returns TRUE if a combo is currently running.
```gpc
if (combo_running(RapidFire)) {
    // Rapid fire is active
}
```

#### combo_restart(combo_name)
Restarts a combo from the beginning.
```gpc
combo_restart(RapidFire);
```

#### wait(time)
Pauses combo execution for specified milliseconds.
```gpc
combo AutoFire {
    set_val(PS4_R1, 100);
    wait(40);
    set_val(PS4_R1, 0);
    wait(40);
}
```

### Remapping Functions

#### remap source -> destination
Remaps one button to another.
```gpc
remap PS4_CROSS -> PS4_TRIANGLE;  // Cross acts as Triangle
```

#### unmap(identifier)
Removes mapping from a button.
```gpc
unmap PS4_CROSS;      // Disconnect Cross button
unmap ALL_REMAPS;     // Remove all remappings
```

### Math Functions

#### abs(value)
Returns absolute value.
```gpc
int result = abs(-50);  // Returns 50
```

#### pow(base, exponent)
Returns base raised to the power of exponent.
```gpc
int result = pow(2, 3);  // Returns 8
```

#### isqrt(value)
Returns integer square root.
```gpc
int result = isqrt(16);  // Returns 4
```

#### irand(max_value)
Returns random integer from 0 to max_value-1.
```gpc
int random_delay = irand(100);  // Random number 0-99
```

#### inv(value)
Returns inverted value (100 - value).
```gpc
int inverted = inv(30);  // Returns 70
```

### Data Segment Functions

#### data(...)
Defines read-only data array.
```gpc
data (
    10, 20, 30, 40,  // Rapid fire timings
    50, 60, 70, 80
);
```

#### dbyte(index)
Reads byte value from data segment.
```gpc
int timing = dbyte(0) * 10;  // Read first data value
```

#### dword(index)
Reads word value from data segment.
```gpc
int timing = dword(0);
```

#### dchar(index)
Reads character value from data segment.
```gpc
int char_val = dchar(0);
```

### LED Functions

#### set_led(led_id, state)
Controls device LEDs.
```gpc
set_led(LED_1, TRUE);   // Turn on LED 1
set_led(LED_2, FALSE);  // Turn off LED 2
```

#### get_led(led_id)
Returns current LED state.
```gpc
if (get_led(LED_1)) {
    // LED 1 is on
}
```

#### reset_leds()
Turns off all LEDs.
```gpc
reset_leds();
```

### Rumble Functions

#### set_rumble(motor, intensity)
Controls controller rumble.
```gpc
set_rumble(RUMBLE_A, 100);  // Full intensity on motor A
set_rumble(RUMBLE_B, 50);   // Half intensity on motor B
```

#### get_rumble(motor)
Returns current rumble intensity.
```gpc
int current_rumble = get_rumble(RUMBLE_A);
```

#### reset_rumble()
Stops all rumble.
```gpc
reset_rumble();
```

#### block_rumble(motor, time)
Blocks rumble on a motor for specified time.
```gpc
block_rumble(RUMBLE_A, 1000);  // Block motor A for 1 second
```

### Bit Operations

#### set_bit(variable, bit_position)
Sets a specific bit to 1.
```gpc
set_bit(my_var, 3);  // Set bit 3 to 1
```

#### clear_bit(variable, bit_position)
Sets a specific bit to 0.
```gpc
clear_bit(my_var, 3);  // Set bit 3 to 0
```

#### test_bit(variable, bit_position)
Tests if a specific bit is set.
```gpc
if (test_bit(my_var, 3)) {
    // Bit 3 is set
}
```

#### get_bits(variable, start_bit, num_bits)
Extracts multiple bits from a variable.
```gpc
int extracted = get_bits(my_var, 2, 4);  // Extract 4 bits starting at bit 2
```

#### set_bits(variable, start_bit, num_bits, value)
Sets multiple bits in a variable.
```gpc
set_bits(my_var, 2, 4, 15);  // Set 4 bits starting at bit 2 to value 15
```

### Persistent Variables

#### set_pvar(index, value)
Sets a persistent variable that survives script reloads.
```gpc
set_pvar(SPVAR_1, 100);  // Set persistent variable 1 to 100
```

#### get_pvar(index)
Gets a persistent variable value.
```gpc
int saved_value = get_pvar(SPVAR_1);
```

### Miscellaneous Functions

#### get_rtime()
Returns elapsed time since last iteration in milliseconds.
```gpc
int frame_time = get_rtime();
```

#### turn_off()
Turns off wireless controller.
```gpc
if (get_battery() < 2) {
    turn_off();
}
```

#### get_battery()
Returns battery level (0-10).
```gpc
int battery = get_battery();
```

#### get_console()
Returns connected console type.
```gpc
if (get_console() == PIO_PS4) {
    // PS4 is connected
}
```

#### get_controller()
Returns connected controller type.
```gpc
if (get_controller() == PIO_PS4) {
    // DualShock 4 is connected
}
```

#### get_slot()
Returns current memory slot number.
```gpc
int current_slot = get_slot();
```

#### load_slot(slot_number)
Loads script from specified memory slot.
```gpc
load_slot(2);  // Load script from slot 2
```

#### check_slot(slot_number)
Checks if a slot contains a valid script.
```gpc
if (check_slot(2)) {
    load_slot(2);
}
```

#### vm_tctrl(value)
Adjusts virtual machine timing.
```gpc
vm_tctrl(-2);  // Run VM 2ms faster (8ms instead of 10ms)
```

---

## Real-World Script Examples

### Example 1: Complete Battlefield 3 Script
*From official ConsoleTuner documentation*

```gpc
/* =====================================================================
 * BATTLEFIELD 3 - FRAG FX Script
 * 
 * System:      PS3
 * Controller:  Frag FX v1
 *  
 * Featuring:
 *  - Class Specific Rapidfire
 *  - Auto Spot
 *  - Bunny Hop
 *  - Force Sprint
 *  - Quick Scope
 *  - Gadget 1 Shortcut
 *  - Gadget 2 Shortcut
 */

/* ---------------------------------------------------------------------
 *  DEFINES
 */
#define MENU         = PS3_START;
#define SPOT         = PS3_SELECT;
#define SCOPE        = PS3_L1;
#define GRENADE      = PS3_L2;
#define CROUCH       = PS3_L3;
#define FIRE         = PS3_R1;
#define JUMP         = PS3_R2;
#define SPRINT_HOLDB = PS3_R3;
#define SIDEARM      = PS3_TRIANGLE;
#define INTERACT     = PS3_CIRCLE;
#define MELEE        = PS3_CROSS;
#define RELOAD       = PS3_SQUARE;
#define GADGET2      = PS3_RIGHT;
#define GADGET1      = PS3_LEFT;
#define FIREMODE     = PS3_DOWN;
#define LIGHT        = PS3_UP;
#define MAX_AIM      = 80;
#define QUICK_SCOPE  = 300;

/* ---------------------------------------------------------------------
 *  DATA SEGMENT
 */
data (
// HOL  REL  HOS  RES -------------------------------------------------
4,   4,   4,   4,     // 0: Optimized Rapidfire
20,   8,   4,  16,     // 1: Support
20,  10,   4,  12,     // 2: G36C + Foregrip             (Engineer)
20,   8,   4,   8,     // 3:                             (Any)
20,  14,   4,  14,     // 4: L86A2                       (Support)
4,   4,   4,  18      // 5: AN94
);

#define AUTO_SHOT = 0;

/* ---------------------------------------------------------------------
 *  REMAPPINGS
 */
remap PS3_R3       -> PS3_L3;
remap PS3_L3       -> PS3_R3;
remap PS3_R2       -> PS3_CROSS;
remap PS3_CROSS    -> PS3_R2;

/* ---------------------------------------------------------------------
 *  VARIABLES
 */
int mfspeed, autospot, bunnyhop_lock, tap;
int asHOL, asREL, asHOS, asRES;
int ashold, asrelease;
int autoShotMode = AUTO_SHOT;

/* ---------------------------------------------------------------------
 *  INITIALIZATION
 */
init {
    LoadAutoShot(autoShotMode * 4);
    ashold = asHOL;
    asrelease = asREL;
}

/* ---------------------------------------------------------------------
 *  MAIN SCRIPT
 */
main {
    /* Mouse Settings and Sensitivity */
    if(get_val(PS3_R3) && get_val(PS3_TRIANGLE)) {
        set_val(PS3_R3, 0);
        set_val(PS3_TRIANGLE, 0);
        swap(PS3_RX, PS3_LX);
        swap(PS3_RY, PS3_LY);
        mfspeed = TRUE;
    } else {
        if(get_val(PS3_RX) < 0) set_val(PS3_RX, get_val(PS3_RX) + 23);
        if(get_val(PS3_RX) > 0) set_val(PS3_RX, get_val(PS3_RX) - 24);
        if(get_val(PS3_RY) < 0) set_val(PS3_RY, get_val(PS3_RY) + 23);
        if(get_val(PS3_RY) > 0) set_val(PS3_RY, get_val(PS3_RY) - 24);
        
        if(!mfspeed) {
            if(get_val(PS3_L1)) {
                sensitivity(PS3_RX, NOT_USE, 68);
                sensitivity(PS3_RY, NOT_USE, 73);
            } else {
                sensitivity(PS3_RX, NOT_USE, 90);
                sensitivity(PS3_RY, NOT_USE, 95);
            }
            deadzone(PS3_RX, PS3_RY, 15, 15);
            
            if(get_val(PS3_RX) >  MAX_AIM) set_val(PS3_RX,  MAX_AIM);
            if(get_val(PS3_RX) < -MAX_AIM) set_val(PS3_RX, -MAX_AIM);
            if(get_val(PS3_RY) >  MAX_AIM) set_val(PS3_RY,  MAX_AIM);
            if(get_val(PS3_RY) < -MAX_AIM) set_val(PS3_RY, -MAX_AIM);
        } else {
            // Vehicles
            sensitivity(PS3_RX, NOT_USE, 115);
            sensitivity(PS3_RY, NOT_USE, 115);
            deadzone(PS3_RX, PS3_RY, 17, 17);
        }
    }
    
    if(event_press(PS3_CIRCLE)) mfspeed = FALSE;

    /* Change Primary Weapon Configuration */
    if(event_press(PS3_PS)) {
        autoShotMode = autoShotMode + 1;
        if(autoShotMode > 5) autoShotMode = 0;
        LoadAutoShot(autoShotMode * 4);
        if(get_val(PS3_L1)) { 
            ashold = asHOS; 
            asrelease = asRES; 
        } else { 
            ashold = asHOL; 
            asrelease = asREL; 
        }
    }
    block(PS3_PS, 1000);
    set_val(TRACE_6, autoShotMode);

    /* Auto Fire */
    if(event_press(PS3_L1)) { 
        ashold = asHOS; 
        asrelease = asRES; 
    } else if(event_release(PS3_L1)) { 
        ashold = asHOL; 
        asrelease = asREL; 
    }
    
    if(get_val(PS3_R1) && !mfspeed) {
        combo_run(AutoFire);
    } else {
        combo_stop(AutoFire);
        if(get_val(PS3_R1)) set_val(PS3_R1, 100);
    }

    /* Auto Spot */
    if(event_press(PS3_PS) || event_press(PS3_SELECT)) {
        autospot = FALSE;
    } else if(event_press(PS3_R3)) {
        autospot = TRUE;
    }
    
    if(autospot) combo_run(AutoSpot);
    else combo_stop(AutoSpot);

    /* Bunny Hop */
    if(bunnyhop_lock) {
        if(get_val(PS3_LY) > -75) {
            bunnyhop_lock = FALSE;
        } else {
            combo_run(EasySprint);
            combo_run(BunnyHop);
            set_val(JUMP, 0);
        }
    } else if(get_val(JUMP) && get_val(SPRINT_HOLDB)) {
        bunnyhop_lock = TRUE;
    } else {
        combo_stop(BunnyHop);
    }

    /* Force Sprint */
    if(event_press(SPRINT_HOLDB) && !combo_running(BunnyHop)) {
        combo_run(ForceSprint);
    }

    /* Hold Breath */
    if(get_val(SCOPE) == 100) {
        set_val(SPRINT_HOLDB, 100);
    }

    /* QuickScope */
    if(event_release(SCOPE) && get_ptime(SCOPE) < 140) {
        combo_run(QuickScope);
    }

    /* Gadget 1 Shortcut */
    if(event_press(PS3_L1) && get_val(PS3_R3)) {
        tap = GADGET1;
        combo_run(Tap);
    } else
    
    /* Gadget 2 Shortcut */
    if(event_release(PS3_START)) {
        if(get_ptime(PS3_START) < 1000) {
            tap = GADGET2;
            combo_run(Tap);
        } else {
            autospot = FALSE;
        }
    }
    block(PS3_START, 1000);
}

/* ---------------------------------------------------------------------
 *  COMBOS
 */
combo Tap {
    set_val(tap, 100);
    wait(60);
}

combo AutoFire {
    set_val(FIRE, 100);
    wait(ashold);
    set_val(FIRE, 0);
    wait(asrelease);
    set_val(FIRE, 0);
}

combo QuickScope {
    set_val(FIRE, 0);
    set_val(SCOPE, 100);
    set_val(SPRINT_HOLDB, 100);
    wait(QUICK_SCOPE);
    set_val(FIRE, 100);
    set_val(SCOPE, 100);
    set_val(SPRINT_HOLDB, 100);
    wait(40);
}

combo AutoSpot {
    set_val(SPOT, 100);
    wait(40);
    set_val(SPOT, 0);
    wait(960);
}

combo ForceSprint {
    set_val(PS3_LY, 0);
    wait(60);
}

combo EasySprint {
    set_val(SPRINT_HOLDB, 100);
    wait(100); 
    wait(100);
}

combo BunnyHop {
    set_val(JUMP, 100);
    wait(60); 
    wait(1000);
}

/* ---------------------------------------------------------------------
 *  FUNCTIONS
 */
function LoadAutoShot(idx) {
    asHOL = dbyte(idx)   * 10;
    asREL = dbyte(idx+1) * 10;
    asHOS = dbyte(idx+2) * 10;
    asRES = dbyte(idx+3) * 10;
}
```

### Example 2: Simple Rapid Fire Script
```gpc
// Simple rapid fire for R1 button
main {
    if(get_val(PS4_R1)) {
        combo_run(RapidFire);
    } else {
        combo_stop(RapidFire);
    }
}

combo RapidFire {
    set_val(PS4_R1, 100);
    wait(40);
    set_val(PS4_R1, 0);
    wait(40);
}
```

### Example 3: Anti-Recoil Script
```gpc
// Anti-recoil compensation
#define RECOIL_COMPENSATION 15

main {
    if(get_val(PS4_R1)) {
        // Apply downward compensation while firing
        set_val(PS4_RY, get_val(PS4_RY) + RECOIL_COMPENSATION);
    }
}
```

### Example 4: Button Remapping
```gpc
// Swap X and O buttons (Japanese vs Western layout)
remap PS4_CROSS -> PS4_CIRCLE;
remap PS4_CIRCLE -> PS4_CROSS;

main {
    // Main logic here
}
```

### Example 5: Turbo Mode Toggle
```gpc
int turbo_enabled = FALSE;

main {
    // Toggle turbo mode with L3+R3
    if(event_press(PS4_L3) && get_val(PS4_R3)) {
        turbo_enabled = !turbo_enabled;
        set_led(LED_1, turbo_enabled);
    }
    
    // Apply turbo to face buttons when enabled
    if(turbo_enabled) {
        if(get_val(PS4_CROSS)) combo_run(TurboX);
        else combo_stop(TurboX);
        
        if(get_val(PS4_SQUARE)) combo_run(TurboSquare);
        else combo_stop(TurboSquare);
    }
}

combo TurboX {
    set_val(PS4_CROSS, 100);
    wait(30);
    set_val(PS4_CROSS, 0);
    wait(30);
}

combo TurboSquare {
    set_val(PS4_SQUARE, 100);
    wait(30);
    set_val(PS4_SQUARE, 0);
    wait(30);
}
```

---

## Development Environment

### Gtuner IV Software
The official development environment for Titan Two scripting:

#### Key Features:
- **Syntax Highlighting**: Full GPC syntax highlighting and error detection
- **Real-time Monitoring**: Live controller input/output monitoring
- **Compiler**: Built-in GPC compiler with error reporting
- **Online Library**: Access to thousands of community scripts
- **Visual Scripting**: Drag-and-drop script creation
- **Device Management**: Firmware updates and device configuration

#### Installation:
1. Download Gtuner IV from ConsoleTuner.com
2. Install with administrator privileges
3. Connect Titan Two via USB
4. Update firmware if prompted
5. Start scripting!

### File Management
- **Script Files**: `.gpc` extension
- **Project Files**: `.gpp` extension (Gtuner Project)
- **Memory Slots**: 1-9 for device storage, 10 for "Build and Run"
- **Backup**: Always backup your scripts before major changes

---

## Advanced Features

### PlayStation 4 Specific Features

#### DualShock 4 Touchpad Support
```gpc
// Check touchpad touch points
if(ps4_touchpad(PS4T_P1)) {
    int x = ps4_touchpad(PS4T_P1X);
    int y = ps4_touchpad(PS4T_P1Y);
    // Process touch coordinates
}

// Simulate touchpad touch
combo TouchpadClick {
    ps4_set_touchpad(50, 50);  // Touch center
    set_val(PS4_TOUCH, 100);
    wait(100);
}
```

#### Authentication Timeout Handling
```gpc
// Monitor PS4 authentication timeout
if(ps4_authtimeout() <= 1) {
    output_reconnection();  // Force reconnection
}
```

### Multi-Platform Compatibility
```gpc
// Detect connected console
main {
    if(get_console() == PIO_PS4) {
        // PS4-specific code
    } else if(get_console() == PIO_XB1) {
        // Xbox One-specific code
    } else if(get_console() == PIO_SWITCH) {
        // Nintendo Switch-specific code
    }
}
```

### Memory Management
```gpc
// Use persistent variables for settings
init {
    // Load saved sensitivity setting
    int sensitivity = get_pvar(SPVAR_1);
    if(sensitivity == 0) {
        sensitivity = 100;  // Default value
        set_pvar(SPVAR_1, sensitivity);
    }
}
```

---

## Complete GPC Keywords Reference

The following 69 keywords have special meaning in GPC and cannot be used as variable names:

1. **abs** - Math Functions
2. **block** - I/O Functions
3. **block_rumble** - Rumble
4. **break** - Flow Control
5. **call** - Combos
6. **check_slot** - Misc Functions
7. **clear_bit** - Bit Operations
8. **combo** - Combos
9. **combo_restart** - Combos
10. **combo_run** - Combos
11. **combo_running** - Combos
12. **combo_stop** - Combos
13. **data** - Data Segment
14. **dbyte** - Data Segment
15. **dchar** - Data Segment
16. **deadzone** - I/O Functions
17. **define** - Basic Syntax
18. **dword** - Data Segment
19. **else** - Flow Control
20. **event_press** - I/O Functions
21. **event_release** - I/O Functions
22. **get_bits** - Bit Operations
23. **get_battery** - Misc Functions
24. **get_console** - Misc Functions
25. **get_controller** - Misc Functions
26. **get_ctrlbutton** - Misc Functions
27. **get_led** - LEDs
28. **get_ledx** - LEDs
29. **get_lval** - I/O Functions
30. **get_ptime** - I/O Functions
31. **get_pvar** - Persistent Variables
32. **get_rtime** - Misc Functions
33. **get_rumble** - Rumble
34. **get_slot** - Misc Functions
35. **get_val** - I/O Functions
36. **if** - Flow Control
37. **init** - Initialization
38. **int** - Variables, Types and Arrays
39. **inv** - Math Functions
40. **isqrt** - Math Functions
41. **irand** - Math Functions
42. **load_slot** - Misc Functions
43. **main** - Main Program
44. **output_protocol** - Misc Functions
45. **output_reconnection** - Misc Functions
46. **pow** - Math Functions
47. **ps4_authtimeout** - Misc Functions
48. **ps4_set_touchpad** - Misc Functions
49. **ps4_touchpad** - Misc Functions
50. **remap** - Remapping
51. **reset_leds** - LEDs
52. **reset_rumble** - Rumble
53. **sensitivity** - I/O Functions
54. **stickize** - I/O Functions
55. **set_bit** - Bit Operations
56. **set_bits** - Bit Operations
57. **set_led** - LEDs
58. **set_ledx** - LEDs
59. **set_pvar** - Persistent Variables
60. **set_rumble** - Rumble
61. **set_val** - I/O Functions
62. **swap** - I/O Functions
63. **test_bit** - Bit Operations
64. **turn_off** - Misc Functions
65. **unmap** - Remapping
66. **vm_tctrl** - Misc Functions
67. **wait** - Combos
68. **while** - Flow Control
69. **wiir_offscreen** - Misc Functions

---

## Best Practices

### 1. Code Organization
```gpc
// Use clear defines for better readability
#define FIRE_BUTTON     PS4_R1
#define AIM_BUTTON      PS4_L1
#define RAPID_FIRE_RATE 40

// Group related functionality
// Use meaningful variable names
int rapid_fire_enabled = FALSE;
int anti_recoil_strength = 15;
```

### 2. Performance Optimization
```gpc
// Minimize operations in main loop
main {
    // Cache frequently used values
    int fire_pressed = get_val(FIRE_BUTTON);
    int aim_pressed = get_val(AIM_BUTTON);
    
    // Use early returns when possible
    if(!fire_pressed && !aim_pressed) return;
    
    // Process only when needed
    if(fire_pressed) {
        combo_run(RapidFire);
    }
}
```

### 3. Memory Management
```gpc
// Use data segment for constant values
data (
    40, 20, 60, 30,  // Rapid fire timings
    15, 10, 25, 20   // Anti-recoil values
);

// Use persistent variables for settings
init {
    if(get_pvar(SPVAR_1) == 0) {
        set_pvar(SPVAR_1, 100);  // Default sensitivity
    }
}
```

### 4. Error Handling
```gpc
// Check slot validity before loading
if(check_slot(2)) {
    load_slot(2);
} else {
    // Handle error - maybe load default slot
    load_slot(1);
}

// Validate input ranges
function set_sensitivity(value) {
    if(value < 1) value = 1;
    if(value > 100) value = 100;
    return value;
}
```

### 5. Multi-Platform Support
```gpc
// Define platform-specific constants
#define FIRE_BTN_PS4    PS4_R1
#define FIRE_BTN_XB1    XB1_RT
#define FIRE_BTN_SWITCH SWITCH_ZR

int fire_button;

init {
    // Set appropriate button based on console
    if(get_console() == PIO_PS4) {
        fire_button = FIRE_BTN_PS4;
    } else if(get_console() == PIO_XB1) {
        fire_button = FIRE_BTN_XB1;
    } else if(get_console() == PIO_SWITCH) {
        fire_button = FIRE_BTN_SWITCH;
    }
}
```

### 6. Debugging Tips
```gpc
// Use TRACE outputs for debugging
main {
    set_val(TRACE_1, get_val(PS4_R1));  // Monitor R1 value
    set_val(TRACE_2, combo_running(RapidFire));  // Monitor combo state
    set_val(TRACE_3, get_battery());  // Monitor battery level
    
    // Use LEDs for status indication
    set_led(LED_1, rapid_fire_enabled);
    set_led(LED_2, anti_recoil_enabled);
}
```

---

## Conclusion

This comprehensive guide covers all aspects of Titan Two GPC programming based on official ConsoleTuner documentation. The GPC language provides powerful tools for creating sophisticated controller modifications while maintaining compatibility across multiple gaming platforms.

### Key Takeaways:
- **GPC is C-like** but specialized for gaming controller manipulation
- **Real-time processing** with 10ms iteration cycles
- **Extensive function library** for all gaming needs
- **Cross-platform compatibility** with automatic detection
- **Professional development tools** with Gtuner IV

### Next Steps:
1. Download and install Gtuner IV
2. Connect your Titan Two device
3. Start with simple scripts and gradually add complexity
4. Join the ConsoleTuner community for support and script sharing
5. Explore the online library for inspiration and examples

Remember to always respect game terms of service and use these tools responsibly for legitimate gaming enhancement purposes.

---

*This guide is based on official ConsoleTuner documentation and is intended for educational purposes. Always refer to the latest official documentation for the most up-to-date information.* 