# CODIGO-FLUJO-SANGUINEO-CON-ALARMA
#include <Wire.h>
#include <LiquidCrystal_I2C.h>

LiquidCrystal_I2C lcd(0x27, 20, 4);  // Dirección I2C y dimensiones del LCD

const int sensorEchoPin = 9;   // Pin ECHO del sensor de flujo
const int sensorTrPin = 10;    // Pin TR del sensor de flujo
const int alarmPin = 5;        // Pin para el buzzer
const int ledPin = 8;          // Pin para el LED
const int amplificationPin = A0;  // Pin analógico para leer la señal amplificada

int sensorValue = 0;           // Variable para almacenar el valor del sensor
float flowRate = 0.0;          // Variable para almacenar el flujo
float distancia = 0.0;          // Variable para almacenar la distancia

void setup() {
  pinMode(sensorEchoPin, INPUT);
  pinMode(sensorTrPin, OUTPUT);
  pinMode(alarmPin, OUTPUT);
  pinMode(ledPin, OUTPUT);
  lcd.init();
  lcd.backlight();
  lcd.setCursor(0, 0);
  lcd.print("Sensor de Flujo");
  
  delay(2000);
  lcd.clear();
}

void loop() {
  // Realizar la medición del caudal
  digitalWrite(sensorTrPin, HIGH);
  delayMicroseconds(20);
  digitalWrite(sensorTrPin, LOW);

  float duration = pulseIn(sensorEchoPin, HIGH);
  flowRate = 1000.0 / (duration / 2.0);
  // Calcular la distancia en centímetros
  distancia = duration * 0.034 / 2;

  // Leer la señal amplificada
  sensorValue = analogRead(amplificationPin);

  // Mostrar la distancia y el caudal en la pantalla LCD
  lcd.setCursor(0, 0);
  lcd.print("Distancia:");
  lcd.print(distancia);
  lcd.print(" cm");

  lcd.setCursor(0, 1);
  lcd.print("Flujo:");
  lcd.print(flowRate, 2);
  lcd.print(" L/s");

  // Activar la alarma si la medición excede los valores de flujo sanguíneo anormales según la edad simulada
  if (flowRate > 4.5 || flowRate < 3.5) {
    digitalWrite(alarmPin, HIGH);
    digitalWrite(ledPin, HIGH);
  } else {
    digitalWrite(alarmPin, LOW);
    digitalWrite(ledPin, LOW);
  }

  delay(1000);
}
