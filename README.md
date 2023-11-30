#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

int ledPrincipal = 9;
int ledRojo = 10;
int ledAmarillo = 11;
int ledVerde = 12;
int sensorSonido = 8;
int buzzer = 7;

int clapCount = 0;
bool juegoActivado = true;
int aplausosNivel[] = {0, 3, 5, 8};

Adafruit_SSD1306 display(128, 64, &Wire, -1);

void setup() {
  pinMode(ledPrincipal, OUTPUT);
  pinMode(ledRojo, OUTPUT);
  pinMode(ledAmarillo, OUTPUT);
  pinMode(ledVerde, OUTPUT);
  pinMode(sensorSonido, INPUT);
  pinMode(buzzer, OUTPUT);

  display.begin(SSD1306_SWITCHCAPVCC, 0x3C);
  display.clearDisplay();
  display.setTextColor(SSD1306_WHITE);
  display.setTextSize(1);
  display.setCursor(0, 0);
  
  tone(buzzer, 1000);
  delay(100);
  noTone(buzzer);
  
  display.println("Aplausometro");
  display.display();
  delay(2000);

  display.clearDisplay();
  display.setCursor(0, 0);
  display.println("Iniciando juego");
  display.display();
  delay(2000);
}

void loop() {
  int sensorValue = digitalRead(sensorSonido);

  if (juegoActivado && sensorValue == HIGH) {
    clapCount++;

    if (clapCount >= aplausosNivel[1] && clapCount < aplausosNivel[2]) {
      digitalWrite(ledRojo, HIGH);
      tone(buzzer, 2000);
      delay(100);
      noTone(buzzer);
    } 
    else if (clapCount >= aplausosNivel[2] && clapCount < aplausosNivel[3]) {
      digitalWrite(ledAmarillo, HIGH);
      tone(buzzer, 2000);
      delay(100);
      noTone(buzzer);
    } 
    else if (clapCount >= aplausosNivel[3]) {
      digitalWrite(ledVerde, HIGH);
      delay(1000);
      delay(1000);

      for (int i = ledPrincipal; i <= ledVerde; ++i) {
        digitalWrite(i, HIGH);
        tone(buzzer, 2000);
        delay(100);
        digitalWrite(i, LOW);
        noTone(buzzer);
      }
      for (int i = ledVerde; i >= ledPrincipal; --i) {
        digitalWrite(i, HIGH);
        tone(buzzer, 2000);
        delay(100);
        digitalWrite(i, LOW);
        noTone(buzzer);
      }
      delay(1000);

      digitalWrite(ledPrincipal, HIGH);

      delay(10000);

      clapCount = 0;
      digitalWrite(ledRojo, LOW);
      digitalWrite(ledAmarillo, LOW);
      digitalWrite(ledVerde, LOW);
      juegoActivado = false;
      display.clearDisplay();
      display.setCursor(0, 0);
      display.println("Juego completado.");
      display.display();
    }
  }

  if (!juegoActivado && sensorValue == HIGH) {
    juegoActivado = true;
    display.clearDisplay();
    display.setCursor(0, 0);
    display.println("Gracias por jugar.");
    display.display();
    delay(200);
  }
}
