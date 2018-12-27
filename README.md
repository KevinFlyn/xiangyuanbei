const int IR_PIN[] = {30,31,32,   33,34,35,36,  37,38,39,40,  41,42,43,44};
const uint8_t left_motor_pin = 6,  right_motor_pin = 7;
const int IN_A1 = 2,  IN_A2 = 3 , IN_B1 = 4 ,IN_B2 = 5; 
int sensor[15] = {0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0 ,0, 0};                          //15个传感器数值的数组 
int Scanleft();
int Scanright();
#define ledG 22
#define ledR 23
#define ledW 24
#define ledB 25
#define Buzzer 45

float Scanforward();
int b;
int sum();
void pinint();
uint8_t displays(void);
float Error,Error0,Error1,Error2;
int a=0;
int c,d=0,e=0,f=0,g;
void motor(int left,int right);
int _millis = 0;
int _millis0 = 0;
int timechange=0;
void _stop();
void go();
void buz();

void setup() {
  pinMode(ledG,OUTPUT);
  pinMode(ledR,OUTPUT);
  pinMode(ledW,OUTPUT);
  pinMode(ledB,OUTPUT);
  pinint();
  Serial.begin(115200);
}
void loop(){
  displays();
  Serial.print("   ");
  //圆环程序
 if(e==0){
   digitalWrite(ledR,HIGH);
   digitalWrite(ledW,LOW);
   digitalWrite(ledB,HIGH);
Scanforward();
  sum();
 
    if(a==4){
      Scanleft();           //进入圆环
      Scanright();          //离开圆环
    if(Error1<-2)
    {e=1;
    b++; 
    buz();
    _millis0 = millis();
    Error=-5;}                  //////////////////////可改
    if(Error2>3)
     { e=2;
     b++;
     buz();
      _millis0 = millis();
      Error=0;}}
  //十字程序
      if(a>5){
      b++;
      buz();
      e=3;
      Error=0;}
      Serial.print(" 无圆环 ");}

      _millis = millis();
      timechange=_millis-_millis0;
      
      if(e==1){
        digitalWrite(ledR,LOW);
        digitalWrite(ledW,HIGH);
        digitalWrite(ledB,HIGH);
        Scanleft();
        sum();
        if(a==1)
        Error=Error1;
        if(a==2)
        Error=Error1+0.5;
        else
        Error=Error1;
        Serial.print(" 进入圆环 ");
        if(timechange>2500)
        e=0;}

        if(e==2){
          digitalWrite(ledR,HIGH);
          digitalWrite(ledW,HIGH);
          digitalWrite(ledB,LOW);
        Scanright();
        sum();
        if(a==1)
        Error=Error2;
        if(a==2)
        Error=Error2+0.5;
        else
        Error=Error2;
        Serial.print(" 离开圆环 ");
        if(timechange>2500)
        e=0;}

        if(e==3){
          go();
          delay(300);
           e=0; }
 //停车程序
        if(b==11)
        {_stop();
        Serial.print("停车");
        delay(1000000);}

  Serial.print("    ");
  Serial.print("b=");
  Serial.print(b);
  Serial.print("    ");
  Serial.print("距离=");  

  
  c=Error*2;
  switch (c){              //读取初值  
     case 0:     motor(180,180); Serial.println("0"); break; 
     case -1:    motor(30,215); Serial.println("-1"); break; 
     case 1:     motor(215,30); Serial.println("1"); break; 
     case -2:    motor(15,230); Serial.println("-2"); break; 
     case 2:     motor(230,15); Serial.println("2"); break; 
     
     case -3:    motor(-30,230); Serial.println("-3"); break; 
     case 3:     motor(230,-30); Serial.println("3"); break; 
     case -4:    motor(-50,235); Serial.println("-4"); break; 
     case 4:     motor(235,-50); Serial.println("4"); break; 
     
     case -5:    motor(-65,240); Serial.println("-5"); break; 
     case 5:     motor(240,-65); Serial.println("5"); break; 
     case -6:    motor(-200,245); Serial.println("-6"); break; 
     case 6:     motor(245,-200); Serial.println("6"); break; 
     case -7:    motor(-240,245); Serial.println("-7"); break; 
     case 7:     motor(245,-240); Serial.println("7"); break; 
     case -8:    motor(-250,250); Serial.println("-8"); break; 
     case 8:     motor(250,-250); Serial.println("8"); break; 
  
     case -9:    motor(-240,254); Serial.println("-9"); break; 
     case 9:     motor(254,-240); Serial.println("9"); break; 
     case -10:   motor(-240,255); Serial.println("-10"); break; 
     case 10:    motor(255,-240); Serial.println("10"); break; 
     case -11:   motor(-250,255); Serial.println("-11"); break; 
     case 11:    motor(255,-255); Serial.println("11"); break; 
   
     
     case -12:   motor(-255,255); Serial.println("-12"); break; 
     case 12:    motor(255,-255); Serial.println("12"); break;    
     case -13:   motor(-255,255); Serial.println("-13"); break; 
     case 13:    motor(255,-255); Serial.println("13"); break; 
     case -14:   motor(-255,255); Serial.println("-14"); break; 
     case 14:    motor(255,-255 ); Serial.println("14"); break; 
     default: motor(+128,-128);Serial.println("E");break; 
}
}
/***********************************************/
int sum()
{
  a=0;
  for(int i=0;i<15;i++)
  if (digitalRead(IR_PIN[i])==1)
       a++;
       Serial.print("   ");
       Serial.print(a);
       Serial.print("    ");
      return a;}
