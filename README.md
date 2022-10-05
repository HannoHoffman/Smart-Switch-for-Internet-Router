# Smart-Switch-for-Internet-Router
For my first Github, I will demonstrate how I managed to create a smart switch to switch a router off and on every set number of hours.

The hours can be adjusted online. There is also a button to immedediately reset the ESP32. 
Then I also added a test button to test if the system is online or not. 

For the project you will need:
- ESP32 (or Arduino Uno for non-online applications)
- 1ch Relay (the one I used is 12V, so ensure you use the correct size and voltage) 
- buck converter
- Male DC plug,
- Female DC plug


The schematic is as follows:

Female DC pin is connected to:
- Negative pin to the Neg of the 12V Relay 
- Negative pin to the buck converter input (IN-)
- Positive pin to the Pos of the 12V relay
- Positive pin to the Pos of the Buck converter (IN+). 

The Buck converter is then set to 3.3VDC by turning the resistor anticlock-wise untill 3.3V is measured with a Multimeter. 

The output of the Buck converter (3.3V) is then connected to the Ground and 3.3V pin on the ESP32 from the (Vout-) and (Vout+) respectively. 

A wire is then connected from pin 14 (of ESP32) to the signal pin on the Relay. 

Male DC plug was connected to: 

- The Negative of the DC plug was connected to the 12V Relay Negative wire. 
- The positive of the output Male pin was connected to the NC (Normally close) and 
- the Pos Input from the 12V relay was connected to Com (Common) (this is where the switch is. Take note that it must be Normally close) 



![IMG-20220927-WA0031](https://user-images.githubusercontent.com/115072716/194025926-5ea73251-9346-4ee5-a341-39f3e6ccded0.jpeg)

![IMG-20220928-WA0025](https://user-images.githubusercontent.com/115072716/194027957-32f0f57b-5a13-470a-8ce1-2027c968c86a.jpeg)






*Variables*:

![image](https://user-images.githubusercontent.com/115072716/194028228-205c5a6b-7a01-4543-80be-8470190f3e8a.png)

*Online Dashboard*:

![image](https://user-images.githubusercontent.com/115072716/194028343-faa9e9e9-82cd-4529-ade1-94a167d6cd89.png)



The code was done in the Arduino cloud environment 

I also added a test button to determine whether the ESP is online or not. (You can remove this if you like to) 

https://create.arduino.cc/iot/things

/* 
  int reset_value;
  bool relay1;

*/

#include "thingProperties.h"

#define relay1_pin 14

int wag =4000;
int teller =0;
  
unsigned long currentMillis;
unsigned long previousMillis = 0;
unsigned long tyd = 0;
unsigned long interval = 30000;


void setup() {
  pinMode(relay1_pin, OUTPUT);
  Serial.begin(9600);
  delay(1500); 
  initProperties();
  ArduinoCloud.begin(ArduinoIoTPreferredConnection);
  setDebugMessageLevel(2);
  ArduinoCloud.printDebugInfo();
}


void loop() {
  ArduinoCloud.update();
  currentMillis = millis();
  digitalWrite(relay1_pin, LOW);
  
  if (currentMillis - previousMillis >= interval) {
    relay_aan();
    previousMillis = currentMillis;
  }
}



void onRelay1Change()  {
    if (relay1 == 1) {
      relay_aan();
      
    }
}


void relay_aan(){
    teller = teller+1;
    reset_value= teller;
    digitalWrite(relay1_pin, HIGH);
    delay(wag);
    digitalWrite(relay1_pin, LOW);
    }


/*
  Since TestButton is READ_WRITE variable, onTestButtonChange() is
  executed every time a new value is received from IoT Cloud.
*/
void onTestButtonChange()  {
  if(test_button==1){
    test_value=1;
  }
  else {
    test_value =0;
  }
}
/*
  Since TimeOn is READ_WRITE variable, onTimeOnChange() is
  executed every time a new value is received from IoT Cloud.
*/
void onTimeOnChange()  {
  interval = 1000*60*60*time_on;
}

