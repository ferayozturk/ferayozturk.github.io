---
title:  "Arduinoların Bluetooth Modülü ile Haberleşmesi"
date:   2017-07-15 10:12:15
---

# Arduinoların Bluetooth Modülü ile Haberleşmesi

Öncelikle kullanacağımız HC-05 bluetooth modülünün nasıl çalıştığına bakalım. Bluetooth modülü başka bir bluetooth cihazla eşleşerek iletişim sağlar. Bu eşleşme master ve slave cihazlar arasında gerçekleşir. Master haberleşme isteği yollar ve 10m alan içerisindeki slave cihazlar cevap verir. HC-05 modülü hem master hemde slave olarak çalışabilir. Fakat hangi durumda çalışacağı AT COMMANDS isimli komutlarla ayarlanır.
**AT COMMANS** listesine [buradan](https://www.itead.cc/wiki/Serial_Port_Bluetooth_Module_(Master/Slave) ulaşabilirsiniz.
AT komutlarını kullanabilmek için gerekli yazılım aşağıdaki gibidir.

```javascript
#include <SoftwareSerial.h>
SoftwareSerial mySerial(10, 11); // RX, TX

void setup() {
  Serial.begin(9600);
  pinMode(9,OUTPUT);
  digitalWrite(9,HIGH);
  Serial.println("Enter AT commands:");
  mySerial.begin(38400);
}

void loop()
{
  if (mySerial.available())
    Serial.write(mySerial.read());
  if (Serial.available())
    mySerial.write(Serial.read());
}
```

Bağlantılar aşağıdaki resimdeki gibi yapıldıktan sonra AT ayarları yapılır.  Ayrıca eğer kullandığınız modülün üzerinde buton varsa 5V pinin bağlarken bu butonu basılı tutmanız gerekir. Aksi taktirde cihaz AT komutlar için hazır duruma gelmeyecektir.

![](https://cdn.instructables.com/FM8/W4A2/HKZAVRT9/FM8W4A2HKZAVRT9.MEDIUM.jpg)

Gerekli ayarlamaları yaptıktan sonra aşağıdaki resimde görüldüğü gibi arduino üzerindeki gerekli bağlantılar yapılır.

![](http://www.elektrobot.net/wp-content/uploads/Untitled-Sketch-2_bb.png)

Ve son olarak kodumuzu iki arduinoyada yükleyelim. Böylece ilk verinin gelmesini arduinolar birbirinden bekleyecektir. Master modülün bağlı olduğu arduinonun serial monitöründen 1 göndererek bağlantıyı başlatmış oluruz.

```javascript
#include <SoftwareSerial.h>

SoftwareSerial mySerial(10, 11); // RX, TX

char gelenKarakter;
int led2 = 4;
int led1 = 5;


void setup() {
  Serial.begin(9600);
  mySerial.begin(9600);
  pinMode(led1, OUTPUT);
  pinMode(led2, OUTPUT);
}

void loop() {
  if(mySerial.available()>0) {
    gelenKarakter=mySerial.read();
    Serial.println(gelenKarakter);
    if(gelenKarakter=='1') {
      digitalWrite(led1,HIGH);
      digitalWrite(led2,LOW);
    }
    else if(gelenKarakter=='2') {
      digitalWrite(led2,HIGH);
      digitalWrite(led1,LOW);
    }
  }
  if(Serial.available() > 0)
  {
    mySerial.write(Serial.read());
  }
}
```
