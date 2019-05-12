# Occasional Knowledge

## Cartesian
* x and y coordinates to uni-dimensional coordinate:
```java
	int uniDimensional = y*width+x;
```
* uni-dimensional coordinate to x and y coordinate:
```java
	int x = (int)i%width;
	int y = (int)i/width;
```

*width= eg. if you have a 4×3 grid with data/objects/images etc. then 4 is your width*
*i= your current index, for example within a for-loop*