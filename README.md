# Occasional Knowledge
This repository is just a dump of things I occasionally need in daily problem solving but I am too lazy to properly memorize or whatever. I mainly work with Processing (Java), openFrameworks (C++) and Arduino (C/C++) but I have written examples in a way that they are universally understandable for beginners.

### Cartesian Transformations
Sometimes you have to go back and forth between x/y coordinates and a linear assortment of data within an ArrayList

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

### Delta timing
There are many fun ways to avoid delay() functions in Processing and Arduino. Beginner's listen up!

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
// also useful when used with State-machines
	
```

---

### Finite-state machine
A finite-state machine, FSM or State machine can help you structure your code way much better.

It will help you entangle code and to abstract your ideas into a code blocks. With the help of a switch-case block we can then structure our program better.
If certain criteria match within the blocks, we can move forward to another block or remain within the block if needed.

```java
int state = INIT;

static final int INIT = 0;
static final int NEXT = 1;
static final int RANDOM = 2;

switch(state) {
	case INIT:
		// do something here
		if(certainCriteriaIsMe) {
			state = NEXT;
		}
	break;

	case NEXT:
		if(millis() - timestamp < interval) return;
		timestamp = millis();

		// code in the NEXT state is only executed for example every 1 second, as we have seen in Delta timing
		state = RANDOM;
	break;

	case RANDOM:
		// do some cool stuff.
		// let's return to our first state then
		state = INIT;
	break;
}
```

---

### Base64 encoding/decoding

Please check out my [Base64 encoding/decoding for Images in Processing repository](https://github.com/ndsh/base64_images_in_processing) on this matter. It's easy to use!

---

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

### speed up your modulos
( relevant for microcontrollers with embedded C language but no floating point alu )
https://embeddedgurus.com/stack-overflow/2011/02/efficient-c-tip-13-use-the-modulus-operator-with-caution/

### quickly resize an image without weird interpolation (smoothing)
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
```java
