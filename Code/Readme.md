#define ledPin 12
#define RelayPin 8
#define sw1Pin 2

int stateSW = 1  //สถานะปุ่มปัจจุบัน

  void setup() {
  // put your setup code here, to run once:
  Serial.begin(9600);
  pinMode(ledPin, OUTPUT);
  pinMode(RelayPin, OUTPUT);

  digitalWrite(ledPin, 0);
}

void loop() {
  // put your main code here, to run repeatedly:
  int readsw = digitalRead(sw1Pin);  //read sw1

  int bottomState = readsw;

  if (readsw != StateSWe){

  }

  if (readsw= 1) {
    digitalWrite(ledPin, 1);

  }

  else if (readsw= 0) {
    digitalWrite(ledPin, 1);
  }
}
