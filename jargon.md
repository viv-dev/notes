# jargon

**string literal**
A string literal is the 'raw' written version of a string - i.e. "Hello, world!". It is useful to be able to refer to the string literal in order to define behaviour that happens around it in certain programming languages. For example, in C/C++ when a string literal is assigned to a `char *`, there will be memory allocated to hold the corresponding number of bytes used in the string, plus an additional null terminator at the end. 

```c++
// char* holds a pointer to the location of the first element of the string stored in memory.
// A null terminator is also added to the end of the memory block, making the length of the string larger by one
// Only the pointer is constant, not the string itself
const char * a_string = "This is a string."
```

