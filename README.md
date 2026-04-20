#include <Wire.h>
#include <LiquidCrystal_I2C.h>

// =========================
// CONFIGURATION
// =========================

// Distance between the two sensors (in meters)
#define DISTANCE 0.10        

// Distance threshold (in cm) to detect an object
#define DETECTION_THRESHOLD 5   

// Speed limit (in km/h) for triggering the buzzer
#define SPEED_LIMIT 1.0      

// Sensor 1 pins
#define TRIG1 2
#define ECHO1 3

// Sensor 2 pins
#define TRIG2 4
#define ECHO2 5

// Buzzer pin
#define BUZZER 6

// Initialize I2C LCD (address 0x27, 20 columns, 4 rows)
LiquidCrystal_I2C lcd(0x27, 20, 4);

// =========================
// FUNCTIONS
// =========================

// Function to measure distance using HC-SR04 (returns distance in cm)
float measureDistance(int trig, int echo) {
  digitalWrite(trig, LOW);        // Ensure trigger is LOW
  delayMicroseconds(2);

  digitalWrite(trig, HIGH);       // Send 10µs pulse to trigger
  delayMicroseconds(10);
  digitalWrite(trig, LOW);

  // Measure the duration of the echo pulse (timeout: 30ms)
  long duration = pulseIn(echo, HIGH, 30000);

  // If no signal is received, return -1 (error)
  if (duration == 0) return -1;

  // Convert time to distance (speed of sound = 0.0343 cm/µs)
  return (duration * 0.0343) / 2;
}

// Function to detect if an object is within the threshold distance
bool objectDetected(int trig, int echo) {
  float d = measureDistance(trig, echo);
  return (d > 0 && d < DETECTION_THRESHOLD);
}

// Function to display 4 lines on the LCD
void displayLCD(String l1 = "", String l2 = "", String l3 = "", String l4 = "") {
  lcd.clear();                   // Clear previous display

  lcd.setCursor(0, 0);
  lcd.print(l1);

  lcd.setCursor(0, 1);
  lcd.print(l2);

  lcd.setCursor(0, 2);
  lcd.print(l3);

  lcd.setCursor(0, 3);
  lcd.print(l4);
}

// Function to activate the buzzer for a given duration (ms)
void beep(int duration = 200) {
  digitalWrite(BUZZER, HIGH);   // Turn buzzer ON
  delay(duration);
  digitalWrite(BUZZER, LOW);    // Turn buzzer OFF
}

// =========================
// INITIALIZATION
// =========================

void setup() {
  // Configure sensor pins
  pinMode(TRIG1, OUTPUT);
  pinMode(ECHO1, INPUT);

  pinMode(TRIG2, OUTPUT);
  pinMode(ECHO2, INPUT);

  // Configure buzzer pin
  pinMode(BUZZER, OUTPUT);

  // Initialize LCD
  lcd.init();
  lcd.backlight();

  // Display startup message
  displayLCD("Arduino UNO", "SPEED RADAR", "Initializing...", "");
  delay(10000); // Wait 10 seconds
}

// =========================
// MAIN LOOP
// =========================

void loop() {

  // Display waiting message
  displayLCD("Radar active", "Waiting...", "", "");

  // Wait until an object is detected by sensor 1
  while (!objectDetected(TRIG1, ECHO1)) {
    delay(5);
  }

  // Record time when object passes sensor 1
  unsigned long t1 = micros();

  displayLCD("Vehicle detected", "Measuring...", "", "");

  // Wait until the object reaches sensor 2
  while (!objectDetected(TRIG2, ECHO2)) {
    delay(5);
  }

  // Record time when object passes sensor 2
  unsigned long t2 = micros();

  // Calculate time difference in seconds
  float delta_t = (t2 - t1) / 1000000.0;

  // Ensure valid time measurement
  if (delta_t > 0) {

    // Calculate speed (m/s → km/h)
    float speed = (DISTANCE / delta_t) * 3.6;

    // Display speed and status
    displayLCD(
      "Speed:",
      String(speed, 2) + " km/h",
      "Limit: " + String(SPEED_LIMIT) + " km/h",
      "Status:"
    );

    // Check if speed exceeds the limit
    if (speed > SPEED_LIMIT) {
      lcd.setCursor(8, 3);
      lcd.print("OVER");

      // Sound alarm multiple times
      for (int i = 0; i < 15; i++) {
        beep(150);
        delay(100);
      }

    } else {
      lcd.setCursor(8, 3);
      lcd.print("OK");

      // Short confirmation beep
      beep(80);
    }

  } else {
    // Error case if time is invalid
    displayLCD("Error", "Invalid time", "", "");
  }

  // Wait before next measurement
  delay(5000);
}
