#include <Wire.h>
#include <LiquidCrystal_I2C.h>
#define swPin 2
#define RelayPin 8
#define ledRPin 12

LiquidCrystal_I2C lcd(0x27, 16, 2);

int buttonState = 1;        // สถานะปุ่มปัจจุบัน (เริ่มต้น HIGH เพราะใช้ INPUT_PULLUP)
int lastButtonReading = 1;  // ค่าที่อ่านได้ครั้งล่าสุด (ก่อน debounce)

int relayState = 0;  // เริ่มต้นให้รีเลย์ OFF
unsigned long lastDebounceTime = 0;
const unsigned long debounceDelay = 50;  // หน่วงกันเด้ง 50 ms

void setup() {

  pinMode(swPin, INPUT);
  pinMode(RelayPin, OUTPUT);
  digitalWrite(RelayPin, relayState);  // ตั้งค่ารีเลย์เริ่มต้น (OFF)
  Serial.begin(9600);
  Serial.println("Toggle Relay by Button");
  lcd.init();
  lcd.backlight();
  lcd.clear();

  lcd.setCursor(0, 0);
  lcd.print("LAB LCD+SW+Relay");
  lcd.setCursor(0, 1);
  lcd.print("Thanapat/Supadet");

  delay(2000);
  lcd.clear();
}

void loop() {
  int reading = digitalRead(swPin);

  Relay(reading);
}

void Relay(int reading) {
  if (reading != lastButtonReading) {
    lastDebounceTime = millis();  // จับเวลาไว้  ตั้งแต่เริ่ม ทำงาน
  }

  // ถ้าผ่านเวลา debounceDelay แล้ว สัญญาณนิ่งแล้ว
  if ((millis() - lastDebounceTime) > debounceDelay) {
    // และถ้าค่านี้ไม่เท่ากับ buttonState เดิม แสดงว่ามีการเปลี่ยนสถานะจริง
    if (reading != buttonState) {
      buttonState = reading;

      // ตรวจเฉพาะตอน "กดลง" (จาก HIGH -> LOW)
      if (buttonState == LOW) {
        // สลับสถานะรีเลย์ (ON <-> OFF)
        relayState = !relayState;
        digitalWrite(RelayPin, relayState);
        digitalWrite(ledRPin, relayState);

        LCD();

        Serial.print("Relay is now: ");
        Serial.println(relayState ? "ON" : "OFF");
      }
    }
  }

  // เก็บค่า reading ไว้ใช้เทียบรอบหน้า
  lastButtonReading = reading;
}

void LCD() {
  lcd.setCursor(0, 1);  // บรรทัดที่ 2
  if (relayState == HIGH) {
    lcd.print("STATE: ON      ");  // เติมช่องว่างเผื่อลบข้อความเก่า
  } else {
    lcd.print("STATE: OFF     ");
  }
  lcd.setCursor(0, 0);
  lcd.print("Relay");
}
