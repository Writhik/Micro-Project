#include <SPI.h>
#include <MFRC522.h>
#include <LiquidCrystal_I2C.h>
LiquidCrystal_I2C lcd(0x27, 16, 2);
//we used arduino mega
 int led1=6; //select port
 int led2=7;
 int led3=8;
 
#define RST_PIN 5
#define SS_PIN 53
 
byte readCard[4];
String MasterTag = "D7E48C19";  // REPLACE this Tag ID with your Tag ID!!!
String RechargeTag = "E7C49F7A";  // REPLACE this Tag ID with your Tag ID!!!

int MasterTagAmount = 15; 

String tagID = "";
boolean secondTap=false;
boolean RechargeSecondTap=false;
boolean MasterTagRechargeable=false;

unsigned long timeBegin;
unsigned long timeEnd;
unsigned long duration;
double durationSce;
double fare;
 
// Create instances
MFRC522 mfrc522(SS_PIN, RST_PIN);

void setup() 
{
  lcd.begin();
  lcd.backlight();
  SPI.begin(); // SPI bus
  mfrc522.PCD_Init(); // MFRC522
   pinMode(led1,OUTPUT);
   pinMode(led2,OUTPUT);
   pinMode(led3,OUTPUT);
  lcd.clear();
  lcd.print("Bus Fare Collect");
  lcd.setCursor(0, 1);
  lcd.print("Tap Your Card>>");
}
 
void loop() 
{
    Serial.begin(9600);
  //Wait until new tag is available
  while (getID()) 
  {
    lcd.clear();
    lcd.setCursor(0, 0);

    if(tagID == RechargeTag){
      if(RechargeSecondTap==false)  {  
        RechargeSecondTap=true; 
        lcd.print("RechargeMode ON");
        MasterTagRechargeable = true;
        digitalWrite(led1,LOW);
        digitalWrite(led2,LOW);
        digitalWrite(led3,HIGH); 
      }
      else{
        RechargeSecondTap=false; 
        lcd.print("RechargeMode OFF");
        MasterTagRechargeable = false; 
        digitalWrite(led1,LOW);
        digitalWrite(led2,LOW);  
        digitalWrite(led3,HIGH); 
      }       
    }

    if (tagID == MasterTag) 
    {
      if(secondTap==false)  {  
        if(MasterTagRechargeable == true){
          lcd.print("Recharge success");
          MasterTagAmount += 40;
          MasterTagRechargeable = false;
          digitalWrite(led1,HIGH); 
          digitalWrite(led2,LOW);
          digitalWrite(led3,LOW);
        }        
        else if(MasterTagAmount>=10){
          secondTap=true;
          lcd.print("Happy Traveling!");
          timeBegin = micros();
          digitalWrite(led1,HIGH); 
          digitalWrite(led2,LOW);
          digitalWrite(led3,LOW);
        } 
        else{
          secondTap=false;
          lcd.print("Balance Low");
          digitalWrite(led2,HIGH); 
          digitalWrite(led1,LOW);
          digitalWrite(led3,LOW); 
        }
        
      }
      else{
        secondTap=false; 
        timeEnd = micros();
        duration = timeEnd - timeBegin;
        durationSce = (double)duration/1000000.0;
        Serial.print("duration: ");
        Serial.println(durationSce);
        fare = durationSce*0.5;
        MasterTagAmount = MasterTagAmount - fare;
        lcd.print("fare is ");
        lcd.print(fare);
        lcd.print(" taka!");
        digitalWrite(led1,HIGH); 
        digitalWrite(led2,LOW);
        digitalWrite(led3,LOW);       
      }              
      // You can write any code here like opening doors, switching on a relay, lighting up an LED, or anything else you can think of.
    }
    
    else
    {
       digitalWrite(led2,HIGH); 
       digitalWrite(led1,LOW);  
       digitalWrite(led3,LOW);  
       lcd.print(" Access Denied!");
       
    }
     
      lcd.setCursor(0, 1);
      lcd.print("Balance : ");
      lcd.print(MasterTagAmount);
       
    delay(2000);
     digitalWrite(led2,LOW); 
     digitalWrite(led1,LOW);
     digitalWrite(led3,LOW);
    lcd.clear();
    lcd.print("Bus Fare Collect");
    lcd.setCursor(0, 1);
    lcd.print("Tap Your Card>>");
  }
}
 
//Read new tag if available
boolean getID() 
{
  // Getting ready for Reading PICCs
  if ( ! mfrc522.PICC_IsNewCardPresent()) { //If a new PICC placed to RFID reader continue
  return false;
  }
  if ( ! mfrc522.PICC_ReadCardSerial()) { //Since a PICC placed get Serial and continue
  return false;
  }
  tagID = "";
  for ( uint8_t i = 0; i < 4; i++) { // The MIFARE PICCs that we use have 4 byte UID
  //readCard[i] = mfrc522.uid.uidByte[i];
  tagID.concat(String(mfrc522.uid.uidByte[i], HEX)); // Adds the 4 bytes in a single String variable
  }
  tagID.toUpperCase();
  mfrc522.PICC_HaltA(); // Stop reading
  return true;
}
