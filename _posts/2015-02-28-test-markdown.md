---
layout: post
title: Ubuntu 16.04'de OpenCv Kurulumu
subtitle: Adım Adım Kurulum
---

Yazıya başlamadan önce Python 2.7 ve Python 3.5.2 Ubuntu 16.04 içinde hazır olarak kurulu olduğunu belirteyim.  
* Python 2.7.12'e konsoldan `Python` komutunu kullanarak ulaşabilirsiniz.  
* Python 3.5.2'e konsoldan `Python3` komutunu kullanarak ulaşabilirsiniz.

Python 2.7 Ubuntu tarafından kullanılan varsayılan versiyon. Python 3'e geçmek gibi planlar var ama bunun için daha zaman var gibi gözüküyor. Bu yazıda OpenCv' nin kurulumu her iki versiyon içinde yapılacak ama sizin hangi versiyon için yapacağınıza karar vermeniz gerekiyor aksi halde bir sürü hata ile karşılaşmanız kaçınılmaz. Ama tavsiye olarak yeni başlıyorsanız Python 3 ile başlamanız daha yararlı olacaktır.  Python versiyonları arasındaki farkları anladığınızda Python 2.7 kodunu Python 3'e taşımak çok da zor değil.

## Adım-1 : OpenCv Bağımlılıklarının Kurulumu
Tüm adımlarda konsol kullanacağımız için Ubuntu konsolu açıp öncelikle güncelleme ve  yükseltmelerinizi yapın.  
~~~
sudo apt-get update 
sudo apt-get upgrade
~~~
Bazı geliştirme araçlarını kuralım:
~~~
sudo apt-get install build-essential cmake pkg-config
~~~
`pkg-config`  sisteminizde muhtemelen kuruludur ama  `apt-get`  komutu eklendiğinden emin olun. `cmake`  OpenCv'i otomatik olarak derlerken kullanacağız.   
OpenCv görüntü işleme ve bilgisayar görmesi işlemlerinde kullanılan bir kütüphane. OpenCv' nin JPEG, PNG, TIFF, vb bir sürü resim formatını deskteklemesi gerekiyor.  OpenCv yükleme ve çözme işlerini kolaylaştırması için diğer I/O kütüphanelerini kullanır.Gerekli olanları kuracağız:
~~~
sudo apt-get install libjpeg8-dev libtiff5-dev libjasper-dev libpng12-dev
~~~
Şu an resimleri yüklemek için gerekli olan kütüphaneleri yükledik. Video ve kameradan gelen veri akışları için kütüphaneleri yükleyelim:
~~~
sudo apt-get install libavcodec-dev libavformat-dev libswscale-dev libv4l-dev 
sudo apt-get install libxvidcore-dev libx264-dev
~~~
OpenCv kısıtlı sayıda GUI(Graphical User Interface) aracı ile geliyor. Bunlardan bazıları ekranımızda bir resmi görüntülemeye yarayan (`cv2.imshow`),  tuş basışlarını beklemek için (`cv2.waitkey`), fare hareketlerini kaydetmek için (`cv2.setMouseCallback`) ve diğer kaydırma butonu gibi basit GUI elemanları. OpenCv ile çok kapsamlı GUI uygulamaları yapmayı beklemeyin sadece basit uygulamalar ve kodumuzu debug etmek için kullanacağız.  

OpenCv'nin GUI işlerini halleden modül (`highgui`) GTK  kütüphanesine dayanır, bu kütüphaneyi aşağıdaki komut ile kuracağız:
~~~
sudo apt-get install libgtk-3-dev
~~~
Daha sonra OpenCv içinde değişik fonksiyonları (matris işlemleri vb.) optimize eden kütüphaneleri kuracağız:  
~~~
sudo apt-get install libatlas-base-dev gfortran
~~~
İlk adımı her iki Python sürümünün geliştirme başlıklarını ve kütüphanelerini kurarak bitireceğiz:
~~~
sudo apt-get install python2.7-dev python3.5-dev
~~~
Bu başlıkları ve kütüphaneleri kurmadığınız taktirde 4.adımda  `cmake`  Python derleyicileri ve kütüphaneleri için otomatik olarak uygun değerleri atayamayacak.
## Adım-2:OpenCv Kaynak Kodu İndir