int Scanleft()
 {    for (int i=0;i<15;i++) {
      if (digitalRead(IR_PIN[i])==1){
        Error1=i-7;
        break; }  }
    return Error1;}
int Scanright(){
   for (int i=14;i>=0;i--){
      if (digitalRead(IR_PIN[i])==1)
      {  Error2=i-7;
        break;}  }
    return Error2;}
    /*************************************************/
float Scanforward()
{for(int i=0;i<15;i++)
      {
        sensor[i] = digitalRead(IR_PIN[i]);
      }
 //000000000-100000000-010000000-001000000-000100000-000010000-000001000-000000100-000110000-001100000
           if ((sensor[0] == 0) && (sensor[1] == 0) && (sensor[2] == 0) && (sensor[3] == 0)&& (sensor[4] == 0) && (sensor[5] == 0)&& (sensor[6] == 0) && (sensor[7] == 0)&& (sensor[8] == 0)&& (sensor[9] == 0)&& (sensor[10] == 0) && (sensor[11] == 0)&& (sensor[12] == 0) && (sensor[13] == 0)&& (sensor[14] == 0)) {
      Error = 0;//          0000000 0 0000000  停
    } else if ((sensor[0] == 1) && (sensor[1] == 0) && (sensor[2] == 0) && (sensor[3] == 0)&& (sensor[4] == 0) && (sensor[5] == 0)&& (sensor[6] == 0) && (sensor[7] == 0)&& (sensor[8] == 0)&& (sensor[9] == 0)&& (sensor[10] == 0) && (sensor[11] == 0)&& (sensor[12] == 0) && (sensor[13] == 0)&& (sensor[14] == 0)) {
      Error = -7;//         1000000 0 0000000  1
    } else if ((sensor[0] == 0) && (sensor[1] == 1) && (sensor[2] == 0) && (sensor[3] == 0)&& (sensor[4] == 0) && (sensor[5] == 0)&& (sensor[6] == 0) && (sensor[7] == 0)&& (sensor[8] == 0)&& (sensor[9] == 0)&& (sensor[10] == 0) && (sensor[11] == 0)&& (sensor[12] == 0) && (sensor[13] == 0)&& (sensor[14] == 0)) {
      Error = -6;//         0100000 0 0000000  2
    } else if ((sensor[0] == 0) && (sensor[1] == 0) && (sensor[2] == 1) && (sensor[3] == 0)&& (sensor[4] == 0) && (sensor[5] == 0)&& (sensor[6] == 0) && (sensor[7] == 0)&& (sensor[8] == 0)&& (sensor[9] == 0)&& (sensor[10] == 0) && (sensor[11] == 0)&& (sensor[12] == 0) && (sensor[13] == 0)&& (sensor[14] == 0)) {
      Error = -5;//         0010000 0 0000000  3
    } else if ((sensor[0] == 0) && (sensor[1] == 0) && (sensor[2] == 0) && (sensor[3] == 1)&& (sensor[4] == 0) && (sensor[5] == 0)&& (sensor[6] == 0) && (sensor[7] == 0)&& (sensor[8] == 0)&& (sensor[9] == 0)&& (sensor[10] == 0) && (sensor[11] == 0)&& (sensor[12] == 0) && (sensor[13] == 0)&& (sensor[14] == 0)) {
      Error = -4;//          0001000 0 0000000  
    }  else if ((sensor[0] == 0) && (sensor[1] == 0) && (sensor[2] == 0) && (sensor[3] == 0)&& (sensor[4] == 1) && (sensor[5] == 0)&& (sensor[6] == 0) && (sensor[7] == 0)&& (sensor[8] == 0)&& (sensor[9] == 0)&& (sensor[10] == 0) && (sensor[11] == 0)&& (sensor[12] == 0) && (sensor[13] == 0)&& (sensor[14] == 0)) {
      Error =  -3;//         0000100 0 0000000  
    } else if ((sensor[0] == 0) && (sensor[1] == 0) && (sensor[2] == 0) && (sensor[3] == 0)&& (sensor[4] == 0) && (sensor[5] == 1)&& (sensor[6] == 0) && (sensor[7] == 0)&& (sensor[8] == 0)&& (sensor[9] == 0)&& (sensor[10] == 0) && (sensor[11] == 0)&& (sensor[12] == 0) && (sensor[13] == 0)&& (sensor[14] == 0)) {
      Error =  -2;//         0000010 0 0000000  
    } else if ((sensor[0] == 0) && (sensor[1] == 0) && (sensor[2] == 0) && (sensor[3] == 0)&& (sensor[4] == 0) && (sensor[5] == 0)&& (sensor[6] == 1) && (sensor[7] == 0)&& (sensor[8] == 0)&& (sensor[9] == 0)&& (sensor[10] == 0) && (sensor[11] == 0)&& (sensor[12] == 0) && (sensor[13] == 0)&& (sensor[14] == 0)) {
      Error =  -1;//         0000001 0 0100000  
    } else if ((sensor[0] == 0) && (sensor[1] == 0) && (sensor[2] == 0) && (sensor[3] == 0)&& (sensor[4] == 0) && (sensor[5] == 0)&& (sensor[6] == 0) && (sensor[7] == 1)&& (sensor[8] == 0)&& (sensor[9] == 0)&& (sensor[10] == 0) && (sensor[11] == 0)&& (sensor[12] == 0) && (sensor[13] == 0)&& (sensor[14] == 0)) {
      Error =  0;//          0000000 1 0000000  
    } else if ((sensor[0] == 0) && (sensor[1] == 0) && (sensor[2] == 0) && (sensor[3] == 0)&& (sensor[4] == 0) && (sensor[5] == 0)&& (sensor[6] == 0) && (sensor[7] == 0)&& (sensor[8] == 1)&& (sensor[9] == 0)&& (sensor[10] == 0) && (sensor[11] == 0)&& (sensor[12] == 0) && (sensor[13] == 0)&& (sensor[14] == 0)) {
      Error =  1;//          0000000 0 1000000  
    }else if ((sensor[0] == 0) && (sensor[1] == 0) && (sensor[2] == 0) && (sensor[3] == 0)&& (sensor[4] == 0) && (sensor[5] == 0)&& (sensor[6] == 0) && (sensor[7] == 0)&& (sensor[8] == 0)&& (sensor[9] == 1)&& (sensor[10] == 0) && (sensor[11] == 0)&& (sensor[12] == 0) && (sensor[13] == 0)&& (sensor[14] == 0)) {
      Error =  2;//          0000000 0 0100000  
    }else if ((sensor[0] == 0) && (sensor[1] == 0) && (sensor[2] == 0) && (sensor[3] == 0)&& (sensor[4] == 0) && (sensor[5] == 0)&& (sensor[6] == 0) && (sensor[7] == 0)&& (sensor[8] == 0)&& (sensor[9] == 0)&& (sensor[10] == 1) && (sensor[11] == 0)&& (sensor[12] == 0) && (sensor[13] == 0)&& (sensor[14] == 0)) {
      Error =  3;//         0000000 0 0010000 
    }else if ((sensor[0] == 0) && (sensor[1] == 0) && (sensor[2] == 0) && (sensor[3] == 0)&& (sensor[4] == 0) && (sensor[5] == 0)&& (sensor[6] == 0) && (sensor[7] == 0)&& (sensor[8] == 0)&& (sensor[9] == 0)&& (sensor[10] == 0) && (sensor[11] == 1)&& (sensor[12] == 0) && (sensor[13] == 0)&& (sensor[14] == 0)) {
      Error =  4;//         0000000 0 0001000  
    }else if ((sensor[0] == 0) && (sensor[1] == 0) && (sensor[2] == 0) && (sensor[3] == 0)&& (sensor[4] == 0) && (sensor[5] == 0)&& (sensor[6] == 0) && (sensor[7] == 0)&& (sensor[8] == 0)&& (sensor[9] == 0)&& (sensor[10] == 0) && (sensor[11] == 0)&& (sensor[12] == 1) && (sensor[13] == 0)&& (sensor[14] == 0)) {
      Error =  5;//         0000000 0 0000100  
    }else if ((sensor[0] == 0) && (sensor[1] == 0) && (sensor[2] == 0) && (sensor[3] == 0)&& (sensor[4] == 0) && (sensor[5] == 0)&& (sensor[6] == 0) && (sensor[7] == 0)&& (sensor[8] == 0)&& (sensor[9] == 0)&& (sensor[10] == 0) && (sensor[11] == 0)&& (sensor[12] == 0) && (sensor[13] == 1)&& (sensor[14] == 0)) {
      Error =  6;//         0000000 0 0000010 
    }else if ((sensor[0] == 0) && (sensor[1] == 0) && (sensor[2] == 0) && (sensor[3] == 0)&& (sensor[4] == 0) && (sensor[5] == 0)&& (sensor[6] == 0) && (sensor[7] == 0)&& (sensor[8] == 0)&& (sensor[9] == 0)&& (sensor[10] == 0) && (sensor[11] == 0)&& (sensor[12] == 0) && (sensor[13] == 0)&& (sensor[14] == 1)) {
      Error =  7;//         0000000 0 0000001  
    }
      
    else if ((sensor[0] == 1 ) && (sensor[1] == 1) && (sensor[2] == 0) && (sensor[3] == 0)&& (sensor[4] == 0) && (sensor[5] == 0)&& (sensor[6] == 0) && (sensor[7] == 0)&& (sensor[8] == 0) && (sensor[9] == 0)&& (sensor[10] == 0) && (sensor[11] == 0)&& (sensor[12] == 0) && (sensor[13] == 0)&& (sensor[14] == 0)) {
      Error = -6.5;//          1100000 0 0000000  
    } 
    else if ((sensor[0] == 0 ) && (sensor[1] == 1) && (sensor[2] == 1) && (sensor[3] == 0)&& (sensor[4] == 0) && (sensor[5] == 0)&& (sensor[6] == 0) && (sensor[7] == 0)&& (sensor[8] == 0) && (sensor[9] == 0)&& (sensor[10] == 0) && (sensor[11] == 0)&& (sensor[12] == 0) && (sensor[13] == 0)&& (sensor[14] == 0)) {
      Error = -5.5;//          0110000 0 0000000  
    } 
    else if ((sensor[0] == 0 ) && (sensor[1] == 0) && (sensor[2] == 1) && (sensor[3] == 1)&& (sensor[4] == 0) && (sensor[5] == 0)&& (sensor[6] == 0) && (sensor[7] == 0)&& (sensor[8] == 0) && (sensor[9] == 0)&& (sensor[10] == 0) && (sensor[11] == 0)&& (sensor[12] == 0) && (sensor[13] == 0)&& (sensor[14] == 0)) {
      Error = -4.5;//          0011000 0 0000000  
    } 
    else if ((sensor[0] == 0 ) && (sensor[1] == 0) && (sensor[2] == 0) && (sensor[3] == 1)&& (sensor[4] == 1) && (sensor[5] == 0)&& (sensor[6] == 0) && (sensor[7] == 0)&& (sensor[8] == 0) && (sensor[9] == 0)&& (sensor[10] == 0) && (sensor[11] == 0)&& (sensor[12] == 0) && (sensor[13] == 0)&& (sensor[14] == 0)) {
      Error = -3.5;//          0001100 0 0000000  
    } 
    else if ((sensor[0] == 0 ) && (sensor[1] == 0) && (sensor[2] == 0) && (sensor[3] == 0)&& (sensor[4] == 1) && (sensor[5] == 1)&& (sensor[6] == 0) && (sensor[7] == 0)&& (sensor[8] == 0) && (sensor[9] == 0)&& (sensor[10] == 0) && (sensor[11] == 0)&& (sensor[12] == 0) && (sensor[13] == 0)&& (sensor[14] == 0)) {
      Error = -2.5;//          0000110 0 0000000  
    } 
    else if ((sensor[0] == 0 ) && (sensor[1] == 0) && (sensor[2] == 0) && (sensor[3] == 0)&& (sensor[4] == 0) && (sensor[5] == 1)&& (sensor[6] == 1) && (sensor[7] == 0)&& (sensor[8] == 0) && (sensor[9] == 0)&& (sensor[10] == 0) && (sensor[11] == 0)&& (sensor[12] == 0) && (sensor[13] == 0)&& (sensor[14] == 0)) {
      Error = -1.5;//          0000011 0 0000000  
    } 
    else if ((sensor[0] == 0 ) && (sensor[1] == 0) && (sensor[2] == 0) && (sensor[3] == 0)&& (sensor[4] == 0) && (sensor[5] == 0)&& (sensor[6] == 1) && (sensor[7] == 1)&& (sensor[8] == 0) && (sensor[9] == 0)&& (sensor[10] == 0) && (sensor[11] == 0)&& (sensor[12] == 0) && (sensor[13] == 0)&& (sensor[14] == 0)) {
      Error = -0.5;//          0000001 1 0000000  
    } 
    else if ((sensor[0] == 0 ) && (sensor[1] == 0) && (sensor[2] == 0) && (sensor[3] == 0)&& (sensor[4] == 0) && (sensor[5] == 0)&& (sensor[6] == 0) && (sensor[7] == 1)&& (sensor[8] == 1) && (sensor[9] == 0)&& (sensor[10] == 0) && (sensor[11] == 0)&& (sensor[12] == 0) && (sensor[13] == 0)&& (sensor[14] == 0)) {
      Error = 0.5;//          0000000 1 1000000 
    } 
    else if ((sensor[0] == 0 ) && (sensor[1] == 0) && (sensor[2] == 0) && (sensor[3] == 0)&& (sensor[4] == 0) && (sensor[5] == 0)&& (sensor[6] == 0) && (sensor[7] == 0)&& (sensor[8] == 1) && (sensor[9] == 1)&& (sensor[10] == 0) && (sensor[11] == 0)&& (sensor[12] == 0) && (sensor[13] == 0)&& (sensor[14] == 0)) {
      Error = 1.5;//          0000000 0 1100000 
    } 
    else if ((sensor[0] == 0 ) && (sensor[1] == 0) && (sensor[2] == 0) && (sensor[3] == 0)&& (sensor[4] == 0) && (sensor[5] == 0)&& (sensor[6] == 0) && (sensor[7] == 0)&& (sensor[8] == 0) && (sensor[9] == 1)&& (sensor[10] == 1) && (sensor[11] == 0)&& (sensor[12] == 0) && (sensor[13] == 0)&& (sensor[14] == 0)) {
      Error = 2.5;//          0000000 0 0110000 
    } 
    else if ((sensor[0] == 0 ) && (sensor[1] == 0) && (sensor[2] == 0) && (sensor[3] == 0)&& (sensor[4] == 0) && (sensor[5] == 0)&& (sensor[6] == 0) && (sensor[7] == 0)&& (sensor[8] == 0) && (sensor[9] == 0)&& (sensor[10] == 1) && (sensor[11] == 1)&& (sensor[12] == 0) && (sensor[13] == 0)&& (sensor[14] == 0)) {
      Error = 3.5;//          0000000 0 0011000 
    } 
    else if ((sensor[0] == 0 ) && (sensor[1] == 0) && (sensor[2] == 0) && (sensor[3] == 0)&& (sensor[4] == 0) && (sensor[5] == 0)&& (sensor[6] == 0) && (sensor[7] == 0)&& (sensor[8] == 0) && (sensor[9] == 0)&& (sensor[10] == 0) && (sensor[11] == 1)&& (sensor[12] == 1) && (sensor[13] == 0)&& (sensor[14] == 0)) {
      Error = 4.5;//          0000000 0 0001100 
    } 
    else if ((sensor[0] == 0 ) && (sensor[1] == 0) && (sensor[2] == 0) && (sensor[3] == 0)&& (sensor[4] == 0) && (sensor[5] == 0)&& (sensor[6] == 0) && (sensor[7] == 0)&& (sensor[8] == 0) && (sensor[9] == 0)&& (sensor[10] == 0) && (sensor[11] == 0)&& (sensor[12] == 1) && (sensor[13] == 1)&& (sensor[14] == 0)) {
      Error = 5.5;//          0000000 0 0000110
    } 
    else if ((sensor[0] == 0 ) && (sensor[1] == 0) && (sensor[2] == 0) && (sensor[3] == 0)&& (sensor[4] == 0) && (sensor[5] == 0)&& (sensor[6] == 0) && (sensor[7] == 0)&& (sensor[8] == 0) && (sensor[9] == 0)&& (sensor[10] == 0) && (sensor[11] == 0)&& (sensor[12] == 0) && (sensor[13] == 1)&& (sensor[14] == 1)) {
      Error = 6.5;//          0000000 0 0000011 
    } 
     else if ((sensor[0] == 1) && (sensor[1] == 1) && (sensor[2] == 1) && (sensor[3] == 0)&& (sensor[4] == 0) && (sensor[5] == 0)&& (sensor[6] == 0) && (sensor[7] == 0)&& (sensor[8] == 0)&& (sensor[9] == 0)&& (sensor[10] == 0) && (sensor[11] == 0)&& (sensor[12] == 0) && (sensor[13] == 0)&& (sensor[14] == 0)) {
      Error = -6;//         1110000 0 0000000  1
    } else if ((sensor[0] == 0) && (sensor[1] == 1) && (sensor[2] == 1) && (sensor[3] == 1)&& (sensor[4] == 0) && (sensor[5] == 0)&& (sensor[6] == 0) && (sensor[7] == 0)&& (sensor[8] == 0)&& (sensor[9] == 0)&& (sensor[10] == 0) && (sensor[11] == 0)&& (sensor[12] == 0) && (sensor[13] == 0)&& (sensor[14] == 0)) {
      Error = -5;//         0111000 0 0000000  2
    } else if ((sensor[0] == 0) && (sensor[1] == 0) && (sensor[2] == 1) && (sensor[3] == 1)&& (sensor[4] == 1) && (sensor[5] == 0)&& (sensor[6] == 0) && (sensor[7] == 0)&& (sensor[8] == 0)&& (sensor[9] == 0)&& (sensor[10] == 0) && (sensor[11] == 0)&& (sensor[12] == 0) && (sensor[13] == 0)&& (sensor[14] == 0)) {
      Error = -4;//         0011100 0 0000000  3
    } else if ((sensor[0] == 0) && (sensor[1] == 0) && (sensor[2] == 0) && (sensor[3] == 1)&& (sensor[4] == 1) && (sensor[5] == 1)&& (sensor[6] == 0) && (sensor[7] == 0)&& (sensor[8] == 0)&& (sensor[9] == 0)&& (sensor[10] == 0) && (sensor[11] == 0)&& (sensor[12] == 0) && (sensor[13] == 0)&& (sensor[14] == 0)) {
      Error = -3;//          0001110 0 0000000  左转1
    }  else if ((sensor[0] == 0) && (sensor[1] == 0) && (sensor[2] == 0) && (sensor[3] == 0)&& (sensor[4] == 1) && (sensor[5] == 1)&& (sensor[6] == 1) && (sensor[7] == 0)&& (sensor[8] == 0)&& (sensor[9] == 0)&& (sensor[10] == 0) && (sensor[11] == 0)&& (sensor[12] == 0) && (sensor[13] == 0)&& (sensor[14] == 0)) {
      Error =  -2;//         0000111 0 0000000  直走
    } else if ((sensor[0] == 0) && (sensor[1] == 0) && (sensor[2] == 0) && (sensor[3] == 0)&& (sensor[4] == 0) && (sensor[5] == 1)&& (sensor[6] == 1) && (sensor[7] == 1)&& (sensor[8] == 0)&& (sensor[9] == 0)&& (sensor[10] == 0) && (sensor[11] == 0)&& (sensor[12] == 0) && (sensor[13] == 0)&& (sensor[14] == 0)) {
      Error =  -1;//         0000011 1 0000000  右转1
    } else if ((sensor[0] == 0) && (sensor[1] == 0) && (sensor[2] == 0) && (sensor[3] == 0)&& (sensor[4] == 0) && (sensor[5] == 0)&& (sensor[6] == 1) && (sensor[7] == 1)&& (sensor[8] == 1)&& (sensor[9] == 0)&& (sensor[10] == 0) && (sensor[11] == 0)&& (sensor[12] == 0) && (sensor[13] == 0)&& (sensor[14] == 0)) {
      Error =  0;//         0000001 1 1000000  右转2
    } else if ((sensor[0] == 0) && (sensor[1] == 0) && (sensor[2] == 0) && (sensor[3] == 0)&& (sensor[4] == 0) && (sensor[5] == 0)&& (sensor[6] == 0) && (sensor[7] == 1)&& (sensor[8] == 1)&& (sensor[9] == 1)&& (sensor[10] == 0) && (sensor[11] == 0)&& (sensor[12] == 0) && (sensor[13] == 0)&& (sensor[14] == 0)) {
      Error =  1;//          0000000 1 1100000  右转3
    } else if ((sensor[0] == 0) && (sensor[1] == 0) && (sensor[2] == 0) && (sensor[3] == 0)&& (sensor[4] == 0) && (sensor[5] == 0)&& (sensor[6] == 0) && (sensor[7] == 0)&& (sensor[8] == 1)&& (sensor[9] == 1)&& (sensor[10] == 1) && (sensor[11] == 0)&& (sensor[12] == 0) && (sensor[13] == 0)&& (sensor[14] == 0)) {
      Error =  2;//          0000000 0 1110000  右转4
    }else if ((sensor[0] == 0) && (sensor[1] == 0) && (sensor[2] == 0) && (sensor[3] == 0)&& (sensor[4] == 0) && (sensor[5] == 0)&& (sensor[6] == 0) && (sensor[7] == 0)&& (sensor[8] == 0)&& (sensor[9] == 1)&& (sensor[10] == 1) && (sensor[11] == 1)&& (sensor[12] == 0) && (sensor[13] == 0)&& (sensor[14] == 0)) {
      Error =  3;//          0000000 0 0111000  右转4
    }else if ((sensor[0] == 0) && (sensor[1] == 0) && (sensor[2] == 0) && (sensor[3] == 0)&& (sensor[4] == 0) && (sensor[5] == 0)&& (sensor[6] == 0) && (sensor[7] == 0)&& (sensor[8] == 0)&& (sensor[9] == 0)&& (sensor[10] == 1) && (sensor[11] == 1)&& (sensor[12] == 1) && (sensor[13] == 0)&& (sensor[14] == 0)) {
      Error =  4;//         0000000 0 0011100 右转4
    }else if ((sensor[0] == 0) && (sensor[1] == 0) && (sensor[2] == 0) && (sensor[3] == 0)&& (sensor[4] == 0) && (sensor[5] == 0)&& (sensor[6] == 0) && (sensor[7] == 0)&& (sensor[8] == 0)&& (sensor[9] == 0)&& (sensor[10] == 0) && (sensor[11] == 1)&& (sensor[12] == 1) && (sensor[13] == 1)&& (sensor[14] == 0)) {
      Error =  5;//         0000000 0 0001110  右转4
    }else if ((sensor[0] == 0) && (sensor[1] == 0) && (sensor[2] == 0) && (sensor[3] == 0)&& (sensor[4] == 0) && (sensor[5] == 0)&& (sensor[6] == 0) && (sensor[7] == 0)&& (sensor[8] == 0)&& (sensor[9] == 0)&& (sensor[10] == 0) && (sensor[11] == 0)&& (sensor[12] == 1) && (sensor[13] == 1)&& (sensor[14] == 1)) {
      Error =  6;//         0000000 0 0000111  右转4
    }
    else
    {Error=0;
    } 
    Serial.print(Error);
    Serial.print("   ");
    return Error;        
  }/*************************************************/
