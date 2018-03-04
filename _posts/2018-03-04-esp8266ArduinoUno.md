---
layout: post
title: Arduino Uno Esp 8266 Bağlantısı ve Güncelleme 
subtitle: ESP8266 Arduino Uno
---
Arduino Uno ve ESP8266 bağlantısının nasıl yapılması gerektiği ile ilgili yerli yabancı bir sürü kaynak var. Bu birazda ESP8266'nın
çok ucuz bir model olmasından kaynaklı olarak stabil bir kurulumun sağlanamış olması olabilir. ESP8266 10-12 tane kadar modeli var sanırım
ve projem için en ucuz olanını(ESP8266-01) seçtim bilseydim biraz daha para verip kurulum ve güncelleme için bu kadar uğraşmazdım.
## Bağlantının Yapılması
Kullanacağımız ekipmanlar Arduino Uno, Breadboard ve Esp8266-01

1.Arduino'nun 3.3V çıkışını(kırmızı kablo ile gösterilen) breadboard üzerindeki bir uca bağlayın.  

2.Arduino'nun Gnd(toprak) ucunu(mavi hat) breadboard üzerinde 3.3V yanına bağlayın.  

3.Arduino'nın Reset ucunu(mavi hat) Gnd'yi bağladığınız hatta Gnd'den sonra bağlayın. 

4.Arduinonun RXD ucunu(sarı hat) ESP8266'nın RX ucuna bağlayın.  

4.Arduinonun TXD(yeşil hat) ucunu ESP8266'nın TX ucuna bağlayın. Normalde arduino ile ESP8266 yı haberleştirmek istediğimizde
birinin TX ucunu ötekinin RX ucuna bağlarız ama burada ESP ile bilgisayarı Arduino aracılığı ile haberleştireceğiz.  

6.ESP8266'nın GND ucunu 2. ve 3. adımda oluşturduğumuz mavi hatta bağlayın.  

7.ESP8266'nın 3.3V ucunu 1. adımda oluşan kırmızı hat üzerine bağlayın.  

8.ESP8266'nın CH_PD ucunu 1.adımda oluşan kırmızı hat üzerine bağlayın.  

Bu 8 adım biraz sıkıcı gelebilir direkt olarak aşağıdaki görsel üzerindende gidebilirsiniz.  

![Arduino Sketch](https://raw.githubusercontent.com/harrunisk/harrunisk.github.io/master/img/Arduino_Esp8266.png)


Şu anlık ESP8266'ya herhangi bir şey yüklemeyeceğiz bu yüzden herhangi bir board seçmemize gerek yok. Arduinun bağlı olduğu
portu seçip serial monitor ekranında 115200 baud hızını ve satır bitişini Both NL & CR seçtinten sonra `AT` Komutu gönderirseniz
size `OK` şeklinde bir cevap gelmesi gerekiyor.  
## Hatalar
Eğer ubuntu kullanıyorsanız `Error opening serial port '/dev/ttyUSB0'` şeklinde bir hata alırsanız gerekli izinleri aşağıdaki
şekilde vererek hatadan kurtabilirsiniz:
~~~
sudo chown username /dev/ttyUSB0
~~~


Eğer hala buna benzer bir hata varsa [burayı](https://www.arduino.cc/en/Guide/Linux) ziyaret edip aşağılarda çözüme ulaşabilirsiniz.

