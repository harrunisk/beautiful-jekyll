---
layout: post
title: Elasticsearch'de Wifi Paket Analizi 
subtitle: Tshark Logstash Filebeat Kibana Elasticsearch
---


Tshark'ın Wireshark konsol arayüzü ya da Wireshark'ın tsharkın grafik arayüzü olan hali olduğu söylenebilir. 
Tshark ile yakalanan wireless paketlerinin bazı özelliklerinin analizini Elasticsearch ve araçları yardımı ile yapacağım.
Kullanacağım araçlar Tshark, ElasticSearch ,Kibana, Logstash ve Filebeat olacak. Elasticsearch, Kibana, Logstash ve Filebeat 
kurulumu için [bu link](https://www.elastic.co/products). Ya da docker kullanarak da halledebilirsiniz. Tshark filtreler kullanarak paketleri yakalayacak csv türünde 
dosyaya yazacak. Filebeat dosyayı dinleyecek Logstash'e aktaracak. Logstash burada yeniden filtreleme yapacak ve
Elasticsearch'e aktaracak. Kibana kullanarak analizlerimi yapacağım.
Mimarimiz aşağıdaki şekilde olacak:  
![Architecture](https://raw.githubusercontent.com/harrunisk/harrunisk.github.io/master/img/ArchitectureBlog.png)  
Wifi paketlerinin analizde kullanacağım filtreler:  

| Filtre        | Açıklama           |   
| ------------- |:-------------:| -----:|
| _ws.col.Time      | Zaman Bilgisi |
| wlan.fc.type      | Çerçeve Türü |   
| wlan.fc.type_subtype      | Çerçeve Alt Türü      |   
| radiotap.dbm_antsignal | Sinyal Gücü(RSSI)      |     
| frame.len      | Çerçeve Boyutu      |   
| radiotap.datarate | Veri Akış Hızı      |   


wlan.fc.type filtresinden gelecek değerler 0, 1, ya da 2 olacak. Bu değerlerin karşılığı:        

| Değer        | Sonuç           |   
| ------------- |:-------------:| -----:|   
| wlan.fc.type==0      | Yönetim Çerçevesi |   
| wlan.fc.type==1      | Kontrol Çerçevesi      |   
| wlan.fc.type==2 | Veri Çerçevesi      |    

wlan.fc.subtype filtresinden gelecek değerler 0 ile 47 arasında değişecek bunlardan bazıları aşağıdaki tabloda kapsamlı olarak [buradan](https://dalewifisec.wordpress.com/2014/04/29/wireshark-802-11-display-filters-2/) ulaşabilirsiniz.  

| Değer        | Sonuç           |   
| ------------- |:-------------:| -----:|   
| wlan.fc.type_subtype==8      | Beacon |   
| wlan.fc.type_subtype==27      | Request To Send      |   
| wlan.fc.type_subtype==28 | Clear To Send      |


## 1.Paket Yakalama ve Dosyaya Yazma  
Kullanacağım filtre:
~~~
tshark -a duration:600 -i phy0.mon -t ad -t ad -lT fields -E separator=, -E quote=d   -e _ws.col.Time  -e wlan.fc.type -e wlan.fc.type_subtype -e radiotap.dbm_antsignal -e frame.len -e radiotap.datarate	 > tshark.csv
~~~
`-a duration:600`  10 dakika boyunca paket yakalayacağımı belirtiyorum.  
`-i phy0.mon` paketleri yakalayacak arayüzümü seçiyorum sizde farklılık gösterebilir.  
`-t ad` burada zamanın nasıl yazdırılacağını belirtiyorum. YYYY-MM-DD formatında.  
`> tshark.csv` yakalanan paketlerin yazılacağı dosya otomatik oluşturuyor.  
Kalan alanlar kullanılan filtrelerin ne şekilde ayrılacağı ile ilgili.  

## 2.Yakalan Paketlerin Filebeat İle Dinlenmesi
Filebeat 1.adımda oluşturduğumuz tshark.csv dosyasını dinleyecek ve buradaki değişiklikleri Logstash'e aktaracak. Bunun için filebeat.yml dosyasını aşağıdaki şekilde değiştiriyoruz.  
~~~
filebeat.modules:
- module: system
  syslog:
    enabled: false
  auth:
    enabled: true
    var.paths: ["/home/tshark.csv"]
name: test
output.logstash:
  hosts: ["localhost:5044"]
~~~
İndirme linki [filebeat.yml](https://raw.githubusercontent.com/harrunisk/WifiPacketAnalysis/master/filebeat.yml)  
`var.paths: ["/home/tshark.csv"]` dosyamızın yolu kendinize göre değiştirin.  
Windows sistemlerde filebeat'in kurulu olduğu yerden filebeat.yml dosyasına erişebilirsiniz sanırım.  
filebeat.yml dosyasına linux sistemler için aşağıdaki yol ile ulaşabilirsiniz:
~~~
cd /etc/filebeat
sudo subl filebeat.yml
~~~
## 3.Logstash'in Filebeat'i Dinlemesi Elastic Searche Yönlendirmesi ve CSV Dosyasına Filtrelerin Uygulanması

2.adımda Filebeat paketleri localhost:5044'e yani Logstash'in çalıştığı sokete yönlendirdi
Bu adımda gelen paketleri toplayıp ElasticSearch'e yönlendireceğiz. Logstash.yml dosyasını aşağıdaki gibi değiştireceğiz:
~~~
input {
  beats {
    port => 5044
  }
}

filter {
   csv {
     source => "message"
     columns => [ "col.time","frame.type","frame.subtype","rssi","frame.size","data.rate" ]
   }


 date {
                match => [ "col.time", "YYYY-MM-DD HH:mm:ss.SSSSSSSSS" ]
                target => "@timestamp"
                }
                mutate {
                add_field => {"[hour]" => "%{+HH}"}
                add_field => {"[minute]" => "%{+mm}"}
                add_field => {"[second]" => "%{+ss}"}
                
                }



   mutate {
     convert => [ "rssi", "integer" ]
     convert => [ "frame.size", "integer" ]
     convert => [ "data.rate", "integer" ]   
     convert => [ "second", "integer" ]  
     convert => [ "minute", "integer" ]  
     convert => [ "hour", "integer" ]  



   }
   
   

 

   if[frame.type]=="0"{
   mutate {
     replace => [ "frame.type", "Management" ]
   }}
   if[frame.type]=="1"{
   mutate {
     replace => [ "frame.type", "Control" ]
   }}
   if[frame.type]=="2"{
   mutate {
     replace => [ "frame.type", "Data" ]
   }}





   if[frame.subtype]=="0"{
   mutate {
     replace => [ "frame.subtype", "Association Request" ]
   } }
   if[frame.subtype]=="1"{
   mutate {
     replace => [ "frame.subtype", "Association Response" ]
   } }
   if[frame.subtype]=="2"{
   mutate {
     replace => [ "frame.subtype", "Reassociation Request" ]
   } }
   if[frame.subtype]=="3"{
   mutate {
     replace => [ "frame.subtype", "Reassociation Response" ]
   } }
   if[frame.subtype]=="4"{
   mutate {
     replace => [ "frame.subtype", "Probe Request" ]
   } }
   if[frame.subtype]=="5"{
   mutate {
     replace => [ "frame.subtype", "Probe Response" ]
   } }
      if[frame.subtype]=="8"{
   mutate {
     replace => [ "frame.subtype", "Beacon" ]
   } }
   if[frame.subtype]=="9"{
   mutate {
     replace => [ "frame.subtype", "ATIM" ]
   } }
   if[frame.subtype]=="10"{
   mutate {
     replace => [ "frame.subtype", "Disassociation" ]
   } }
   if[frame.subtype]=="11"{
   mutate {
     replace => [ "frame.subtype", "Authentication" ]
   } }
   if[frame.subtype]=="12"{
   mutate {
     replace => [ "frame.subtype", "Deauthentication" ]
   } }
   if[frame.subtype]=="13"{
   mutate {
     replace => [ "frame.subtype", "Action Frames" ]
   } }
   if[frame.subtype]=="24"{
   mutate {
     replace => [ "frame.subtype", "Block ACK Request" ]
   } }
   if[frame.subtype]=="25"{
   mutate {
     replace => [ "frame.subtype", "Block ACK" ]
   } }
   if[frame.subtype]=="26"{
   mutate {
     replace => [ "frame.subtype", "Power Save Poll" ]
   } }
   if[frame.subtype]=="27"{
   mutate {
     replace => [ "frame.subtype", "Request to Send" ]
   } }
   if[frame.subtype]=="28"{
   mutate {
     replace => [ "frame.subtype", "Clear to Send" ]
   } }
   if[frame.subtype]=="29"{
   mutate {
     replace => [ "frame.subtype", "ACK" ]
   } }
   if[frame.subtype]=="30"{
   mutate {
     replace => [ "frame.subtype", "CFP End" ]
   } }
   if[frame.subtype]=="31"{
   mutate {
     replace => [ "frame.subtype", "CFP End ACK" ]
   } }
   if[frame.subtype]=="33"{
   mutate {
     replace => [ "frame.subtype", "Data + CF ACK" ]
   } }
   if[frame.subtype]=="34"{
   mutate {
     replace => [ "frame.subtype", "Data + CF Poll" ]
   } }
   if[frame.subtype]=="35"{
   mutate {
     replace => [ "frame.subtype", "Data + CF ACK + CF Poll" ]
   } }
   if[frame.subtype]=="36"{
   mutate {
     replace => [ "frame.subtype", "Null Data" ]
   } }
   if[frame.subtype]=="37"{
   mutate {
     replace => [ "frame.subtype", "Null Data + CF ACK" ]
   } }
   if[frame.subtype]=="38"{
   mutate {
     replace => [ "frame.subtype", "Null Data + CF Poll" ]
   } }
   if[frame.subtype]=="39"{
   mutate {
     replace => [ "frame.subtype", "Null Data + CF ACK + CF Poll" ]
   } }
   if[frame.subtype]=="40"{
   mutate {
     replace => [ "frame.subtype", "QoS Data" ]
   } }
   if[frame.subtype]=="41"{
   mutate {
     replace => [ "frame.subtype", "QoS Data + CF ACK" ]
   } }
   if[frame.subtype]=="42"{
   mutate {
     replace => [ "frame.subtype", "QoS Data + CF Poll" ]
   } }
   if[frame.subtype]=="43"{
   mutate {
     replace => [ "frame.subtype", "QoS Data + CF ACK + CF Poll" ]
   } }
   if[frame.subtype]=="44"{
   mutate {
     replace => [ "frame.subtype", "Null QoS Data" ]
   } }
   if[frame.subtype]=="46"{
   mutate {
     replace => [ "frame.subtype", "Null QoS Data + CF Poll" ]
   } }
   if[frame.subtype]=="47"{
   mutate {
     replace => [ "frame.subtype", "Null QoS Data + CF ACK + CF Poll" ]
   } }                                                                                                                                     


   
  }
output {
  elasticsearch {
    hosts => ["http://localhost:9200"]
    index => "%{[@metadata][beat]}-%{+YYYY.MM.dd}" 
    document_type => "%{[@metadata][type]}" 
    user => elastic
    password => changeme
  }
}
~~~






