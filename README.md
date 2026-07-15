#include <Adafruit_LiquidCrystal.h>

Adafruit_LiquidCrystal lcd(0);

// ---------------- Pins ----------------
const int buzzerPin = 8;
const int startBtn = 2;
const int pauseBtn = 3;
const int stopBtn = 4;
const int extraBtn = 5;

// ---------------- Timer ----------------
int minutes = 25;
int seconds = 0;

bool running = false;
bool paused = false;

// ---------------- Messages ----------------
const char* messages[] = {
  "Stay Strong!",
  "Keep Going!",
  "You Got This!",
  "Study Smart!",
  "Never Give Up!"
};

const int numMessages = sizeof(messages) / sizeof(messages[0]);

int msgIndex = 0;

// ---------------- Time ----------------
unsigned long lastSecond = 0;
unsigned long lastMessage = 0;

void setup() {
  
  Serial.begin(9600);

  pinMode(buzzerPin, OUTPUT);

  pinMode(startBtn, INPUT_PULLUP);
  pinMode(pauseBtn, INPUT_PULLUP);
  pinMode(stopBtn, INPUT_PULLUP);
  pinMode(extraBtn, INPUT_PULLUP);

  lcd.begin(16, 2);

  lcd.setCursor(0, 0);
  lcd.print(" Study Timer ");
  lcd.setCursor(0, 1);
  lcd.print(" Ready!");
  delay(2000);

  lcd.clear();
}

void loop() {

  // ---------- START ----------
  if (digitalRead(startBtn) == LOW) {
    running = true;
    paused = false;
    lastSecond = millis();
    
      lcd.clear();
  	  lcd.setCursor(0, 0);
  	  lcd.print("START PRESSED");
    
    delay(500);
  }

  // ---------- PAUSE ----------
  if (digitalRead(pauseBtn) == LOW) {
    if (running) {
      paused = !paused;
    }
    delay(200);
  }

  // ---------- STOP ----------
  if (digitalRead(stopBtn) == LOW) {

    running = false;
    paused = false;

    minutes = 25;
    seconds = 0;

    lcd.clear();
    lcd.setCursor(0, 0);
    lcd.print("Timer Reset");
    delay(1000);
    lcd.clear();
  }

  // ---------- EXTRA MINUTE ----------
  if (digitalRead(extraBtn) == LOW) {

    minutes++;

    lcd.clear();
    lcd.setCursor(0, 0);
    lcd.print("+1 Minute");
    delay(700);
    lcd.clear();
  }

  // ---------- TIMER ----------
  if (running && !paused) {

  lcd.setCursor(0, 1);
  lcd.print("RUNNING        ");

  if (millis() - lastSecond >= 1000) {

      lastSecond = millis();

      if (seconds == 0) {

        if (minutes == 0) {

          running = false;

          lcd.clear();
          lcd.setCursor(0, 0);
          lcd.print("Great Job!");

          lcd.setCursor(0, 1);
          lcd.print("Session Done");

          tone(buzzerPin, 1000);
          delay(3000);
          noTone(buzzerPin);

          while (1);

        } else {

          minutes--;
          seconds = 59;

        }

      } else {

        seconds--;

      }

    }

  }

  // ---------- CHANGE MESSAGE ----------
  if (millis() - lastMessage >= 10000) {

    lastMessage = millis();

    msgIndex++;

    if (msgIndex >= numMessages) {
      msgIndex = 0;
    }

  }

  // ---------- DISPLAY ----------
  lcd.setCursor(0, 0);

  char line1[17];
  sprintf(line1, "Time %02d:%02d", minutes, seconds);

  lcd.print("                ");
  lcd.setCursor(0, 0);
  lcd.print(line1);

  lcd.setCursor(0, 1);
  lcd.print("                ");
  lcd.setCursor(0, 1);
  lcd.print(messages[msgIndex]);

}
