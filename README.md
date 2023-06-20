# plc++
The idea of the project is, to create a language compilable in C++, generating C++ code that would restrict user from any action resulting in running program in any unpredictable way.

Example:

```cpp
#include <avr/io.h>

int main(void) {
  
  DDRD |=  ( 1 << 4 ); 
  DDRC &= ~( 1 << 5 ); 

  while (1) {
    
    bool isPressed = PINC & ( 1 << 5 );
    
    if( isPressed ) {   
      PORTD |= ( 1 << 4 ) ;
      for (int i = 0; i > -10; i++);
    }
    
    else  {
      PORTD &= ~( 1 << 4 ) ;
    }
  }
}
```
```
>>> 10: bool isPressed = PINC & ( 1 << 5 );
>>> 'isPressed' variable is input dependent.
>>>
>>> 12: if( isPressed ) ...
>>> Input dependent variable 'isPressed' used as an argument of a control function 'if'.
>>>
>>> Error: Input dependent variable cannot be an argument of a control function. Use conditional operator ( test ? ifTrue : ifFalse ) instead.
```
Let's say I compile this in C++, I run the program, and I forget to press the button during the tests, which would come out fake positive because of line `for (int i = 0; i > -10; i++);`. That's why it is not possible to give it to compilation in my language.
```cpp
#include <avr/io.h>

int main(void) {
  
  DDRD |=  ( 1 << 4 ); 
  DDRC &= ~( 1 << 5 ); 

  while (1) {
    
    for (int i = 0; i > -10; i++);
    PORTD   =   PINC & ( 1 << 5 )   ?   PORTD | ( 1 << 4 )   :   PORTD & ~( 1 << 4 );
  }
}
```
Now everything is correct and following will be generated and compiled by C++ compiler.
```cpp
#include <avr/io.h>

int main(void) {
  
  DDRD |=  ( 1 << 4 ); 
  DDRC &= ~( 1 << 5 ); 

  while (1) {
    
    for (int i = 0; i > -10; i++);
    
    uint8_t autoGenereted_filename_AV42L_K06RC_H2Z13_WD3JJ_1T = PORTD | ( 1 << 4 );
    uint8_t autoGenereted_filename_AV42L_K06RC_BDN0N_WK8WF_1F = PORTD & ~( 1 << 4 );
    
    PORTD   =   PINC & ( 1 << 5 )   ? autoGenereted_filename_AV42L_K06RC_H2Z13_WD3JJ_1T : autoGenereted_filename_AV42L_K06RC_BDN0N_WK8WF_1F;
    
  }
}
```
Wasteful? Yes. Limiting? Totally. But my device fails first test as soon as I turn it on.
