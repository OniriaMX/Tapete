
//Mux control pins for analog
const byte s0 = 7;
const byte s1 = 8;
const byte s2 = 9;
const byte s3 = 10;

//Mux control pins for Output signal (OUT_pin)
const byte w0 = 2; 
const byte w1 = 3;
const byte w2 = 4;
const byte w3 = 5;

const boolean muxChannel[16][4]={
    {0,0,0,0}, //channel 0
    {1,0,0,0}, //channel 1
    {0,1,0,0}, //channel 2
    {1,1,0,0}, //channel 3
    {0,0,1,0}, //channel 4
    {1,0,1,0}, //channel 5
    {0,1,1,0}, //channel 6
    {1,1,1,0}, //channel 7
    {0,0,0,1}, //channel 8
    {1,0,0,1}, //channel 9
    {0,1,0,1}, //channel 10
    {1,1,0,1}, //channel 11
    {0,0,1,1}, //channel 12
    {1,0,1,1}, //channel 13
    {0,1,1,1}, //channel 14
    {1,1,1,1}  //channel 15
  };

//Mux in "SIG" pin
const byte SIG_pin = 0;

//Mux out "SIG" pin 
const byte OUT_pin = 6;

//Row and Column pins
const byte ROW_pin = 12;
const byte COL_pin = 11;

//incoming serial byte
int inByte = 0;

int valor = 0;               //variable for sending bytes to processing
int calibra[15][15];         //Calibration array for the min values of each od the 225 sensors.
int minsensor=254;          //Variable for staring the min array
int multiplier = 254;
int pastmatrix[15][15];

void setup(){
    
  pinMode(s0, OUTPUT); 
  pinMode(s1, OUTPUT); 
  pinMode(s2, OUTPUT); 
  pinMode(s3, OUTPUT); 
  
  pinMode(w0, OUTPUT); 
  pinMode(w1, OUTPUT); 
  pinMode(w2, OUTPUT); 
  pinMode(w3, OUTPUT); 
  
  pinMode(OUT_pin, OUTPUT); 
  
  pinMode(ROW_pin, OUTPUT);
  pinMode(COL_pin, OUTPUT);
//  pinMode(SIG_pin, INPUT_PULLUP);
  
  digitalWrite(s0, LOW);
  digitalWrite(s1, LOW);
  digitalWrite(s2, LOW);
  digitalWrite(s3, LOW);
  
  digitalWrite(w0, LOW);
  digitalWrite(w1, LOW);
  digitalWrite(w2, LOW);
  digitalWrite(w3, LOW);
  
  digitalWrite(OUT_pin, HIGH);
  digitalWrite(ROW_pin, HIGH);
  digitalWrite(COL_pin, HIGH);
  
 
  
  Serial.begin(115200);
  
  Serial.println("\n\nCalibrando...\n");
  
  // Full of 0's of initial matrix
  for(byte j = 0; j < 15; j ++){ 
    writeMux(j);
    for(byte i = 0; i < 15; i ++)
      calibra[j][i] = 0;
  }
  
  // Calibration
  for(byte k = 0; k < 10; k++){  
    for(byte j = 0; j < 15; j ++){ 
      writeMux(j);
      for(byte i = 0; i < 15; i ++)
        calibra[j][i] = calibra[j][i] + readMux(i);
    }
  }
  
  //Print averages
  for(byte j = 0; j < 15; j ++){ 
    writeMux(j);
    for(byte i = 0; i < 15; i ++){
      calibra[j][i] = calibra[j][i]/10;
      if(calibra[j][i] < minsensor)
        minsensor = calibra[j][i];
      Serial.print(calibra[j][i]);
      Serial.print("\t");
    }
  Serial.println(); 
  }
  
  Serial.println();
  Serial.print("Minimo: ");
  Serial.println(minsensor);
  Serial.print("Tope max: ");
  Serial.println(254);
  Serial.println();
  
  establishContact();
  
  digitalWrite(ROW_pin, LOW);
  digitalWrite(COL_pin, LOW);
}


  
void loop(){
  //Loop through and read all 16 values
  //Reports back Value at channel 6 is: 346
  if (Serial.available() > 0){
    inByte = Serial.read();
    
    if(inByte == 'A'){
    
      for(int j = 14; j >= 0; j--){ 
        writeMux(j);
        digitalWrite(ROW_pin,!digitalRead(ROW_pin));
        for(int i = 0; i < 15; i++){
          
          
          
          valor = readMux(i);
          
          
          
          //Saturacion de los sensores
          int limsup = 100;
          if(valor > limsup)
            valor = limsup;
            
          if(valor < calibra[j][i])
            valor = calibra[j][i];  
          
          valor = map(valor,calibra[j][i], limsup,1,254); 
          
  //        if (valor > (pastmatrix[j][i] * .50)  && valor < (pastmatrix[j][i] * 1.5)) 
  //          valor = pastmatrix[j][i];
  //        if (valor < (pastmatrix[j][i] * .50)  && valor > (pastmatrix[j][i] * 1.5))
  //          pastmatrix[j][i] = valor;
          if(valor < 80)
            valor = 1;
          if(valor > 254)
            valor = 254;
          
          Serial.write(valor);
          digitalWrite(COL_pin,!digitalRead(COL_pin));
        } 
      }
    }
    
    if(inByte == 'B'){
      Serial.write(0);
      for(int j = 14; j >= 0; j--){ 
        writeMux(j);
        digitalWrite(ROW_pin,!digitalRead(ROW_pin));
        for(int i = 0; i < 15; i++){
          
          
          
          valor = readMux(i);
          
          
          
          //Saturacion de los sensores
          int limsup = 100;
          if(valor > limsup)
            valor = limsup;
            
          if(valor < calibra[j][i])
            valor = calibra[j][i];  
          
          valor = map(valor,calibra[j][i], limsup,1,254); 
          
  //        if (valor > (pastmatrix[j][i] * .50)  && valor < (pastmatrix[j][i] * 1.5)) 
  //          valor = pastmatrix[j][i];
  //        if (valor < (pastmatrix[j][i] * .50)  && valor > (pastmatrix[j][i] * 1.5))
  //          pastmatrix[j][i] = valor;
          if(valor < 80)
            valor = 1;
          if(valor > 254){
            valor = 254;
            Serial.write(14-j);
            Serial.write(i);
            Serial.write(valor);
          }
          digitalWrite(COL_pin,!digitalRead(COL_pin));
        } 
      }
      Serial.write(255);
    }
  }
}





byte readMux(byte channel){
  byte controlPin[] = {s0, s1, s2, s3};

  //loop through the 4 sig
  for(int i = 0; i < 4; i ++){
    digitalWrite(controlPin[i], muxChannel[channel][i]);
  }

  //read the value at the SIG pin
  byte val = analogRead(SIG_pin)/2;

  //return the value
  return val;
}

void writeMux(byte channel){
  byte controlPin[] = {w0, w1, w2, w3};

  //loop through the 4 sig
  for(byte i = 0; i < 4; i ++){
    digitalWrite(controlPin[i], muxChannel[channel][i]);
  }
}

void establishContact() {
  while (Serial.available() <= 0) {
    Serial.print('A');   // send a capital A
    delay(300);
  }
}
