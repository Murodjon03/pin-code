#include <Keypad.h>
#include <LiquidCrystal.h>
#include <Servo.h>

LiquidCrystal lcd(7, 6, 5, 4, 3, 2);

Servo lockServo;
#define SERVO_PIN    3
#define SERVO_LOCKED 160
#define SERVO_OPEN    60

const byte ROWS = 4, COLS = 4;
char keys[ROWS][COLS] = {
  {'1','2','3','A'},
  {'4','5','6','B'},
  {'7','8','9','C'},
  {'*','0','#','D'}
};
byte rowPins[ROWS] = {A5, A4, A3, A2};
byte colPins[COLS]  = {A1, A0, 13, 12};

Keypad keypad = Keypad(makeKeymap(keys), rowPins, colPins, ROWS, COLS);

String correctPIN = "0303";
String enteredPIN = "";

void showHome() {
  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("  ArduinoSafe   ");
  lcd.setCursor(0, 1);
  lcd.print("  PIN kiriting  ");
}

void setup() {
  Serial.begin(9600);
  lcd.begin(16, 2);
  lockServo.attach(SERVO_PIN);
  lockServo.write(SERVO_LOCKED);
  lcd.setCursor(0, 0);
  lcd.print("  ArduinoSafe   ");
  lcd.setCursor(0, 1);
  lcd.print("   v2.0 Ready   ");
  delay(2000);
  showHome();
  Serial.println("Tizim ishga tushdi. PIN kiriting:");
}

void loop() {
  char key = keypad.getKey();
  if (!key) return;

  if (key >= '0' && key <= '9') {
    enteredPIN += key;
    lcd.setCursor(0, 1);
    lcd.print("                ");
    lcd.setCursor(0, 1);
    for (unsigned int i = 0; i < enteredPIN.length(); i++) lcd.print("*");
    Serial.print("*");
  }

  if (key == '#') {
    Serial.println();
    if (enteredPIN == correctPIN) {
      Serial.println("QULF OCHILDI!");
      lcd.clear();
      lcd.setCursor(0, 0);
      lcd.print(" PIN togri!    ");
      lcd.setCursor(0, 1);
      lcd.print(" Qulf ochildi! ");
      lockServo.write(SERVO_OPEN);
      delay(5000);
      lockServo.write(SERVO_LOCKED);
      showHome();
    } else {
      Serial.println("PIN xato!");
      lcd.clear();
      lcd.setCursor(0, 0);
      lcd.print("   PIN XATO!   ");
      lcd.setCursor(0, 1);
      lcd.print(" Qayta urining ");
      delay(2000);
      showHome();
    }
    enteredPIN = "";
  }

  if (key == '*') {
    enteredPIN = "";
    lcd.setCursor(0, 1);
    lcd.print("                ");
    Serial.println("PIN tozalandi");
  }
}
