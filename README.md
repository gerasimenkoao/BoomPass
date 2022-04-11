# BoomPass
Пишу эти строки на русском, может прочитав их у кого-то из той страны проснется понимание происходящего:

Указатель направления взрыва.

https://i.postimg.cc/BQF1s44h/Boom1.jpg

До 24 февраля у нас было все (с. "Не смотри вверх").

Не от хорошей жизни собрал я данное устройство.

У меня не было электиричества, связи и учебника геометрии.

А был ноутбук, Ардуино и взрывы ...

В системе много условностей - что земля плоская, сектор обнаружения - 180 градусов, а скорость звука равна 340м/с.

И еще принималось во внимание, что расстояние до источника на порядки больше базы(340мм) между микрофонами, иначе я не писал-бы эти строки.

https://i.postimg.cc/y6kQzQxf/degree.jpg

Так с помощью линейки и транспортира я вычислсл задержки сигнала для различных углов с шагом 10 градусов.

Собственно, устройство - примитив - цифровые выходы датчиков включаются на входы 2(левый) и 3(правый) Ардуино.

Скетч тоже не отличается изяществом, ибо в таких условиях не до красоты кода:

~~~
// Point to shoot sound with two microphones (340mm between it) and one serwo, ~10 degree accuracy
#include <Arduino.h>
#include <Servo.h>

Servo myservo;  // create servo object to control a servo

int degree;    // degree to shoot point
int delta = 0; // delay between microphones
boolean Left = false ;  // mark of sound from left side sensor (number 2)

// variables to write interrupts time
volatile unsigned long int time1 = 0;
volatile unsigned long int time2 = 0;

void setup() {
  myservo.attach(9);  // attaches the servo on pin 9 to the servo object
// set pins to work with interrupts;   
      myservo.write(180); //test servo to left
      delay(1000);
      myservo.write(0); //test servo to right
      delay(1000);
       myservo.write(90); //test servo to center
      delay(1000);
      attachInterrupt(0, sensor_1, RISING); //left sensor
      attachInterrupt(1, sensor_2, RISING); //right sensor
}

void loop() 
{
  if ( time1 != 0 && time2 != 0 ) {  
    if ( time2<time1 ) {delta=time1-time2; Left=true;} //sound from the left
    if ( time1<time2 ) {delta=time2-time1; Left=false;} //sound from the right
    time1=0;
    time2=0;
       
       if ( delta > 0 ) {degree=90;}
       if ( delta > 52 ) {degree=80;}
       if ( delta > 223 ) {degree=70;}
       if ( delta > 423 ) {degree=60;}
       if ( delta > 576 ) {degree=50;}
       if ( delta > 702 ) {degree=40;}
       if ( delta > 808 ) {degree=30;}
       if ( delta > 897 ) {degree=20;}
       if ( delta > 959 ) {degree=10;}
       if ( delta > 990 ) {degree=0;}

       if (!Left){degree=180-degree;}
        }
  myservo.write(degree);                  // sets the servo position according to the scaled value
  delay(333);

}

void sensor_1()
{ if ( time1 == 0 ) { time1 = micros(); } }

void sensor_2()
{ if ( time2 == 0 ) { time2 = micros();}  }
~~~

Питание схемы - от стабилизированного источника 5В (powerbank).
Отладка производится подстроечниками на звуковых сенсорах.
При постукивании отверткой посередине между датчиками, сервопривод должен устанавливаться в центральное положение.

https://i.postimg.cc/KjPg2hMk/bereza.jpg

"Хотят-ли русские войны?"
С этого стихотворения обычно начинается урок мира.
Очень жаль, что многие его не усвоили...


Arduino pointer to explosion sound

I make this project under enemy fire.
Without phone or Internet.
But under bomb explosion, when russian soldier shoot civilians.

System use SG-90 servo, Arduino pro mini (or just Arduino) and powered from 5v PowerBank.

Digital pin from left sensor connected to #2 of Arduino, right - to #3.

System tuning comes with adjucting of MIC sensitivity when tapping on miidle between it servo must point to centre.

On russian site i be banned just for verse "Is a russians will war?"
