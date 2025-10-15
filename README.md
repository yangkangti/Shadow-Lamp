# Shadow-Lamp
const int RED_PIN = 9;
const int GREEN_PIN = 10;
const int BLUE_PIN = 11;
const int BUTTON_PIN = 2;
int colorState = 0; 
const int MAX_STATES = 7;
int lastButtonState = HIGH;
unsigned long lastChangeTime = 0;
const int changeInterval = 30;
int dynamicHue = 0; 
void setColor(int redValue, int greenValue, int blueValue) {
  analogWrite(RED_PIN, redValue);
  analogWrite(GREEN_PIN, greenValue);
  analogWrite(BLUE_PIN, blueValue);
}
void setStaticColor() {
  switch (colorState) {
    case 0: 
      setColor(0, 0, 0);
      break;
    case 1: 
      setColor(255, 0, 0);
      break;
    case 2:
      setColor(0, 0, 255);
      break;
    case 3: 
      setColor(0, 255, 0);
      break;
    case 4: 
      setColor(255, 0, 255);
      break;
    case 5: 
      setColor(0, 255, 255);
      break;
    case 6: 
      setColor(255, 255, 0);
      break;
  }
}

void dynamicEffect() {
  if (millis() - lastChangeTime >= changeInterval) {
    lastChangeTime = millis();
    dynamicHue++;
    if (dynamicHue > 255) {
      dynamicHue = 0;
    }
    int r = 0, g = 0, b = 0;
    if (dynamicHue < 85) { 
      r = 255 - dynamicHue * 3;
      g = dynamicHue * 3;
      b = 0;
    } else if (dynamicHue < 170) { 
      r = 0;
      g = 255 - (dynamicHue - 85) * 3;
      b = (dynamicHue - 85) * 3;
    } else { 
      r = (dynamicHue - 170) * 3;
      g = 0;
      b = 255 - (dynamicHue - 170) * 3;
    }
    setColor(r, g, b);
  }
}

void setup() {
  pinMode(RED_PIN, OUTPUT);
  pinMode(GREEN_PIN, OUTPUT);
  pinMode(BLUE_PIN, OUTPUT);
  pinMode(BUTTON_PIN, INPUT_PULLUP); 

  setColor(0, 0, 0);
  
  Serial.begin(9600); 
}

void loop() {
  int currentButtonState = digitalRead(BUTTON_PIN);
  if (currentButtonState == LOW && lastButtonState == HIGH) {
    colorState++;
    if (colorState > MAX_STATES) {
      colorState = 0;
    }
    if (colorState <= 6) {
      setStaticColor(); 
    }
    delay(200); 
  }
  if (colorState == 7) {
    dynamicEffect();
  }
  lastButtonState = currentButtonState;
}
