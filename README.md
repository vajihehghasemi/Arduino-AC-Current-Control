# Arduino AC Current Control

Control AC 230V AC devices using a TRIAC and Arduino for dimming or power regulation.

## Description
This project demonstrates how to control AC current to appliances using a TRIAC and Arduino. It allows you to dim lights,heat or regulate power safely.

**⚠️ Warning:** AC 230V is dangerous. Make sure to follow safety precautions.

## Components
- Arduino (Uno, Nano, etc.)
- TRIAC (T1635H-6T)
- Opto-isolator ( MOC3021)
- AC load (heater, etc.)
- Resistors, Diode, Capacitors as required
- Breadboard / PCB and wires

## Code
Upload the Arduino sketch in the `Arduino` folder:

int zeroc = 2;         // zero cross detector input pin
int triac = 3;         // triac gate pin
int vresistor = A0;    // potentiometer pin
int current;           // delay amount

volatile bool fireTriac = false; // flag set by interrupt
volatile int delayTime = 0;      // delay for triac firing

void setup() {
  Serial.begin(9600);

  pinMode(triac, OUTPUT);
  pinMode(zeroc, INPUT);
  pinMode(vresistor, INPUT);

  attachInterrupt(digitalPinToInterrupt(zeroc), zeroCross, RISING);
}

void loop() {
  // Read potentiometer outside interrupt
  current = analogRead(vresistor);
  delayTime = map(current, 0, 1023, 0, 10000);

  Serial.println(delayTime);

  // If zero-cross happened, fire the triac
  if (fireTriac) {
    fireTriac = false;

    delayMicroseconds(delayTime);
    digitalWrite(triac, HIGH);
    delayMicroseconds(50);
    digitalWrite(triac, LOW);
  }
}

// interrupt routine
void zeroCross() {
  fireTriac = true;
}

