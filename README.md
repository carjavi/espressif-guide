<br>

<p align="center"><img src="https://raw.githubusercontent.com/carjavi/espressif-guide/master/img/espressif.png" height="100" alt=" " /></p>

<h1 align="center">Espressif Guide</h1> 
<h4 align="right">Dic 22</h4>

<img src="https://img.shields.io/badge/Hardware-ESP32-red">
<img src="https://img.shields.io/badge/Hardware-Arduino__nano-red">

ESP8266 / ESP32 guide

<br>

## ESP32 Arduino core Setting
go to your Arduino sketchbook directory. It’s by default Arduino directory in My Documents unless otherwise changed. You can verify it by opening ```Arduino IDE > File > Preferences > Sketchbook Location```.

Considering your sketchbook directory is located in My Documents > Arduino, open the directory. You should see libraries directory inside it.

Now create a new directory called hardware. Inside it create another directory called espressif. Inside it create another directory called esp32.

The directory structure should look like ```My Documents > Arduino > hardware > espressif > esp32```

Now extract the previously downloaded ESP32 core in esp32 directory.

## Interrupts In ESP32
ESP32 provides up to 32 interrupt slots for each core. Each interrupt has a certain priority level and can be classified into two types.

```Hardware interrupts``` – These occur in response to an external event. For example, a GPIO interrupt (when a key is pressed) or a touch interrupt (when touch is detected).

```Software Interrupt``` – These occur in response to a software instruction. For example, a simple timer interrupt or a watchdog timer interrupt (when the timer times out).

Once you are done, verify that “boards.txt”, “platform.txt”, and the cores, doc, tools, etc. folders are there inside esp32 directory.

In order to compile code for the ESP32, you need the Xtensa GNU compiler collection (GCC) installed on your machine. Go to ```esp32 > tools``` folder and execute ```get.exe```

This executable will download the Xtensa GNU tools and the ESP32 software development kit (SDK), and unzip them to the proper location.

You should see a few new folders in the “tools” directory, including “sdk” and “xtensa-esp32-elf” once it’s done.

## ESP32 GPIO Interrupt
In ESP32 we can define an interrupt service routine function that will be called when the GPIO pin changes its logic level.

All GPIO pins in an ESP32 board can be configured to act as interrupt request inputs.

## Attaching an Interrupt to a GPIO Pin
In the Arduino IDE, we use a function called ```attachInterrupt()``` to set an interrupt on a pin by pin basis. The syntax looks like below.

```
attachInterrupt(GPIOPin, ISR, Mode);
```

This function accepts three arguments:

```GPIOPin``` – sets the GPIO pin as the interrupt pin, which tells ESP32 which pin to monitor.

```ISR``` – is the name of the function that will be called each time the interrupt occurs.

```Mode``` – defines when the interrupt should be triggered. Five constants are predefined as valid values:

| Name      | Purpose                                         |
|-----------|-------------------------------------------------|
| **LOW**   | Triggers the interrupt whenever the pin is LOW |
| **HIGH**  | Triggers the interrupt whenever the pin is HIGH  |
| **CHANGE**  | Triggers the interrupt whenever the pin changes value, from HIGH to LOW or LOW to HIGH |
| **FALLING**  | Triggers the interrupt when the pin goes from HIGH to LOW  |
| **RISING**  | Triggers the interrupt when the pin goes from LOW to HIGH |


## Detaching an Interrupt from a GPIO Pin
When you want ESP32 to no longer monitor the pin, you can call the detachInterrupt() function. The syntax looks like below.
```
detachInterrupt(GPIOPin);
```

## Interrupt Service Routine
The Interrupt Service Routine (ISR) is a function that is invoked every time an interrupt occurs on the GPIO pin.

Its syntax looks like below.
```
void IRAM_ATTR ISR() {
    Statements;
}
```
ISRs in ESP32 are special kinds of functions that have some unique rules that most other functions do not have.

An ISR cannot have any parameters, and they should not return anything.
ISRs should be as short and fast as possible as they block normal program execution.
They should have the IRAM_ATTR attribute, according to the ESP32 documentation.

## Example Code: Simple Interrupt
Here the above sketch is rewritten to demonstrate how to debounce an interrupt programmatically. In this sketch we allow the ISR to be executed only once on each button press, instead of executing it multiple times.
```
struct Button {
    const uint8_t PIN;
    uint32_t numberKeyPresses;
    bool pressed;
};

Button button1 = {18, 0, false};

//variables to keep track of the timing of recent interrupts
unsigned long button_time = 0;  
unsigned long last_button_time = 0; 

void IRAM_ATTR isr() {
    button_time = millis();
if (button_time - last_button_time > 250)
{
        button1.numberKeyPresses++;
        button1.pressed = true;
       last_button_time = button_time;
}
}

void setup() {
    Serial.begin(115200);
    pinMode(button1.PIN, INPUT_PULLUP);
    attachInterrupt(button1.PIN, isr, FALLING);
}

void loop() {
    if (button1.pressed) {
        Serial.printf("Button has been pressed %u times\n", button1.numberKeyPresses);
        button1.pressed = false;
    }
}
```



<br>

more inf: https://lastminuteengineers.com/handling-esp32-gpio-interrupts-tutorial/<br>
https://lastminuteengineers.com/esp32-arduino-ide-tutorial/

<br>

---
Copyright &copy; 2022 [carjavi](https://github.com/carjavi). <br>
```www.instintodigital.net``` <br>
carjavi@hotmail.com <br>
<p align="center">
    <a href="https://instintodigital.net/" target="_blank"><img src="https://raw.githubusercontent.com/carjavi/espressif-guide/master/img/developer.png" height="100" alt="www.instintodigital.net"></a>
</p>