void _stop()
{ digitalWrite(IN_A1,LOW);
  digitalWrite(IN_A2,LOW);
  digitalWrite(IN_B1,LOW);
  digitalWrite(IN_B2,LOW);
  analogWrite(left_motor_pin,120);
  analogWrite(right_motor_pin,120);
}
  void go()
  {
  digitalWrite(IN_A1,HIGH);
  digitalWrite(IN_A2,LOW);
  digitalWrite(IN_A1,HIGH);
  digitalWrite(IN_A2,LOW);
  analogWrite(left_motor_pin,150);
  analogWrite(right_motor_pin,150);
  }
void pinint()
{    
  pinMode(ledG,OUTPUT);
  pinMode(ledR,OUTPUT);
  pinMode(ledW,OUTPUT);
  pinMode(ledB,OUTPUT);
  pinMode(Buzzer,OUTPUT);
  int i;
    for (i = 0; i < 15; i++) {
    pinMode(IR_PIN[i], INPUT);} 
  pinMode (IN_A1, OUTPUT); //设置引脚为输出引脚
  pinMode (IN_A2, OUTPUT); //设置引脚为输出引脚
  pinMode (IN_B1, OUTPUT); //设置引脚为输出引脚
  pinMode (IN_B2, OUTPUT);} //设置引脚为输出引脚7
