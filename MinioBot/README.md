<h1> MinioBot </h1>
<h2> How to run it </h2>
To make this code work, you have to install the <a href="https://github.com/OttoDIY/OttoDIYLib"><span style="color:blue"><Otto.h></span></a> library.
After installing the zip file, implement it in Arduino IDE (Sketches --> Include Library --> Include Library from .zip file).

In the end, open the .ino file: when you'll open it, arduino will ask you to create a dedicated folder, obviously allow it.

<strong> PS: </strong> to make MinioBot do what he was programmed to do using Bluetooth commands you can use a Serial Bluetooth Terminal (the app that has been used by us is <a href="https://www.microsoft.com/store/productId/9WZDNCRDFST8?ocid=pdpshare"> this</a>; we've developed a more user-friendly app, but the bluetooth libreries aren't compatible with the HC06 module because it isn't a BLE device. 
<h1> Libraries, Variables, Macros and Servos </h1>
There've been included 3 libreries:

- <code href="https://github.com/arduino-libraries/Servo">Servo.h</code>: gives the opportunity to change servos' angle with high precision.

- <code href="https://docs.arduino.cc/learn/built-in-libraries/software-serial"><SoftwareSerial.h></code>: allows serial communication between digital pins.

- <code href="https://github.com/OttoDIY/OttoDIYLib"><Otto.h></code>: otto's library, it allows to implement useful functions, like <code>Otto.walk()</code>.

There are two main <strong> #define </strong> sections:

- <em> Defining notes </em> : in this section it has been assigned to each note a frequency, to make easier and more understandable the melodies' arrays declaration.
```C++
///////////////////////////////////////////////////////////////////
//-- Defining Notes ---------------------------------------------//
///////////////////////////////////////////////////////////////////

#define NOTE_B0  31
#define NOTE_C1  33
#define NOTE_CS1 35
#define NOTE_D1  37
#define NOTE_DS1 39
#define NOTE_E1  41
#define NOTE_F1  44
#define NOTE_FS1 46
#define NOTE_G1  49
#define NOTE_GS1 52
#define NOTE_A1  55
#define NOTE_AS1 58
#define NOTE_B1  62
#define NOTE_C2  65
#define NOTE_CS2 69
#define NOTE_D2  73
#define NOTE_DS2 78
#define NOTE_E2  82
#define NOTE_F2  87
#define NOTE_FS2 93
#define NOTE_G2  98
#define NOTE_GS2 104
#define NOTE_A2  110
#define NOTE_AS2 117
#define NOTE_B2  123
#define NOTE_C3  131
#define NOTE_CS3 139
#define NOTE_D3  147
#define NOTE_DS3 156
#define NOTE_E3  165
#define NOTE_F3  175
#define NOTE_FS3 185
#define NOTE_G3  196
#define NOTE_GS3 208
#define NOTE_A3  220
#define NOTE_AS3 233
#define NOTE_B3  247
#define NOTE_C4  262
#define NOTE_CS4 277
#define NOTE_D4  294
#define NOTE_DS4 311
#define NOTE_E4  330
#define NOTE_F4  349
#define NOTE_FS4 370
#define NOTE_G4  392
#define NOTE_GS4 415
#define NOTE_A4  440
#define NOTE_AS4 466
#define NOTE_B4  494
#define NOTE_C5  523
#define NOTE_CS5 554
#define NOTE_D5  587
#define NOTE_DS5 622
#define NOTE_E5  659
#define NOTE_F5  698
#define NOTE_FS5 740
#define NOTE_G5  784
#define NOTE_GS5 831
#define NOTE_A5  880
#define NOTE_AS5 932
#define NOTE_B5  988
#define NOTE_C6  1047
#define NOTE_CS6 1109
#define NOTE_D6  1175
#define NOTE_DS6 1245
#define NOTE_E6  1319
#define NOTE_F6  1397
#define NOTE_FS6 1480
#define NOTE_G6  1568
#define NOTE_GS6 1661
#define NOTE_A6  1760
#define NOTE_AS6 1865
#define NOTE_B6  1976
#define NOTE_C7  2093
#define NOTE_CS7 2217
#define NOTE_D7  2349
#define NOTE_DS7 2489
#define NOTE_E7  2637
#define NOTE_F7  2794
#define NOTE_FS7 2960
#define NOTE_G7  3136
#define NOTE_GS7 3322
#define NOTE_A7  3520
#define NOTE_AS7 3729
#define NOTE_B7  3951
#define NOTE_C8  4186
#define NOTE_CS8 4435
#define NOTE_D8  4699
#define NOTE_DS8 4978
#define REST      0
```
- <em> Defining Servos and others </em> : in this section every servo and sensor has been defined.  
```C++
///////////////////////////////////////////////////////////////////
//-- Defining servos and others ---------------------------------//
///////////////////////////////////////////////////////////////////
#define LeftLeg 2 // Looking MinioBot from the front
#define RightLeg 3 // Looking MinioBot from the front
#define LeftFoot 4 // Looking MinioBot from the front
#define RightFoot 5 // Looking MinioBot from the front
#define Buzzer 13
#define RightArm 6 // Looking MinioBot from the front
#define LeftArm 7 // Looking MinioBot from the front
Servo Servo_6; /* RightArm */
Servo Servo_7; /* LeftArm */
Servo Servo_5; /* RightFoot */
Servo Servo_4; /* LeftFoot */
```
Also there are many variables tied to many important functions. They are:
```C++
bool walkForward = false;
bool walkBackwards = false; // they're tied to the functions walkForward_si and walkBackwards_si; they're used to stop the walk functions while they're false.
int Piezo = 13; // Buzzer, used to play the melodies' notes
int rxPin = 11; 
int txPin = 12; // they're used to initialize rx and tx pin used by the bluetooth module HC-06.
int i = 0; // used to stop the fly function while its value is equal to 0.
char message; // used to memorize the message sent by the user, wich'll trigger one of the switch cases situated in the loop function.
```
<h1> Functions </h1>
In this code, there are two types of functions:

- <em> MinioBot All Moves </em>: functions that move MinioBot's servos.

- <em> Melodies </em>: functions that allow the Buzzer to play melodies.
<h2> MinioBot All Moves </h2>

<code>startArms()</code>: sets both arms in a neutral position.
```C++
///////////////////////////////////////////////////////////////////
//-- MinioBot All Moves -----------------------------------------//
///////////////////////////////////////////////////////////////////
void startArms(){
  Servo_6.write(0); delay(100); 
  Servo_7.write(180); delay(100); 
}
```

<code>move_RightArm()</code>: allows to move the right arm's servo based on its position (when its down it goes up, when it's up it goes down).
```C++
void move_RigthArm() {
  if(Servo_6.read() == 0){
    Servo_6.write(90);
    Servo_6.write(1); 
    Servo_6.write(180);
  } else{
    Servo_6.write(0);
  }
}
```
<code>move_LeftArm()</code>: allows to move the left arm's servo based on its position (when its down it goes up, when it's up it goes down).
```C++
void move_LeftArm() {
  if(Servo_7.read() == 180){
    Servo_7.write(90); 
    Servo_7.write(179); 
    Servo_7.write(5);
  } else{
    Servo_7.write(180);
  }
}
```
<code>bothArms()</code>: allows to move both arms' servos based on its position (when the're down they go up, when they're up they go down).
```C++
void bothArms() {
  if(Servo_6.read() == 0 && Servo_7.read() == 180){
    Servo_6.write(180);
    Servo_7.write(0);
  } else{
    Servo_6.write(0);
    Servo_7.write(180);
  }
}
```
<code>walkForward_si()</code>: allows MinioBot to go forward while walkForward is true.
```C++
void walkForward_si(){
  while(walkForward = true){
    Otto.walk(1, 750, 1);
    message = char(bluetooth.read());
       if (message=='h'){
       walkForward = false;
    }
  }
}
```
<code>walkBackwards_si()</code>: allows MinioBot to go backwards while walkBackwards is true.
```C++
void walkBackwards_si(){
  while(walkBackwards = true){
    Otto.walk(1, 750, -1);
    message = char(bluetooth.read());
       if (message=='h'){
       walkBackwards = false;
    }
  }
}
```
<code>TurnLeft()</code>: allows MinioBot to turn left making 5 steps.
```C++
void TurnLeft(){
  Otto.turn(5, 750, 1);
}
```
<code>TurnRight()</code>: allows MinioBot to turn left making 5 steps.
```C++
void TurnRight(){
  Otto.turn(5, 750, -1);
}
```
<code>fly()</code>: allows Miniobot to "fly" (he doesn't fly, he just moves his arms up and down simultaneously :p).
```C++
void fly(){
  for (i=0; i<10;i++){
    Servo_6.write(90) ; delay(100);
    Servo_7.write(90); delay(100);
    Servo_6.write(0) ; delay(100);
    Servo_7.write(180); delay(45);
    message = char(bluetooth.read());
    if (message=='0'){
      break;
    }
  }
}
```
<h2> Melodies </h2>

The code written to make the Buzzer play these melodies is from <a href="https://github.com/robsoncouto/arduino-songs"> this </a> repository, but it has been readapted to work with Miniobot. Here's the code:
```C++
///////////////////////////////////////////////////////////////////
//-- Melodies ---------------------------------------------------//
///////////////////////////////////////////////////////////////////

void HappyBirthday(){
int tempo = 140;

int melody[] = {

  NOTE_C4,4, NOTE_C4,8, 
  NOTE_D4,-4, NOTE_C4,-4, NOTE_F4,-4,
  NOTE_E4,-2, NOTE_C4,4, NOTE_C4,8, 
  NOTE_D4,-4, NOTE_C4,-4, NOTE_G4,-4,
  NOTE_F4,-2, NOTE_C4,4, NOTE_C4,8,

  NOTE_C5,-4, NOTE_A4,-4, NOTE_F4,-4, 
  NOTE_E4,-4, NOTE_D4,-4, NOTE_AS4,4, NOTE_AS4,8,
  NOTE_A4,-4, NOTE_F4,-4, NOTE_G4,-4,
  NOTE_F4,-2,
 
  };

int notes = sizeof(melody) / sizeof(melody[0]) / 2;

int wholenote = (60000 * 4) / tempo;

int divider = 0, noteDuration = 0;
  for (int thisNote = 0; thisNote < notes * 2; thisNote = thisNote + 2) {
    divider = melody[thisNote + 1];
    if (divider > 0) {
      noteDuration = (wholenote) / divider;
    } else if (divider < 0) {
      noteDuration = (wholenote) / abs(divider);
      noteDuration *= 1.5; 
    }
    tone(Piezo, melody[thisNote], noteDuration * 0.9);
    delay(noteDuration);
    noTone(Piezo);
  }
}

void MerryChristmas(){
  int tempo = 140;
  int melody[] = {
  NOTE_C5,4, 
  NOTE_F5,4, NOTE_F5,8, NOTE_G5,8, NOTE_F5,8, NOTE_E5,8,
  NOTE_D5,4, NOTE_D5,4, NOTE_D5,4,
  NOTE_G5,4, NOTE_G5,8, NOTE_A5,8, NOTE_G5,8, NOTE_F5,8,
  NOTE_E5,4, NOTE_C5,4, NOTE_C5,4,
  NOTE_A5,4, NOTE_A5,8, NOTE_AS5,8, NOTE_A5,8, NOTE_G5,8,
  NOTE_F5,4, NOTE_D5,4, NOTE_C5,8, NOTE_C5,8,
  NOTE_D5,4, NOTE_G5,4, NOTE_E5,4,

  NOTE_F5,2,
};
  int notes = sizeof(melody) / sizeof(melody[0]) / 2;

  int wholenote = (60000 * 4) / tempo;

  int divider = 0, noteDuration = 0;

  for (int thisNote = 0; thisNote < notes * 2; thisNote = thisNote + 2) {

    divider = melody[thisNote + 1];
    if (divider > 0) {
      noteDuration = (wholenote) / divider;
    } else if (divider < 0) {
      noteDuration = (wholenote) / abs(divider);
      noteDuration *= 1.5; 
    }

    tone(Piezo, melody[thisNote], noteDuration * 0.9);

    delay(noteDuration);

    noTone(Piezo);
  }
}

void rickroll(){
  int tempo = 114;
  int melody[] = {

  NOTE_D5,-4, NOTE_E5,-4, NOTE_A4,4, //1
  NOTE_E5,-4, NOTE_FS5,-4, NOTE_A5,16, NOTE_G5,16, NOTE_FS5,8,
  NOTE_D5,-4, NOTE_E5,-4, NOTE_A4,2,
  NOTE_A4,16, NOTE_A4,16, NOTE_B4,16, NOTE_D5,8, NOTE_D5,16,
  NOTE_D5,-4, NOTE_E5,-4, NOTE_A4,4, //repeat from 1
  NOTE_E5,-4, NOTE_FS5,-4, NOTE_A5,16, NOTE_G5,16, NOTE_FS5,8,
  NOTE_D5,-4, NOTE_E5,-4, NOTE_A4,2,
  NOTE_A4,16, NOTE_A4,16, NOTE_B4,16, NOTE_D5,8, NOTE_D5,16,
  REST,4, NOTE_B4,8, NOTE_CS5,8, NOTE_D5,8, NOTE_D5,8, NOTE_E5,8, NOTE_CS5,-8,
  NOTE_B4,16, NOTE_A4,2, REST,4, 

  REST,8, NOTE_B4,8, NOTE_B4,8, NOTE_CS5,8, NOTE_D5,8, NOTE_B4,4, NOTE_A4,8, //7
  NOTE_A5,8, REST,8, NOTE_A5,8, NOTE_E5,-4, REST,4, 
  NOTE_B4,8, NOTE_B4,8, NOTE_CS5,8, NOTE_D5,8, NOTE_B4,8, NOTE_D5,8, NOTE_E5,8, REST,8,
  REST,8, NOTE_CS5,8, NOTE_B4,8, NOTE_A4,-4, REST,4,
  REST,8, NOTE_B4,8, NOTE_B4,8, NOTE_CS5,8, NOTE_D5,8, NOTE_B4,8, NOTE_A4,4,
  NOTE_E5,8, NOTE_E5,8, NOTE_E5,8, NOTE_FS5,8, NOTE_E5,4, REST,4,
   
  NOTE_D5,2, NOTE_E5,8, NOTE_FS5,8, NOTE_D5,8, //13
  NOTE_E5,8, NOTE_E5,8, NOTE_E5,8, NOTE_FS5,8, NOTE_E5,4, NOTE_A4,4,
  REST,2, NOTE_B4,8, NOTE_CS5,8, NOTE_D5,8, NOTE_B4,8,
  REST,8, NOTE_E5,8, NOTE_FS5,8, NOTE_E5,-4, NOTE_A4,16, NOTE_B4,16, NOTE_D5,16, NOTE_B4,16,
  NOTE_FS5,-8, NOTE_FS5,-8, NOTE_E5,-4, NOTE_A4,16, NOTE_B4,16, NOTE_D5,16, NOTE_B4,16,

  NOTE_E5,-8, NOTE_E5,-8, NOTE_D5,-8, NOTE_CS5,16, NOTE_B4,-8, NOTE_A4,16, NOTE_B4,16, NOTE_D5,16, NOTE_B4,16, //18
  NOTE_D5,4, NOTE_E5,8, NOTE_CS5,-8, NOTE_B4,16, NOTE_A4,8, NOTE_A4,8, NOTE_A4,8, 
  NOTE_E5,4, NOTE_D5,2, NOTE_A4,16, NOTE_B4,16, NOTE_D5,16, NOTE_B4,16,
  NOTE_FS5,-8, NOTE_FS5,-8, NOTE_E5,-4, NOTE_A4,16, NOTE_B4,16, NOTE_D5,16, NOTE_B4,16,
  NOTE_A5,4, NOTE_CS5,8, NOTE_D5,-8, NOTE_CS5,16, NOTE_B4,8, NOTE_A4,16, NOTE_B4,16, NOTE_D5,16, NOTE_B4,16,

  NOTE_D5,4, NOTE_E5,8, NOTE_CS5,-8, NOTE_B4,16, NOTE_A4,4, NOTE_A4,8,  
  NOTE_E5,4, NOTE_D5,2, REST,4,
};
  int notes = sizeof(melody) / sizeof(melody[0]) / 2;

  int wholenote = (60000 * 4) / tempo;

  int divider = 0, noteDuration = 0;
    
  for (int thisNote = 0; thisNote < notes * 2; thisNote = thisNote + 2) {

    divider = melody[thisNote + 1];
    if (divider > 0) {
      noteDuration = (wholenote) / divider;
    } else if (divider < 0) {
      noteDuration = (wholenote) / abs(divider);
      noteDuration *= 1.5; 
    }

    tone(Piezo, melody[thisNote], noteDuration * 0.9);

    delay(noteDuration);

    noTone(Piezo);
  }
}
```
<h1> Setup </h1>
In <code>setup()</code> sensors and pins get initialized with certain parameters.


```C++
///////////////////////////////////////////////////////////////////
//-- Setup ------------------------------------------------------//
///////////////////////////////////////////////////////////////////

void setup() {
  pinMode(Piezo, OUTPUT); // pin 13 gets initialized as "OUTPUT", because a Buzzer has as an "OUTPUT" sounds
  Serial.begin(9600); // sets serial communication between digital pin at 9600 baud rate
  bluetooth.begin(9600); // sets serial communication beetween HC-06 and user's device at 9600 baud rate
  Servo_6.attach(RightArm);
  Servo_7.attach(LeftArm); // initializes RightArm and LeftArm using a function from <Servo.h>
  Otto.init(LeftLeg, RightLeg, LeftFoot, RightFoot, true, Piezo);  
  startArms(); // sets arms in a neutral position
  Otto.sing(S_connection); // makes the Buzzer play the connection sound (beep)
  Otto.home(); // sets legs and foots in a neutral position
}
```
<h1> Loop </h1>
Every time the function <code>Loop()</code> gets executed, the variable <em> message </em> (which contains the message that the user sent) passes through the switch, if bluetooth is avaible; certain chars can trigger cases of the switch and in these are called the functions that have been declared earlier.
It's important to say that the <code>Loop()</code> function is the core of this code: without it, MinioBot can't do a thing (except for the setup initializations).

```C++
///////////////////////////////////////////////////////////////////
//-- Principal Loop ---------------------------------------------//
///////////////////////////////////////////////////////////////////
void loop(){
  Serial.println("Start"); // to notice, using the serial monitor, when the loop() function gets executed
  if (bluetooth.available()) {
    message = char(bluetooth.read()); // the data memorized in the variable "message" gets overwritten with the char that the user sent using Bluetooth
    Serial.println("message="); 
    Serial.println(message); // prints what's contained in the variable "message"
    switch(message)
    {
      case 'a':
          move_RigthArm();
          break;
      case 'b':
          move_LeftArm();
          break;
      case 'c':
          bothArms();
          break;
     case 'f':
          fly();
          break;
     case 'g':
          walkForward = !walkForward;
          walkForward_si();
          break;
     case 'i':
          HappyBirthday();
          break;     
     case 'l':
          walkBackwards_si();
          break;
     case 'p':
          MerryChristmas();
          break;
     case 'n':
          TurnLeft();
          break;
     case 'o':
          TurnRight();
          break;
     case 'r':
          rickroll();
          break;
    }
  }
}
```
