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