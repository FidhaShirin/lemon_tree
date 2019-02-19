#hvac
#include <LiquidCrystal.h>

LiquidCrystal lcd(7,6,5,4,3,2);   //defining lcd pins
int value=0; //initializing variables
float volts=0.0; 
float temp=0.0;

int output=0;

int ac_control;
int switch_now=0;
int low=0;

#define ac_indicator 10
#define TEMP_MIN  16
#define CONDITIONER_PIN   11
#define OK_LED_PIN    	9
#define TEMP_SENSOR_PIN	A0
#define POTENTIOMETER A1
#define switchpin 8

void setup()
{
  pinMode(ac_indicator,OUTPUT);
  pinMode(switchpin, INPUT);
  pinMode(CONDITIONER_PIN,OUTPUT);
  pinMode(OK_LED_PIN,OUTPUT); 
  pinMode(ac_control,INPUT);
  pinMode(TEMP_SENSOR_PIN,INPUT);      //setting arduino pin3 as input
  Serial.begin(9600);   // opens serial port, sets data rate to 9600 bps
  lcd.begin(16,2);      // set up the LCD's number of columns and rows
}

void loop()
{
  switch_now = digitalRead(switchpin);
  ac_control = analogRead(A1);
  value=analogRead(A0);			  //read from A0
  volts=(value/1024.0)*5.0;      //conversion to volts
  temp= volts*100.0;             //conversion to temp Celsius
  
  
  Serial.print("temperature= "); 
  Serial.println(temp);
  lcd.setCursor(0,0);
  lcd.print("TEMP= ");
  lcd.print(temp);
  lcd.print(" C");
  lcd.setCursor(0,1);
  
  delay(500);
  if( switch_now==HIGH)
  {
    
    lcd.print("AC on ");
    if (temp > TEMP_MIN) 
    {  
    digitalWrite(OK_LED_PIN, HIGH);
    digitalWrite(ac_indicator,HIGH);
    output = (ac_control)/4;
    Serial.println(output);
    analogWrite(CONDITIONER_PIN, output);
    delay(100);
        
	} else {
        digitalWrite(ac_indicator,LOW);
        digitalWrite(CONDITIONER_PIN, LOW);
        digitalWrite(OK_LED_PIN, HIGH);
	}
  }
  else{
    digitalWrite(ac_indicator,LOW);
    digitalWrite(OK_LED_PIN, LOW);
    analogWrite(CONDITIONER_PIN, low);
    lcd.print("AC off");
  } 
}
