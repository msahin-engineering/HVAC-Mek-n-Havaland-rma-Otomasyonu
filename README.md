
# HVAC İç Mekân Havalandırma Otomasyonu

## Proje Hakkında

Bu proje, Siemens S7-1200 PLC, TIA Portal V18 ve Factory I/O kullanılarak geliştirilmiş bir HVAC (Isıtma, Havalandırma ve İklimlendirme) otomasyonu uygulamasıdır.

Projenin amacı, ortam sıcaklığını termokupl üzerinden okuyarak 6 adet fanın sıcaklık değerine bağlı olarak kademeli şekilde çalışmasını sağlamaktır.

Sistem, sıcaklık değerine göre fanların çalışma hızlarını otomatik olarak ayarlar. Ayrıca farklı sıcaklık aralıkları sinyal lambalarıyla gösterilir ve çok yüksek sıcaklık durumunda operatör uyarılır.

## Kullanılan Yazılımlar ve Donanımlar

- Siemens S7-1200 PLC (CPU 1214C AC/DC/Rly)
- TIA Portal V18
- Factory I/O
- Siemens S7-PLCSIM
- SM 1231 TC termokupl giriş modülü
- SM 1232 AQ analog çıkış modülleri
- 6 adet fan
- Termokupl sıcaklık sensörü
- 6 adet fan hız göstergesi
- 4 adet sıcaklık sinyal lambası
- Start/Stop çevirmeli anahtar

## Sistemin Çalışma Prensibi

Sistem, çevirmeli anahtar Start konumuna getirildiğinde çalışmaya başlar.

Termokupl üzerinden alınan analog sıcaklık bilgisi PLC tarafından okunur ve sıcaklık değerine dönüştürülür.

PLC, hesaplanan ortam sıcaklığına göre fanları kademeli olarak devreye alır ve analog çıkışlar üzerinden fanların çalışma hızlarını belirler.

### 1. Düşük Sıcaklık (20 °C Altı)

- Fan 1 ve Fan 2 devreye girer.
- Fanlar %50 hızda çalışır.
- Fan 3, Fan 4, Fan 5 ve Fan 6 çalışmaz.
- Düşük sıcaklık lambası yanar.

### 2. Normal Sıcaklık (20–30 °C)

- Fan 1, Fan 2, Fan 3 ve Fan 4 devreye girer.
- Dört fan %50 hızda çalışır.
- Fan 5 ve Fan 6 çalışmaz.
- Normal sıcaklık lambası yanar.

### 3. Yüksek Sıcaklık (30–40 °C)

- Altı fanın tamamı devreye girer.
- Bütün fanlar %50 hızda çalışır.
- Yüksek sıcaklık lambası yanar.

### 4. Çok Yüksek Sıcaklık (40 °C Üzeri)

- Altı fanın tamamı çalışır.
- Bütün fanlar %100 hızda çalışır.
- Çok yüksek sıcaklık lambası yanar.

### 5. Sistemin Durdurulması

- Çevirmeli anahtar Stop konumuna getirilir.
- Bütün fanların analog çıkış değerleri sıfırlanır.
- Fanlar durur.
- Ortam sıcaklığı 40 °C'nin üzerindeyse çok yüksek sıcaklık uyarı lambası yanıp sönerek operatörü uyarır.

## PLC Programının Yapısı

PLC programı Ladder (LAD) programlama dili kullanılarak hazırlanmıştır.

Program toplam 9 Network bölümünden oluşmaktadır.

| Network | Açıklama |
|---|---|
| Network 1 | PLC LAB fonksiyonunun çağrılması |
| Network 2 | Ortam sıcaklığının okunması |
| Network 3 | Ortam sıcaklığının hesaplanması |
| Network 4 | Fan 1 ve Fan 2'nin %50 hızda çalışması |
| Network 5 | Fan 3 ve Fan 4'ün %50 hızda devreye girmesi |
| Network 6 | Fan 5 ve Fan 6'nın %50 hızda devreye girmesi |
| Network 7 | Tüm fanların %100 hızda çalışması |
| Network 8 | Sistemin durdurulması |
| Network 9 | Çok yüksek sıcaklıkta sistem kapalıyken uyarı verilmesi |

## Analog Sinyallerin İşlenmesi

Termokupl giriş modülünden alınan sıcaklık bilgisi PLC içerisinde işlenerek gerçek sıcaklık değerine dönüştürülür.

Programda MOVE, CONV ve DIV komutları kullanılmıştır.

- MOVE: Analog giriş değerini ilgili değişkene aktarır.
- CONV: INT veri tipindeki değeri REAL veri tipine dönüştürür.
- DIV: Analog değeri ölçeklendirmek için kullanılır.

Fan hızları analog çıkış değerleri üzerinden kontrol edilir.

| Fan Hızı | PLC Analog Çıkış Değeri |
|---|---|
| %0 | 0 |
| %50 | 13824 |
| %100 | 27648 |

Factory I/O ortamında fan hızları göstergeler üzerinden izlenmektedir.

## PLC Giriş ve Çıkış Adresleri

### Dijital Girişler

