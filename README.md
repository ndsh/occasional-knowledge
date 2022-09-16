[![](https://img.shields.io/badge/using-Processing-brightgreen.svg?style=flat-square&color=000000)](http://processing.org/)
[![](https://img.shields.io/badge/using-Arduino-brightgreen.svg?style=flat-square&color=000000)](http://arduino.cc/)

| Information | Donate |
|:------------|:-------|
| knowledge that doesn't stick well or takes some time to settle in my brain. i come here often to look at the stuff :] | [![BuyMeACoffee](https://www.buymeacoffee.com/assets/img/guidelines/logo-mark-1.svg)](https://www.buymeacoffee.com/ndsh) |

# Occasional Knowledge
This repository is just a dump of things I occasionally need in daily problem solving but I am too lazy to properly memorize or whatever. I mainly work with Processing (Java), openFrameworks (C++) and Arduino (C/C++) but I have written examples in a way that they are universally understandable for beginners.



## Overview
* [Transformations](#transformations)
* [Timing](#timing)
* [Clean](#clean)
* [Performance](#performance)
* [Random](#random)
* [Raspberry PI](#raspberry)

---

## Transformations

### Cartesian Transformations
Sometimes you have to go back and forth between x/y coordinates and a linear assortment of data, for example within an ArrayList or some similar list-like data structure.

**x and y coordinates to uni-dimensional coordinate:**
```java
int uniDimensional = y*width+x;
```

**uni-dimensional coordinate to x and y coordinate:**
```java
int x = (int)i%width;
int y = (int)i/width;
```

*width= eg. if you have a 4×3 grid with data/objects/images etc. then 4 is your width*

*i= your current index, for example within a for-loop*

**remap range (-1,1) to (0,1)**

example:
-1 + 1 = 0 * 0,5 -> 0
-0.5 + 1 = 0.5 * 0.5 -> 0.25
0 + 1 = 1 * 0.5 -> 0.5
0.5 + 1 = 1,5 * 0.5 -> 0.75
1 + 1 = 2 * 0,5 -> 1

```java
float remap(float val) {
    return (val + 1) * 0.5;
}
```

---

### Base64 encoding/decoding

Please check out my [Base64 encoding/decoding for Images in Processing repository](https://github.com/ndsh/sketches/tree/master/01%20Classes/04%20Base64) on this matter. It's easy to use!

---

## Timing

### Delta timing
There are many *fun* ways to avoid delay() functions in Processing and Arduino. Beginners listen up!

**positive delta timing:**
```java
long timestamp = 0;
long interval = 1000; // 1000 milliseconds = 1 second
if(millis() - timestamp > interval) {
	timestamp = millis();
	// run code within this block every 1 second!
}
```

**negating delta timing:**
```java
long timestamp = 0;
long interval = 1000; // 1000 milliseconds = 1 second
if(millis() - timestamp < interval) return;
timestamp = millis();
// run code within this block every 1 second!
// it's just a different way of writing code, maybe a bit faster?
// the delta timing cancels your loop, e.g. draw() in Processing or loop() within Arduino
// also useful when used with Finite State-machines	
```

### Timestamp

timestamp class
```java
import java.util.Date;

class Timestamp {
  public Timestamp() {
  }
  
  long getTimestamp() {
    Date d = new Date();
    long current = d.getTime()/1000; 
    return current;
  }
  
  String getTime() {
    return "["+ hour()+":"+minute()+":"+second()+"] ";
  }
  
  String getDatePrefix() {
    return (year() + "" + month() + "" + day() + "_");
  }
}
```

usage:

```java
Timestamp ts;

void setup() {
  ts = new Timestamp();
  println(ts.getTimestamp());
  
}

void draw() {
}
```

---

## Clean

### Finite-state machine
A finite-state machine, FSM or State machine can help you structure your code way much better.

It will help you entangle code and to abstract your ideas into a code blocks. With the help of a switch-case block we can then structure our program better.
If certain criteria match within the blocks, we can move forward to another block or remain within the block if needed.

Processing example of a Finite-state machine:
```java
int state = INIT;

static final int INIT = 0;
static final int NEXT = 1;
static final int RANDOM = 2;

void setState(int _state) {
  state = _state;
}

void stateMachine(int state) {
	switch(state) {
		case INIT:
			// do something here
			if(certainCriteriaIsMe) {
				setState(NEXT);
			}
		break;

		case NEXT:
			if(millis() - timestamp < interval) return;
			timestamp = millis();

			// code in the NEXT state is only executed for example every 1 second, as we have seen in Delta timing
			setState(RANDOM);
		break;

		case RANDOM:
			// do some cool stuff.
			// let's return to our first state then
			setState(INIT);
		break;
	}
}
```

Arduino example:
```c++
#define INTRO 0
#define RUN 1

int state = 0;


void setup() {
  // put your setup code here, to run once:
  Serial.begin(9600);
  Serial.println("setup");
}

void loop() {
  stateMachine();
}

void stateMachine() {
  switch(state) {
    case INTRO:
      Serial.println("intro");
      state = RUN;
    break;

    case RUN:
      Serial.println("run");
    break;
  }
}
```

---

## Performance

### Speed up your modulos
( relevant for microcontrollers with embedded C language but no floating point alu )
https://embeddedgurus.com/stack-overflow/2011/02/efficient-c-tip-13-use-the-modulus-operator-with-caution/

tl;dr

```c++
C = A % B is equivalent to C = A - B * (A / B)
```

---


### Map a float value with Arduino
In Arduino you cannot natively map a float value. Period.
Here is a function that fixes this issue.
Credit: jonasotto.de / julian-h.de

// original comment on this issue
// for arduino :(
// arduino is stupid.
// so jonasotto.de and me wrote this.
// end of problems.



```c++
float mapfloat(float x, float in_min, float in_max, float out_min, float out_max) {
  return (x - in_min) * (out_max - out_min) / (in_max - in_min) + out_min;
}
```

### set() is faster than image() in Processing
set() is faster than image() when drawing untransformed images.

---

## Random

### Breathing LED (Apple Style)
Original source: https://sean.voisen.org/blog/2011/10/breathing-led-with-arduino/
```c++
int ledPin = 9;
float speed = PI/2; // anything from PI to PI/2 goes here

void setup() {
}

void loop() {
    float val = (exp(sin(millis()/2000.0*speed)) - 0.36787944)*108.0;
    analogWrite(ledPin, val);
}
```

### Adjust aspect ratio
with a Processing example:

```java
float[] calculateAspectRatioFit(float srcWidth, float srcHeight, float maxWidth, float maxHeight) {
  //float[] result;
  float ratio = Math.min(maxWidth / srcWidth, maxHeight / srcHeight);
  float result[] = {srcWidth*ratio, srcHeight*ratio};
  //return { width: srcWidth*ratio, height: srcHeight*ratio };
  return result;
}
```

usage:
```java
 PImage p = loadImage("yourImage.jpg");
 float[] aspect = calculateAspectRatioFit(setWidth, setHeight, 1280, 720);
 surface.setSize((int)aspect[0], (int)aspect[1]);
```

### Integer-ratio scaling aka "Quickly resize an image without that weird smudgy interpolation" (aka "sans smoothing")
```java
PImage img;
PImage canvas;
PGraphics dd;
 
void setup() 
{
  size(200,200);
  img = loadImage("base.png");
  image(img,0,0);
  dd = createGraphics(2*img.width,2*img.height);  
  dd.noSmooth();  
  dd.beginDraw();
  dd.clear();
  dd.endDraw();
  noLoop();
}
 
void draw() 
{
  canvas = img.get(0,0,width,height);
  canvas.loadPixels();
  dd.beginDraw();
  dd.loadPixels();
  for (int y = 0; y < height; y++)
  {
    for (int x = 0; x < width; x++)
    {
      dd.set(x*2, y*2, canvas.get(x, y));
      dd.set(x*2+1, y*2+1, canvas.get(x, y));
      dd.set(x*2+1, y*2, canvas.get(x, y));
      dd.set(x*2, y*2+1, canvas.get(x, y));
    }
  }  
  dd.updatePixels();
  dd.endDraw();
  dd.save("zoom.png");
  println("..finished.");
}
```

### Switch Processing Sketch between "fullScreen" and "normal" Screen
```java
boolean big = true;
void setup(){
  fullScreen();
  surface.setResizable(true);
  surface.setSize(600, 400);
}
 
void draw(){
  rect(200, 200, 20, 20);
}
 
void mousePressed(){
  if(big) {
    surface.setSize(600,400);
    big = false;
  } else {
    surface.setSize(displayWidth,displayHeight);
    big = true;
  }
}
```

### Average Color of a certain rectangular area
```java
color averageColor(PImage source, float x, float y, float w, float h) {
  PImage temp = source.get((int)x, (int)y, (int)w, (int)h);
  temp.loadPixels();
  int r = 0;
  int g = 0;
  int b = 0;
  
  for (int i=0; i<temp.pixels.length; i++) {
    color c = temp.pixels[i];
    r += c>>16&0xFF;
    g += c>>8&0xFF;
    b += c&0xFF;
  }

  // return the mean of the R, G and B conponents
  r /= temp.pixels.length;
  g /= temp.pixels.length;
  b /= temp.pixels.length;
 
  return color(r, g, b);
}
```

### Average Color of a certain circular area
```java
color averageColorCircular(PImage img, int x, int y, int radius) {
  float r = 0;
  float g = 0;
  float b = 0;
  int num = 0;

  /* Iterate through a bounding box in which the circle lies */
  for (int i = x - radius; i < x + radius; i++) {
    for (int j = y - radius; j < y + radius; j++) {

      // if pixel is outside of canvas: skip
      if (i < 0 || i >= width || j < 0 || j >= height)
        continue;

      // if pixel is outside of circle: skip
      if (dist(x, y, i, j) > r)
        continue;

      color c = img.get(i, j);
      r += c>>16&0xFF;
      g += c>>8&0xFF;
      b += c&0xFF;
      num++;
    }
  }

  // return the mean of the R, G, and B components
  return color(r/num, g/num, b/num);
}
```

### Averaging RGB colors the right way
Video explainer by minutephysics: [Computer Color is Broken](https://www.youtube.com/watch?v=LKnqECcg6Gw&ab_channel=minutephysics)
instead of using for example:
```java
  r += c>>16&0xFF;
  r /= temp.pixels.length;
```

sum the squares of components and return th sqrt of the squared R, G and B sums:
```java
  r += (c>>16&0xFF) * (c>>16&0xFF);
  r /= sqrt(r/temp.pixels.length);
```

### map() with different easings
from [https://github.com/sighack/easing-functions/tree/master/code/easing](easing-functions)
```java
/* The map2() function supports the following easing types */
final int LINEAR = 0;
final int QUADRATIC = 1;
final int CUBIC = 2;
final int QUARTIC = 3;
final int QUINTIC = 4;
final int SINUSOIDAL = 5;
final int EXPONENTIAL = 6;
final int CIRCULAR = 7;
final int SQRT = 8;

/* When the easing is applied (in, out, or both) */
final int EASE_IN = 0;
final int EASE_OUT = 1;
final int EASE_IN_OUT = 2;

/*
 * A map() replacement that allows for specifying easing curves
 * with arbitrary exponents.
 *
 * value :   The value to map
 * start1:   The lower limit of the input range
 * stop1 :   The upper limit of the input range
 * start2:   The lower limit of the output range
 * stop2 :   The upper limit of the output range
 * type  :   The type of easing (see above)
 * when  :   One of EASE_IN, EASE_OUT, or EASE_IN_OUT
 */
float map2(float value, float start1, float stop1, float start2, float stop2, int type, int when) {
  float b = start2;
  float c = stop2 - start2;
  float t = value - start1;
  float d = stop1 - start1;
  float p = 0.5;
  switch (type) {
  case LINEAR:
    return c*t/d + b;
  case SQRT:
    if (when == EASE_IN) {
      t /= d;
      return c*pow(t, p) + b;
    } else if (when == EASE_OUT) {
      t /= d;
      return c * (1 - pow(1 - t, p)) + b;
    } else if (when == EASE_IN_OUT) {
      t /= d/2;
      if (t < 1) return c/2*pow(t, p) + b;
      return c/2 * (2 - pow(2 - t, p)) + b;
    }
    break;
  case QUADRATIC:
    if (when == EASE_IN) {
      t /= d;
      return c*t*t + b;
    } else if (when == EASE_OUT) {
      t /= d;
      return -c * t*(t-2) + b;
    } else if (when == EASE_IN_OUT) {
      t /= d/2;
      if (t < 1) return c/2*t*t + b;
      t--;
      return -c/2 * (t*(t-2) - 1) + b;
    }
    break;
  case CUBIC:
    if (when == EASE_IN) {
      t /= d;
      return c*t*t*t + b;
    } else if (when == EASE_OUT) {
      t /= d;
      t--;
      return c*(t*t*t + 1) + b;
    } else if (when == EASE_IN_OUT) {
      t /= d/2;
      if (t < 1) return c/2*t*t*t + b;
      t -= 2;
      return c/2*(t*t*t + 2) + b;
    }
    break;
  case QUARTIC:
    if (when == EASE_IN) {
      t /= d;
      return c*t*t*t*t + b;
    } else if (when == EASE_OUT) {
      t /= d;
      t--;
      return -c * (t*t*t*t - 1) + b;
    } else if (when == EASE_IN_OUT) {
      t /= d/2;
      if (t < 1) return c/2*t*t*t*t + b;
      t -= 2;
      return -c/2 * (t*t*t*t - 2) + b;
    }
    break;
  case QUINTIC:
    if (when == EASE_IN) {
      t /= d;
      return c*t*t*t*t*t + b;
    } else if (when == EASE_OUT) {
      t /= d;
      t--;
      return c*(t*t*t*t*t + 1) + b;
    } else if (when == EASE_IN_OUT) {
      t /= d/2;
      if (t < 1) return c/2*t*t*t*t*t + b;
      t -= 2;
      return c/2*(t*t*t*t*t + 2) + b;
    }
    break;
  case SINUSOIDAL:
    if (when == EASE_IN) {
      return -c * cos(t/d * (PI/2)) + c + b;
    } else if (when == EASE_OUT) {
      return c * sin(t/d * (PI/2)) + b;
    } else if (when == EASE_IN_OUT) {
      return -c/2 * (cos(PI*t/d) - 1) + b;
    }
    break;
  case EXPONENTIAL:
    if (when == EASE_IN) {
      return c * pow( 2, 10 * (t/d - 1) ) + b;
    } else if (when == EASE_OUT) {
      return c * ( -pow( 2, -10 * t/d ) + 1 ) + b;
    } else if (when == EASE_IN_OUT) {
      t /= d/2;
      if (t < 1) return c/2 * pow( 2, 10 * (t - 1) ) + b;
      t--;
      return c/2 * ( -pow( 2, -10 * t) + 2 ) + b;
    }
    break;
  case CIRCULAR:
    if (when == EASE_IN) {
      t /= d;
      return -c * (sqrt(1 - t*t) - 1) + b;
    } else if (when == EASE_OUT) {
      t /= d;
      t--;
      return c * sqrt(1 - t*t) + b;
    } else if (when == EASE_IN_OUT) {
      t /= d/2;
      if (t < 1) return -c/2 * (sqrt(1 - t*t) - 1) + b;
      t -= 2;
      return c/2 * (sqrt(1 - t*t) + 1) + b;
    }
    break;
  };
  return 0;
}

/*
 * A map() replacement that allows for specifying easing curves
 * with arbitrary exponents.
 *
 * value :   The value to map
 * start1:   The lower limit of the input range
 * stop1 :   The upper limit of the input range
 * start2:   The lower limit of the output range
 * stop2 :   The upper limit of the output range
 * v     :   The exponent value (e.g., 0.5, 0.1, 0.3)
 * when  :   One of EASE_IN, EASE_OUT, or EASE_IN_OUT
 */
float map3(float value, float start1, float stop1, float start2, float stop2, float v, int when) {
  float b = start2;
  float c = stop2 - start2;
  float t = value - start1;
  float d = stop1 - start1;
  float p = v;
  float out = 0;
  if (when == EASE_IN) {
    t /= d;
    out = c*pow(t, p) + b;
  } else if (when == EASE_OUT) {
    t /= d;
    out = c * (1 - pow(1 - t, p)) + b;
  } else if (when == EASE_IN_OUT) {
    t /= d/2;
    if (t < 1) return c/2*pow(t, p) + b;
    out = c/2 * (2 - pow(2 - t, p)) + b;
  }
  return out;
}
```

### Serial communication with Arduino (or in this case Teensy)
From PaulStoffregen: [UART + I2C display --> not working over 1200 baud rate for UART](https://forum.pjrc.com/threads/67454-UART-I2C-display-gt-not-working-over-1200-baud-rate-for-UART?p=281054#post281054)

*Sending Microcontroller*
```c++
int pin0 = A0;
int pin1 = A1;
unsigned long my_time;
int reset_switch = 16;
int reset_pin = 0;

void setup() {
  // put your setup code here, to run once:
  pinMode(reset_switch, INPUT_PULLUP);
  Serial.begin(9600);
  Serial1.begin(1000000);
  Serial.println("Start Send");
  if (Serial.available() > 0) {
    Serial.clear();
    delay(50);
  }
  if (Serial1.available() > 0) {
    Serial1.clear();
    delay(50);
  }
}

void loop() {
  static int messageCount=0;
  
  reset_pin = digitalRead(reset_switch);
  if (reset_pin == LOW) {
    do_reset();
  }
  int val0 = map(analogRead(pin0), 0, 1023, 0, 1100);
  int val1 = map(analogRead(pin1), 0, 1023, 0, 200);

  my_time = millis();
  Serial1.print("<");
  Serial1.print(val0);
  Serial1.print(",");
  Serial1.print(val1);
  Serial1.print(">");

  Serial.print(reset_pin);
  Serial.print("\t");
  Serial.print(my_time);
  Serial.print("\t");
  Serial.print(val0);
  Serial.print("\t");
  Serial.println(val1);

  // once every 5000 messages, allow a brief silent time
  // for the receiver to detect line idle and then start bit
  if (++messageCount >= 5000) {  
    Serial1.flush(); // wait for buffered data to transmit
    delayMicroseconds(10); // then wait approx 10 bit times
    messageCount = 0;
  }

}

void do_reset() {
  // send reboot command -----
  SCB_AIRCR = 0x05FA0004;
}
```

*Receiving Microcontroller*
```c++
//Receiver Code

const byte numChars = 32;
char receivedChars[numChars];
char tempChars[numChars];        // temporary array for use when parsing

int integerFromPC = 0;
float floatFromPC = 0.0;

boolean newData = false;

int reset_switch = 16;
int reset_pin = 0;

void setup() {
  // put your setup code here, to run once:
  pinMode(reset_switch, INPUT_PULLUP);
  Serial.begin(9600);
  Serial1.begin(1000000);

  Serial.println("Start Receive");

  if (Serial.available() > 0) {
    Serial.clear();
    delay(50);
  }
  if (Serial1.available() > 0) {
    Serial1.clear();
    delay(50);
  }

  Serial.println("start");

}

void loop() {

  reset_pin = digitalRead(reset_switch);
  if (reset_pin == LOW) {
    do_reset();
  }
  
  recvWithStartEndMarkers();
  if (newData == true) {
    strcpy(tempChars, receivedChars);
    // this temporary copy is necessary to protect the original data
    //   because strtok() used in parseData() replaces the commas with \0
    parseData();
    showParsedData();
    newData = false;
  }
}


void recvWithStartEndMarkers() {
  static boolean recvInProgress = false;
  static byte ndx = 0;
  char startMarker = '<';
  char endMarker = '>';
  char rc;

  while (Serial1.available() > 0 && newData == false) {
    rc = Serial1.read();
    //Serial.println((char)rc);

    if (recvInProgress == true) {
      if (rc != endMarker) {
        receivedChars[ndx] = rc;
        ndx++;
        if (ndx >= numChars) {
          ndx = numChars - 1;
        }
      }
      else {
        receivedChars[ndx] = '\0'; // terminate the string
        recvInProgress = false;
        ndx = 0;
        newData = true;
      }
    }

    else if (rc == startMarker) {
      recvInProgress = true;
    }
    else {
      Serial.print("Unexpected character: ");
      Serial.println(rc);
    }
  }
}

//============

void parseData() {      // split the data into its parts

  char * strtokIndx; // this is used by strtok() as an index

  strtokIndx = strtok(tempChars, ",");     // get the first part - the string
  integerFromPC = atoi(strtokIndx);     // convert this part to an integer

  strtokIndx = strtok(NULL, ",");
  floatFromPC = atof(strtokIndx);     // convert this part to a float

}

//============

void showParsedData() {
  Serial.print("Integer ");
  Serial.println(integerFromPC);
  Serial.print("Float ");
  Serial.println(floatFromPC);
}

void do_reset() {
  // send reboot command -----
  SCB_AIRCR = 0x05FA0004;
}
```

### get the aspect ratio of an image
this is an easy one but i didn't think about it yesterday: what if you want to get the aspect ratio of an image? you divide the width by the height, right?
width=200, height=100 = width/height = 200/100 = 2 = 2:1 aspect ratio.
what if you have something more exotic or even something much more familiar?
width=1920, height=1080 = 1920/1080 = 1.777777778 = 1.7778:1 ? it's technically correct, but we would rather see the "16:9" here.
what we are missing is the GCD (greatest common divisor) or GGT (größter gemeinsamer teiler) to normalize the values.

we can use either of these two implementations:

```java
// greatest common divisor. standard implementation.
int gcd(int a, int b) {
  if (a == 0) return b;
  while (b != 0) {
    if (a > b) a = a - b;
    else b = b - a;
  }
  return a;
}
```

```java
// greatest common divisor. modified version
int gcd2(int a, int b) {
  int h;
  while (b != 0) {
    h = a % b;
    a = b;
    b = h;
  }
  return a;
}
```

then use the following function (not fully implemented tho)
```java
void getAspectRatio(int _w, int _h) {
  int divisor = gcd(_w, _h);
  println((_w/divisor) + ":" + (_h/divisor)); 
}

getAspectRatio(1920, 1080);
getAspectRatio(200, 100);
getAspectRatio(1690, 2000);
```

### how to write a processing library – get started
quick start via shiffman:
- [https://github.com/sighack/easing-functions/tree/master/code/easing](easing-functions)
- [https://www.youtube.com/watch?v=pI2gvl9sdtE](How to Make a Processing (Java) Library Part 1)
- [https://www.youtube.com/watch?v=U0TGZCEWn8g](How to Make a Processing (Java) Library Part 2)

a catalog of core.jar functions:
https://processing.github.io/processing-javadocs/core/index-all.html

follow everything in the tutorial, but if you want to compile and have it run in Processing4+ please change the following in resources/build.xml

```xml
<target name="copyToSketchbook">
	…
	<fileset dir="${project.tmp}/${project.name}"/>
</target>
```

and modify it to:

```xml
<target name="copyToSketchbook">
	…
<fileset dir="${project.tmp}/${project.name}">
		<!--
			https://github.com/processing/processing-library-template/pull/31
			To avoid the following error in Processing 4.0 and newer:
			The library "${project.name}" cannot be used
			because it contains the processing.core libraries.
			Please contact the library author for an update.
		 -->
		<exclude name="**/core.jar"/>
	</fileset>
</target>
```

this will exclude the core.jar from compiling with the library and ensure that Processing4 can load and run your library.

### how to include another processing library into your own (for example: oscP5 inside your lib)
1. add the .jar-file that you need to your eclipse project: drag & drop file to /lib
2. in /resources/build.properties add the jar to the local classpath:
```xml
	classpath.local.include=core.jar,oscp5.jar
```
3. in build.xml add the jar as an exclude to the **<copyToSketchBook>** block:
```xml
<target name="copyToSketchbook">
	…
<fileset dir="${project.tmp}/${project.name}">
		…
		// possible other excludes
		<exclude name="**/oscP5.jar"/> // or yourFileName.jar
	</fileset>
</target>
```
4. in your java-class add the imports to the header:

```java
// the standard stuff
package display.library;
import processing.core.*;

// the lib import
import oscP5.*;
import netP5.*;
```

5. in your processing sketches import your library but also the imports that you did in step 4. this should do the trick for now.

### modulo wrappers

```java
int modWrap(int i, int max) {
  return ((i % max) + max) % max;
}

int modWrap(int a, int b){
    return (a+(ceil(abs(a)/b))*b) % b;
}
```

## Raspberry

### Raspberry Pi autostart Processing sketches
// have to update later with the proper example and a also watchdog example
// these are just "notes" for now
- https://discourse.processing.org/t/autostart-a-program/24368/11
- https://forums.raspberrypi.com/viewtopic.php?f=91&t=287334&p=1738121&hilit=humidity#p1738121
- https://forums.raspberrypi.com/viewtopic.php?t=294014 (this is the most important for now)
- https://forums.raspberrypi.com/viewtopic.php?t=230911

i believe this goes into the lxde autostart file (from https://forum.processing.org/two/discussion/22968/#Comment_100027)
```
/usr/local/bin/processing-java --sketch=/home/pi/sketchbook/sketchname --run

```

example
```
/home/curtain/Downloads/processing-4.0b8/processing-java --sketch=/home/curtain/Desktop/sketchname --run

```

### How to kill a java sketch via ssh
$pidof java
// this will return the <PID>
$kill SIGKILL <PID>


# javascript
create a two-dimensional empty array quickly:
```javascript
var arr = Array.from(Array(2), () => new Array(4));
```
