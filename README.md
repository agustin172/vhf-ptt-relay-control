//constant value which will not change:
const int PTT = 6;
const int REL1 = 2;
const int REL2 = 3;
const int REL3 = 4;
const int REL4 = 5;

//values which will change:
int PTTState = 0;
int PTTPushCounter = 0;
int lastPTTState = 0;

unsigned long previousMillis = 0; // will store last time a relay was updated.
unsigned long currentMillis = 0;
unsigned long timeLastPush = 0;  //Millisek. since last time PTT was pressed..
unsigned long timeOutPTT = 10000; // Timeout in milliseconds

DFRobot_LCD lcd(16,2); // Type of LCD display set 16 characters and 2 lines.

void setup() {
// initializing lcd
lcd.init();
 // start serial port for LCD
 Serial.begin(9600);
 Serial.println("PTT READY");
 lcd.setCursor(0,0);

//Pin configuration as connected by the schematic
pinMode(PTT,INPUT);
pinMode(REL1,OUTPUT);
pinMode(REL2,OUTPUT);
pinMode(REL3,OUTPUT);
pinMode(REL4,OUTPUT);
Serial.begin(9600);
Serial.println("PTT READY");
}

void loop() {
 // read the PTT input pin:
 PTTState = digitalRead(PTT);

// compare the PTTState to its previous state
 if (PTTState != lastPTTState && lastPTTState == 0) {  //If changed PTT state AND lastPTTState = 0,
   if ((millis() - timeLastPush) > timeOutPTT ) {  //If timeout-limit for PTT reached:
 timeLastPush = millis();        //Set new timecouter start now,
 PTTPushCounter = 0;         //Set PTT counter to 0.
    }
    }
   // if the state has changed, increment the counter
   if (PTTState == HIGH) {
     // if the current state is HIGH then the PTT went from off to on:
     PTTPushCounter++;
     Serial.println("PTT PUSHED");
     Serial.print("NUMBER OF PTT PUSHES: ");
     Serial.println(PTTPushCounter);
   }
   
   // Delay a little bit to avoid bouncing
  delay(50);

// save the current state as the last state, for next time through the loop
 lastPTTState = PTTState;

if (PTTPushCounter  %4 == 3) {
   lcd.clear();
   lcd.setCursor(3,0);
   lcd.print("RELAY 1 ON!");
   digitalWrite(REL1, HIGH);
   }
         
 if  (PTTPushCounter  %6== 5) {
       lcd.clear();
   lcd.setCursor(3,0);
   lcd.print("RELAY 2 ON!");
   digitalWrite(REL2,HIGH);
 }
 if (PTTPushCounter   %8== 7) {
       lcd.clear();
   lcd.setCursor(3,0);
   lcd.print("RELAY 3 ON!");
   digitalWrite(REL3,HIGH);  
 }
 if (PTTPushCounter   %10== 9) {
       lcd.clear();
   lcd.setCursor(3,0);
   lcd.print("RELAY 4 ON!");
   digitalWrite(REL4,HIGH);
   }

timeLastPush=millis();

if ((timeLastPush - previousMillis) >= 600000) {
   // saves the last time we turned the relay on/off
   lcd.clear();
   lcd.setCursor(0,0);
   lcd.print("ALL RELAYS OFF!");
   digitalWrite(REL1,LOW);
   digitalWrite(REL2,LOW);
   digitalWrite(REL3,LOW);
   digitalWrite(REL4,LOW);
   
   previousMillis = millis();
   Serial.println(previousMillis);

}
}