| Adres | Açıklama |
|---|---|
| %I0.0 | Sistem Start |
| %I0.1 | Sistem Stop |

### Analog Giriş

| Adres | Açıklama |
|---|---|
| %IW96 | Termokupl Girişi |

### Analog Çıkışlar

| Adres | Açıklama |
|---|---|
| %QW30 | Fan 1 |
| %QW32 | Fan 2 |
| %QW34 | Fan 3 |
| %QW40 | Fan 4 |
| %QW42 | Fan 5 |
| %QW44 | Fan 6 |

### Dijital Çıkışlar

| Adres | Açıklama |
|---|---|
| %Q0.0 | Düşük Sıcaklık Lambası |
| %Q0.1 | Normal Sıcaklık Lambası |
| %Q0.2 | Yüksek Sıcaklık Lambası |
| %Q0.3 | Çok Yüksek Sıcaklık Lambası |

## Proje Görselleri

### 01 - PLC Donanım Yapılandırması

Siemens S7-1200 PLC, termokupl giriş modülü ve analog çıkış modüllerinin TIA Portal üzerindeki donanım yapılandırması.

![PLC Donanım Yapılandırması](01%20-%20PLC%20Donanım%20Yapılandırması.png.png)

### 02 - PLC Değişken Tablosu

PLC programında kullanılan dijital girişler, analog girişler, analog çıkışlar, sinyal lambaları ve yardımcı değişkenler.

![PLC Değişken Tablosu](02%20-%20PLC%20Değişken%20Tablosu.png.png)

### 03 - Düşük Sıcaklık Simülasyonu

Ortam sıcaklığının 20 °C'nin altında olduğu durumda Fan 1 ve Fan 2'nin %50 hızda çalışması.

![Düşük Sıcaklık Simülasyonu](03%20-%20Düşük%20Sıcaklık%20Simülasyonu.png.png)

### 04 - Düşük Sıcaklıkta Fan Kontrolü

Düşük sıcaklık koşulunda PLC programının çalışma durumu ve fanlara gönderilen analog çıkış değerleri.

![Düşük Sıcaklıkta Fan Kontrolü](04%20-%20Düşük%20Sıcaklıkta%20Fan%20Kontrolü.png.png)

### 05 - Normal Sıcaklık Simülasyonu

Ortam sıcaklığı 20–30 °C arasındayken Fan 1, Fan 2, Fan 3 ve Fan 4'ün %50 hızda çalışması.

![Normal Sıcaklık Simülasyonu](05%20-%20Normal%20Sıcaklık%20Simülasyonu.png.png)

### 06 - Sistem Durdurma Simülasyonu

Stop komutuyla bütün fanların durdurulması ve analog çıkış değerlerinin sıfırlanması.

![Sistem Durdurma Simülasyonu](06%20-%20Sistem%20Durdurma%20Simülasyonu.png.png)

### 07 - Yüksek Sıcaklık Simülasyonu

Ortam sıcaklığı 30–40 °C arasındayken altı fanın tamamının %50 hızda çalışması.

![Yüksek Sıcaklık Simülasyonu](07%20-%20Yüksek%20Sıcaklık%20Simülasyonu.png.png)

### 08 - Çok Yüksek Sıcaklık Simülasyonu

Ortam sıcaklığının 40 °C'yi aşmasıyla altı fanın tamamının %100 hızda çalışması.

![Çok Yüksek Sıcaklık Simülasyonu](08%20-%20Çok%20Yüksek%20Sıcaklık%20Simülasyonu.png.png)

### 09 - Yüksek Sıcaklıkta Sistem Durdurma

Ortam sıcaklığı 40 °C'nin üzerindeyken sistemin durdurulması ve operatörün yanıp sönen uyarı lambasıyla bilgilendirilmesi.

![Yüksek Sıcaklıkta Sistem Durdurma](09%20-%20Yüksek%20Sıcaklıkta%20Sistem%20Durdurma.png.png)

### 10 - Factory I/O Giriş ve Çıkış Bağlantıları

Factory I/O ile Siemens S7-PLCSIM arasındaki dijital ve analog giriş-çıkış adreslerinin eşleştirilmesi.

![Factory I/O Giriş ve Çıkış Bağlantıları](10%20-%20Factory%20IO%20Giriş%20ve%20Çıkış%20Bağlantıları.png.png)

## Proje Dosyası

**TIA Portal proje arşivi:**

`HVAC İç Mekân Havalandırma Otomasyonu.zap18`

Proje dosyası TIA Portal V18 kullanılarak açılabilir.

## Sonuç

Bu projede, ortam sıcaklığına bağlı olarak fanların kademeli şekilde devreye alınması ve çalışma hızlarının otomatik olarak kontrol edilmesi gerçekleştirilmiştir.

Uygulama kapsamında analog sinyallerin okunması ve ölçeklendirilmesi, sıcaklık karşılaştırma işlemleri, PLC üzerinden fan hız kontrolü, dijital çıkışlarla durum gösterimi ve yüksek sıcaklık uyarı mantığı ele alınmıştır.

Projenin çalışma senaryoları Factory I/O ve Siemens S7-PLCSIM ortamında simüle edilmiştir.
