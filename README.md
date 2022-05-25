# 20bcs065_santhana-kumar



/*

santhanakumarsa2018@gmail.com
SAkumar2003
http://arduino.esp8266.com/stable/package_esp8266com_index.json

*/

#include <LiquidCrystal.h>
#include <ESP8266WiFi.h>
#include <Wire.h>

const int FirePin = D1; 
const int relay = D2; 

String apiKey = "0P0D6XCC94H22ZWJ";     

const char *ssid =  "electronics";     
const char *pass =  "wifihotspot";
const char* server = "api.thingspeak.com";

WiFiClient client;


const  char phNU1[]="8870345424";
const  char phNU2[]="9600979800";

String line;
uint32_t tsLastReport = 0;
unsigned char i,j,k,Fire;
int entry;
boolean f1;

void setup()
{
  //Serial.begin(9600);                //uncommand
  Serial.begin(115200);                //command
  Serial.print("Connecting...");
 
  Serial.println(ssid);                  //command
  WiFi.begin(ssid, pass);                //command
  while (WiFi.status() != WL_CONNECTED) //command
  {                                     //command
    delay(500);                         //command
    Serial.println(",");                //command
  }                                     //command
 
  pinMode(relay, OUTPUT);
  digitalWrite(relay, HIGH);

  delay(500);
 
}
void loop()
{
  
  Fire=digitalRead(FirePin);
 // f1=1; //uncommand
  
  if (Fire==0)
  {
    
    Serial.println("fire");
    
    if (client.connect(server, 80))  
    {
      
      String postStr = apiKey;
      postStr += "&field1=1";
     // postStr += String(Fire);
      postStr += "\r\n\r\n";
      client.print("POST /update HTTP/1.1\n");
      client.print("Host: api.thingspeak.com\n");
      client.print("Connection: close\n");
      client.print("X-THINGSPEAKAPIKEY: " + apiKey + "\n");
      client.print("Content-Type: application/x-www-form-urlencoded\n");
      client.print("Content-Length: ");
      client.print(postStr.length());
      client.print("\n\n");
      client.print(postStr);
      Serial.println("Content-Length: ");
      Serial.println(postStr.length());
      Serial.println("%. Send to Thingspeak.");
      Serial.println(entry);
      delay(1000);
      client.stop();
      f1=1;
     }
     if(f1==1)
     {
      GSM_MODEM_Send(phNU1,"Fire detected");
      delay(1000);
      GSM_MODEM_Send(phNU2,"Fire detected");
      delay(1000);
      digitalWrite(relay, LOW);
      entry++;
      f1=0;
     }
     }
 
   if (Fire==1)
    {
      digitalWrite(relay, HIGH);
    }
    
 }
    
 void GSM_MODEM_Enter()
{
  Serial.write(0x0D);
  Serial.write(0x0a);
  delay(500);
}
void GSM_MODEM_Init()
{
  Serial.print("AT");
  GSM_MODEM_Enter();
  Serial.print("AT");
  GSM_MODEM_Enter();
  Serial.print("AT+CSCS=");
  Serial.write('"');
  Serial.print("GSM");
  Serial.write('"');
  GSM_MODEM_Enter();
  Serial.print("AT+CMGF=1");
  GSM_MODEM_Enter();
  Serial.print("AT+CSMP=17,167,0,0");
  GSM_MODEM_Enter();
  Serial.print("AT+CNMI=2,2,0,0,0");
  GSM_MODEM_Enter();
}

void GSM_MODEM_Send(const  char *Number, const  char *Message)
{
  GSM_MODEM_Init();
  Serial.print("AT+CMGS=");
  Serial.write('"');
  while (*Number)
  {
    Serial.write(*Number++);
  }
  Serial.write('"');
  delay(500);
  GSM_MODEM_Enter();
  delay(500);
  while (*Message)
  {
    Serial.write(*Message++);
  }
  Serial.write(0x1A);
  delay(500);
}