Bu çalışmada OpenCv'nin  3.1.0  versiyonunu kullanacağız. İhtiyacınıza göre olan versiyonu [buradan](https://github.com/opencv/opencv) indirip kurabilirsiniz. Zip olarak indirip arşivden aşağıdaki komutlarla çıkaracağız:
~~~
cd ~
wget -O opencv.zip https://github.com/Itseez/opencv/archive/3.1.0.zip
unzip opencv.zip
~~~
Sadece OpenCv indirmek yeterli olmayacak. Biz 2.4 OpenCv versiyonunda  bulunan daha sonra geliştirme ve patent gerekçeleri ile OpenCV 3+ içinde bulunmayan SIFT ve SURF modüllerini de kullanmak istiyoruz. Bu yüzden contrib dosyalarına ihtiyacımız olacak. SIFT ve SURF modülleri hakkında daha fazla bilgiye [buradan](http://www.pyimagesearch.com/2015/07/16/where-did-sift-and-surf-go-in-opencv-3/) ulaşabilirsiniz.Yüklemeye contrib ile devam ediyoruz:
~~~
wget -O opencv_contrib.zip https://github.com/Itseez/opencv_contrib/archive/3.1.0.zip
unzip opencv_contrib.zip
~~~
Burada derleme hataları almamak için dikkat edilmesi gereken nokta  **opencv**  ve  **opencv_contrib**  versiyonlarının aynı olması.  
## Adım-3:Python 2.7 yada Python 3.5 Gereksinimlerinin Kurulması
Derleme için gerekli olan Python geliştirme araçlarını optimize etmeye başlayabiliriz. İlk adım olarak  pip  (Python Paket Yöneticisi) kurarak başlayabiliriz. Bu yazıda  `pip3`  yerine  `pip`  kullanacağız:
~~~
cd ~
wget https://bootstrap.pypa.io/get-pip.py
sudo python get-pip.py
~~~
Kullanım kolaylığı ve pratikliği açısından bu projede [virtualenv](https://virtualenv.pypa.io/en/latest/) ve [virtualenvwrapper](https://virtualenvwrapper.readthedocs.io/en/latest/) kullanacağız.Bu kütüphaneler çalıştığımız her proje için ayrı ve bağımsız Python değişkenleri oluşturmamıza yarar.  Burada sanal ortam kullanmamızdaki asıl amaç her projenin ihtiyaç duyduğu kütüphanelerin farklı olmasından kaynaklı oluşan kirliliği ve dağınıklığı engellemek.  

Neden sanal değişkenlerin(virtualenv ve virtualenvwrapper) çok yararlı bir yöntem olduğuna dair bir yazı okumak isterseniz [buradan](https://realpython.com/blog/python/python-virtual-environments-a-primer/) inceleyebilirsiniz.Bu yazının yarısında neden Python sanal değişkenler kullanıldığına dair bi yazı da [burada](http://www.pyimagesearch.com/2016/05/02/accessing-rpi-gpio-and-gpio-zero-with-opencv-python/).  

Python topluluğunda sanal değişkenlere sahip olmak bir standart siz de yüklerseniz iyi olur:
~~~
sudo pip install virtualenv virtualenvwrapper
sudo rm -rf ~/get-pip.py ~/.cache/pip
~~~
`virtualenv` ve `virtualwrapper`'ı kurduktan sonra   `~/.bashrc`'ı güncellememiz gerekiyor. `~/.bashrc` konsolu her açtığımızda çalışan kabuk script. Biz burada  `WORKON_HOME`  isimli çevre değişkenimizi Python sanal değişkenlerinin olduğu yeri işaret etmek için kullanacağız.Böylece gerekli ayarları  `virtualenvwrapper`'dan alabileceğiz.  `~/.bashrc`'ı güncellemek için basit bir yazı düzenleyici  `nano  vim`  ya da  `emacs` işimizi görecektir. Grafiksel düzenleyiciler de kullanılabilir  `geany`  ya da  `sublimetext` . En basit olarak  `nano`  iş görecektir.  
Düzenleyicilerden kaçınarak  `cat`  komutu ile işimizi halledeceğiz:
~~~
echo -e "\n# virtualenv and virtualenvwrapper" >> ~/.bashrc
echo "export WORKON_HOME=$HOME/.virtualenvs" >> ~/.bashrc
echo "source /usr/local/bin/virtualenvwrapper.sh" >> ~/.bashrc
~~~
 `~/.bashrc`'ı düzenledikten sonra değişiklikleri yüklememiz gerekiyor:
~~~
source ~/.bashrc
~~~
`source` komutunu çağırmamız şu an çalıştığımız konsolu güncelleyecek. Çalıştığımız konsolu kapattıktan sonra yaptığımız yeni güncellemelerle birlikte `.bashrc` çalışacak.  
`virtualenv` ve  `virtualenvwrapper`  kurulduktan sonraki adım. Gerekli olan Python değişkenlerini kurmak. Ama başlamadan önce bu değişkenleri Python 2.7 için mi kuracaksınız yoksa Python 3 için buna karar vermeniz gerekiyor. Yazı da her ikisi için de gerekli olan komutlara yer verilecek.
### Python Değişkenlerinin Oluşturulması
Python 2.7 kullanacaksanız:
~~~
mkvirtualenv cv -p python2
~~~
Python 3 kullacakanız:
~~~
mkvirtualenv cv -p python3
~~~
Hangi Python sürümünüzü kullandığınızdan bağımsız olarak  `cv`(computer vision) isimli Python sanal değişkenimizi oluşturduk. Kendinize daha kolay gelen bir isim koyabilirsiniz. Yada başka sanal değişkenler oluşturabilirsiniz. Bu yazının geri kalanında `cv` kullanılacağı için kolaylık olması açısından `cv` koymak isabet olacaktır.
### Cv Sanal Değişkeni İçinde Olduğumuzu Kontrol Edelim
Sistemi kapatıp açıp yeni bir konsol açtığımızda `cv` sanal değişkenine ulaşmak için `workon` komutuna ihtiyaç duyacağız:
~~~
workon cv
~~~
Komutu işlettikten sonra şöyle bir ekranla karşılaşmanız gerekiyor:
Here's a useless table:

| Number | Next number | Previous number |
| :------ |:--- | :--- |
| Five | Six | Four |
| Ten | Eleven | Nine |
| Seven | Eight | Six |
| Two | Three | One |


How about a yummy crepe?

![Crepe](http://s3-media3.fl.yelpcdn.com/bphoto/cQ1Yoa75m2yUFFbY2xwuqw/348s.jpg)

Here's a code chunk:

~~~
var foo = function(x) {
  return(x + 5);
}
foo(3)
~~~

And here is the same code with syntax highlighting:

```javascript
var foo = function(x) {
  return(x + 5);
}
foo(3)
```

And here is the same code yet again but with line numbers:

{% highlight javascript linenos %}
var foo = function(x) {
  return(x + 5);
}
foo(3)
{% endhighlight %}

## Boxes
You can add notification, warning and error boxes like this:

### Notification

{: .box-note}
**Note:** This is a notification box.

### Warning

{: .box-warning}
**Warning:** This is a warning box.

### Error

{: .box-error}
**Error:** This is an error box.
