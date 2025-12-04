

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