void motor(int left,int right)
{
   if(left > 0){
  digitalWrite(IN_A1,HIGH);
  digitalWrite(IN_A2,LOW);}
  if(left < 0){
  digitalWrite(IN_A1,LOW);
  digitalWrite(IN_A2,HIGH);
  left= -left;} 
  if(left==0){
   digitalWrite(IN_A1,LOW);
  digitalWrite(IN_A2,LOW); }
    if(right > 0){
  digitalWrite(IN_B1,HIGH);
  digitalWrite(IN_B2,LOW);}
 if(right < 0){
  digitalWrite(IN_B1,LOW);
  digitalWrite(IN_B2,HIGH);
  right = -right; } 
  if(right == 0){
  digitalWrite(IN_B1,LOW);
  digitalWrite(IN_B2,LOW);}
  analogWrite(left_motor_pin,left);
  analogWrite(right_motor_pin,right);}
uint8_t displays(void)
{   for(int i=0;i<15;i++) {
    sensor[i] = digitalRead(IR_PIN[i]); }
    for(int i=0;i<7;i++)
    Serial.print(sensor[i]);
    Serial.print(" ");
    Serial.print(sensor[7]);
    Serial.print(" ");
    for(int i=8;i<15;i++)
    Serial.print(sensor[i]);}
    void buz()
{
    for(int i = 0 ; i < 500 ; i++)    //循环100次
  {
    digitalWrite(Buzzer,HIGH);    //设置输出高电平
    delayMicroseconds(50); //延时PotBuffer值 us
    digitalWrite(Buzzer,LOW);     //设置输出低电平
    delayMicroseconds(200);        //延时100us
  }
}
