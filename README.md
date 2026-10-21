# invernadero-inteligente-
<img width="1002" height="717" alt="image" src="https://github.com/user-attachments/assets/163ca566-0f5e-4d70-805c-dfeec880e095" />
<img width="974" height="756" alt="image" src="https://github.com/user-attachments/assets/710143f1-883a-4474-81cc-ca7c53fdb93e" />

#include <Adafruit_LiquidCrystal.h>
#include <Servo.h>

int TEMPERATURA = 0;
int HUMEDAD = 0;

Adafruit_LiquidCrystal lcd_1(0);
Servo servo_7;

void setup()
{
  lcd_1.begin(16, 2);
  pinMode(A0, INPUT);
  pinMode(A1, INPUT);
  Serial.begin(9600);
  
  servo_7.attach(7, 500, 2500);
  pinMode(8, OUTPUT);
  // pinMode(12, OUTPUT); // Pin 12 de la bomba eliminado
  pinMode(13, OUTPUT); // Pin para el buzzer

  lcd_1.setBacklight(1);
}

void loop()
{
  // Cálculo de temperatura y humedad
  TEMPERATURA = (-40 + 0.488155 * (analogRead(A0) - 20));
  HUMEDAD = map(analogRead(A1), 0, 1023, 0, 117);

  // Mostrar Temperatura en LCD y Monitor Serial
  lcd_1.setCursor(0, 0);
  lcd_1.print("T=      "); // Espacios para limpiar residuos anteriores
  lcd_1.setCursor(2, 0);
  lcd_1.print(TEMPERATURA);
  Serial.print("Temperatura: ");
  Serial.println(TEMPERATURA);

  // Mostrar Humedad en LCD y Monitor Serial
  lcd_1.setCursor(0, 1);
  lcd_1.print("H=      "); // Espacios para limpiar residuos anteriores
  lcd_1.setCursor(2, 1);
  lcd_1.print(HUMEDAD);
  Serial.print("Humedad: ");
  Serial.println(HUMEDAD);

  // Valores predeterminados (apagado / posición inicial)
  servo_7.write(0);
  digitalWrite(8, LOW);

  // Control del Servo según la temperatura
  if (TEMPERATURA > 30 && TEMPERATURA <= 40) {
    servo_7.write(90);
  } 
  
  // Control de Alarma y Servo para temperaturas más altas
  if (TEMPERATURA > 40 && TEMPERATURA <= 50) {
    servo_7.write(90);
    digitalWrite(8, HIGH); // Encender pin 8
    tone(13, 1661, 1000);  // Tono de alarma por temperatura
  }

  // Control de Humedad: Suena el buzzer si está baja (< 30) o alta (> 70)
  if (HUMEDAD < 30 || HUMEDAD > 70) {
    tone(13, 1200, 500); // Tono de alarma por humedad en el pin 13
  }

  delay(500); // Pausa para que se alcance a leer bien en la simulación
}
