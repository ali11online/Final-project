#include <SoftwareSerial.h> // Decleration for Tx and Rx
SoftwareSerial BT(2, 3); //TX, RX respetively          

                    //H-bridge pinout  ( these are the variables connected to the Arduino board )
int enable1= 10;                
int in_put1 = 9;                    
int  in_put2 = 8;                    
int in_put3 = 7;                    
int in_put4 = 6;                    
int enable2 = 5 ;   
                 
const int Right = A0;    //Right IR sensor  
            
const int Left = A2;     //Left IR sensor         
 
String rxdata; // store many charecters
 
int modeFlag = 1; // Execution mode for sensing process

void setup(){ // put the setup code here, to run once
                             
Serial.begin(9600);  // start serial communication at 9600bps serial.begin(9600);
                    
BT.begin(9600);      // for the serial port to start it
Serial.println("New code has been uploaded successfully");
pinMode(enable1, OUTPUT); // sets the digitals as output
pinMode(in_put1, OUTPUT);
pinMode(in_put3, OUTPUT);
pinMode(in_put4, OUTPUT); 
pinMode(in_put2 , OUTPUT);
pinMode(enable2, OUTPUT);
analogWrite(enable1, 50);   // speed motor 1 
analogWrite(enable2, 50);   // speed motor 2
pinMode(Right, INPUT);  // initialize Right sensor as an input
pinMode(Left, INPUT);   // initialize Left sensor as an input
delay(500); // waits for half a second
}
//-----Functions---
void forward()                
{           //to move the robot in forword direction 
digitalWrite(in_put1, HIGH); //sets the digital pin 1 on
digitalWrite(in_put2, LOW);  //sets the digital pin 13 off
digitalWrite(in_put3, LOW);  
digitalWrite(in_put4, HIGH);  
}
 
void backward(){        //to move the robot in reverse (backword)     
digitalWrite(in_put1, LOW);  
digitalWrite(in_put2, HIGH);      
digitalWrite(in_put3, HIGH);
digitalWrite(in_put4, LOW);  
}
 
void turnRight(){      //turn the robot to the Right      
digitalWrite(in_put1, LOW);  
digitalWrite(in_put2, HIGH);
digitalWrite(in_put3, LOW);  
digitalWrite(in_put4, HIGH);  
}
 
void turnLeft(){     //turn the robot to the Left         
digitalWrite(in_put1, HIGH);
digitalWrite(in_put2, LOW);  
digitalWrite(in_put3, HIGH);  
digitalWrite(in_put4, LOW);  
}
 
void Stop()
{            //to stop the robot, here all the motors go off    
             
digitalWrite(in_put1, LOW);
digitalWrite(in_put2, LOW);  
digitalWrite(in_put3, LOW);
digitalWrite(in_put4, LOW);  
}
//--------------
 
void loop(){  
while (BT.available()){   //  if there is data available on the serial port or if there is data come from the app, it must to meet the condition
  delay(10);
  char c = BT.read();     // to read the available data and put it in C variable
  rxdata = c;
  }
  if (rxdata.length() > 0) {  //if data is sent from app to bluetooth, then it reads it and saves it in states
    Serial.println(rxdata);  
    if(rxdata == "8"){
      Serial.println("Moving forward");
      forward();
     }
     if(rxdata == "2"){
      Serial.println("Moving backward");
      backward();
     }
     if(rxdata == "6"){
      Serial.println("turning right");
      turnRight();
     }
     if(rxdata == "4"){
      Serial.println("turning left");
      turnLeft();
     }
     if(rxdata == "5"){
      Serial.println("Stopped");
      Stop();
     }
     if(rxdata == "7"){
      Serial.println("mode is set to auto line following");
      Stop();
      modeFlag = 0;
     }  
     if(rxdata == "9"){
      Serial.println("mode is set to manual app control");
      modeFlag = 1;
     }
  }        
//---------line following-----  
if (modeFlag == 0){
  if(digitalRead(Right) == 0 && digitalRead(Left) == 0){    //if Right Sensor and Left Sensor are not detecting a black line
    forward();  // it will move forward                                                  
  }
  if(digitalRead(Right) == 1 && digitalRead(Left) == 0){    //if Right Sensor is detecting a Black line and Left Sensor is not detecting
    turnRight(); // it will turn right
  }
  if(digitalRead(Right) == 0 && digitalRead(Left) == 1){    //if Right Sensor is not detecting a black line and Left Sensor is detecting 
    turnLeft();  // it will turn left                                         
    }
  if (digitalRead(Right) == 1 && digitalRead(Left) == 1){   //if Right Sensor and Left Sensor are both detecting the Black line or any black color
    Stop();}     // it will stop        
  }
}
