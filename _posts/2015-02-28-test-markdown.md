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
![Workon Cv](https://raw.githubusercontent.com/harrunisk/harrunisk.github.io/master/img/OpenCvImg1Workoncv.png)

Eğer resimin ikinci satırında gördüğünüz gibi **(cv)** yazısı görmüyorsanız sanal değişkenimizin içinde değilsiniz demek. Sanal değişkenden çıkmak içinde `deactivate` komutunu kullanabilirsiniz.
### Python Sanal Değişkeni İçine NumPy Kurulumu
OpenCV derlemeden önceki son adımımız matematiksel işlemlerde kullanılan [NumPy](http://www.numpy.org/) paketini kurmak olacak. NumPy'i kurmadan önce sanal değişken içinde olduğumuzdan emin olmamız lazım , eğer `cv` içinde değilseniz sistemin manual olarak kullandığı Python çevre değişkenleri içine kurulacak:
~~~
pip install numpy
~~~
## Adım-4:Ubuntu 16.04 de OpenCv Ayarlanması ve Derlenmesi
Şu ana kadar OpenCv derlemesi için gerekli olan tüm gereklilikleri tamamladık. Bundan sonraki adımlar OpenCV'nin ayarlarının yapılması ve derlenmesi. Başlamadan önce `cv` sanal değişkeni içinde olduğunuzu tekrar kontrol etmeniz gerekiyor. Konsolda komut satırında yüm yazılardan önce (cv) şeklinde bir yazı görmeniz gerekiyor, en üstteki resimde olduğu gibi. Eğer `cv` sanal değişkeni içinde değilseniz:
~~~
workon cv
~~~
`cv` sanal değişkeni içinde olduğumuzdan emin olduktan sonra CMake kullanarak ayarlamalarımızı yapmaya başlayabiliriz:
~~~
cd ~/opencv-3.1.0/
mkdir build
cd build
cmake -D CMAKE_BUILD_TYPE=RELEASE 
 -D CMAKE_INSTALL_PREFIX=/usr/local   
 -D INSTALL_PYTHON_EXAMPLES=ON 
 -D INSTALL_C_EXAMPLES=OFF 
 -D OPENCV_EXTRA_MODULES_PATH=~/opencv_contrib-3.1.0/modules 
 -D PYTHON_EXECUTABLE=~/.virtualenvs/cv/bin/python 
 -D BUILD_EXAMPLES=ON .. 
~~~
`~/opencv-3.1.0.zip` dosyasını indirip çıkartmıştık. Çıkarttığımız o dosyanın içine girmemiz gerekiyor. Burada kodları satır satır kopyalayıp derlemeniz hatalardan kaçınmak açısından daha faydalı olacaktır. Eğer `make` ya da `cmake` aşamalarında `stdlib.h: No such file or directory` şeklinde bir hata alıyorsanız `-D ENABLE_PRECOMPILED_HEADERS=OFF` satırını CMake konfigürasyon ayarları içine eklemeniz gerekli yani üstte ki kod bölmesinin en alt satırına eklemeniz gerekiyor. Garanti olması açısından `build` klasörünü silip(`rm -r build` kullanabilirsiniz) en baştan başlayarak hatadan kurtulabilirsiniz.  build  isimli alt klasörü oluşturduktan sonra  gerçek derleme öncesinde `cmake` ile gerekli ayarlamaları yaptık. 

Gerçek derlemeye geçmeden önce  CMake çıktısını kontrol etmekte fayda var. Çıktıda önemli olan nokta `Python 2` ve `Python 3` başlıklı bölümler.  

Eğer OpenCV derlemesini Python 2.7 için yapıyorsanız `Python 2` başlıklı bölümde `Interpreter`, `Libraries` , `numpy` ve `package path`  bölümlerine geçerli yolların atandığından emin olun. Çıktının aşağıdakine benzer bir şey olması gerekiyor:
![Python2 Check](https://raw.githubusercontent.com/harrunisk/harrunisk.github.io/master/img/OpenCvImg2Python2.png)

Python 2.7 için `cv2` isimli bir sanal ortam oluşturdum.  
Python 3 için  `cv`  isimli bir sanal ortam oluşturdum.  
Bu çıktı bize şunları verecektir :  
`Interpreter`  bize   `cv2`   içindeki  Python 2.7 binary dosyalarını belirtir.    
`Librariers` bize Python 2.7 kütüphanesini (1.bölümün sön adımında kurmuştuk) belirtir.  
`numpy` bize `cv2` içindeki NumPy kurulumunu belirtir.  
`packages path`  bize   `lib/python2.7/site-packages`'ı belirtiyor.`CMAKE_INSTALL_PREFIX` ile birleştirilip OpenCV' i derlediğimizde `cv2.so` bağlantılarını  `/usr/local/lib/python2.7/site-packages/` içinde bulacağız.  

Eğer OpenCV derlemesini Python 3 için yapıyorsanız  çıktının aşağıdakine benzer bir şey olması gerekiyor:
![Python3 Check](https://raw.githubusercontent.com/harrunisk/harrunisk.github.io/master/img/OpenCvImg3Python3.png)  

Eğer  yukarıdaki değerler sizde benzer bir şekilde değilseniz büyük bir ihtimal CMake'den önce `cv` sanal değişkeni içine girmediniz demek. O zaman yapmanız gereken `workon cv` ile sanal değişkene erişip CMake komutlarını yeniden işletmeniz.
CMake komutları hatasız olarak işlettiyseniz şimdi OpenCV derlemesine geçebiliriz:
~~~
make -j8
~~~
Burada `-j` OpenCV derlenirken kaç tane işlemci kullanacağınızı belirtiyor. Birden fazla işlemci kullanmak derlemeyi hızlandırmasına rağmen bazı durumlarda derleme işleminiz patlayabilir ve hatalarla karşılaşabilirsiniz. Bu durumlarda `make clean` ile yapıyı temizleyip derleme işlemini tek işlemci ile yapmak daha faydalı olacaktır.
~~~
make clean
make
~~~
Başarılı bir derlemeden sonra benim ekranda aşağıda ki gibi bir görüntü oluştu:
![OpenCv Compile](https://raw.githubusercontent.com/harrunisk/harrunisk.github.io/master/img/OpenCvImg4OpenCvCompile.png)
Son aşama OpenCV'i Ubuntu 16.04'de kurmak olacak:
~~~
sudo make install
sudo ldconfig
~~~
## Adım-5:OpenCv Kurulumun Bitirilmesi
Son bir iki adım sonunda OpenCV sistemde tam olarak kullanılır duruma gelecek.
### Python 2.7 İçin
`sudo make install` komutunu işlettikten sonra, Python 2.7'nin OpenCV 3 için olan bağlamları `/usr/local/lib/python-2.7/site-packages/'` içinde konumlanmış olması gerekiyor. `ls` komutu ile kontrol edebilirsiniz:
~~~
ls -l /usr/local/lib/python2.7/site-packages/
total 1972
-rw-r--r-- 1 root staff 2016728 mag 30 03:27 cv2.so
~~~
Bazı durumlarda OpenCv   `/usr/local/lib/python-2.7/site-packages/`  yerine    `/usr/local/lib/python-2.7/dist-packages/`    içine kurulmuş olabilir. Eğer  `/usr/local/lib/python-2.7/dist-packages/cv2.so` bağlamları  `site-packages`   dizininde yoksa     `dist-packages`  dizinini kontrol etmekte fayda var. Bu bölümde son aşama OpenCV    `cv2.so`  bağlamlarını cv sanal değişkenine sembolik bağlantı yapmak:
~~~
cd ~/.virtualenvs/cv/lib/python2.7/site-packages/
ln -s /usr/local/lib/python2.7/site-packages/cv2.so cv2.so
~~~
### Python 3.5 İçin

  `sudo make install`  komutunu işlettikten sonra, Python 3.5'in OpenCV 3 için olan bağlamları   `/usr/local/lib/python-3.5/site-packages/`  içinde konumlanmış olması gerekiyor.  `ls`    komutu ile kontrol edebilirsiniz:  
~~~
ls -l /usr/local/lib/python3.5/site-packages/
total 1972
-rw-r--r-- 1 root staff 2016728 mag 30 03:27 cv2.so
~~~
Bazı durumlarda  `cv2.so` 'nun ismi farklı olabiliyor. `cv2.cpython-35m-x86_64-linux-gnu.so`  ismine benzer bi ismi olabilir.Dosyanun adını değiştirerek olası hatalardan kurtulabiliriz: 
~~~
cd /usr/local/lib/python3.5/site-packages/
sudo mv cv2.cpython-35m-x86_64-linux-gnu.so cv2.so
~~~
İsmi değiştirdikten sonra son aşama    `cv2.so`  bağlamlarını cv sanal değişkenine sembolik bağlantı yapmak: 
~~~
cd ~/.virtualenvs/cv/lib/python3.5/site-packages/
ln -s /usr/local/lib/python3.5/site-packages/cv2.so cv2.so 
~~~
## Adım-6:OpenCv Kurulumun Test Edilmesi
Şu an Ubuntu 16.04 üzerinde OpenCV 3 kurulumunu bitirmiş bulunuyoruz. Test etmek için:  
1.Yeni bir terminal aç.  
2.`workon cv`  komutunu çalıştır  cv sanal değişkenine ulaş.  
3.Python üzerinde OpenCV bağlamları çağır. 
Aşağıda ki gibi bir şey yapabilirsiniz:
~~~
$ cd ~
$ workon cv
$ python
Python 3.5.2 (default, Nov 17 2016, 17:05:23) 
[GCC 5.4.0 20160609] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import cv2
>>> cv2.__version__
'3.1.0'
>>> 
~~~
Yukarıda gördüğünüz gibi,OpenCV bağlamlarına Python 3.5 üzerinden erişebiliyorum.Python'un hangi versiyonunu kullandığınızdan bağımsız olarak bu yazıda ki adımları izleyerek OpenCV ve Python'u Ubuntu 16.04 üzerinde kurabilirsiniz.OpenCV'i kurduktan sonra   `opencv-3.1.0`  ve   `opencv-contrib-3.1.0`  klasörlerini ve   `.zip`  dosyalarını silebiliriz:
~~~
cd ~
rm -rf opencv-3.1.0 opencv_contrib-3.1.0 opencv.zip opencv_contrib.zip
~~~
Bu dosyaların silmeden önce OpenCV'nin sisteminizde düzgün bir şekilde kurulduğundan emin olun aksi halde tüm işlemleri tekrardan başlatmanız gerekebilir.


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
