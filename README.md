Bu proje, bir PT100 sıcaklık sensöründen gelen analog sinyali okumak, işlemek ve bu sıcaklık değerini bir HMI ekranında görselleştirmek için geliştirilmiştir. Sistem, bir Siemens SIMATIC S7-1200 F-CPU (Fail-safe CPU) ve bir HMI (Comfort Panel) kullanmaktadır. Projenin ana bileşenleri şunlardır:

PLC Programı: PT100 sensöründen gelen analog sinyali işler ve değerleri skala eder.

HMI Arayüzü: İşlenen sıcaklık değerini operatörlerin kolayca görebileceği ve izleyebileceği bir grafik arayüzde gösterir.

Safety (Güvenlik) Fonksiyonu: Proje, fail-safe özelliklere sahip bir CPU kullandığı için, olası bir tehlikeli durumda sistemi güvenli bir duruma getirecek bir güvenlik programı da içerir.

Çalışma Mantığı
Proje, analog sinyal işleme, veritabanı yönetimi ve kullanıcı arayüzü olmak üzere üç temel mantıksal katmana ayrılabilir.

1. Analog Sinyal İşleme
Bu projenin temel işlevi, bir PT100 direnç termometresinden gelen 4-20 mA'lik analog akım sinyalini almaktır. Bu sinyal, PLC'nin analog giriş modülü tarafından işlenir ve ham bir tamsayı (INTEGER) değerine dönüştürülür.

Ham Sinyal Dönüşümü: PLC, fiziksel 4-20 mA sinyalini 0 ile 27648 arasında bir ham dijital değere dönüştürür. Main [OB1] bloğunda bulunan NORM_X fonksiyonu bu işlemi gerçekleştirir.

%MW100 (AnalogGiris): Sensörden gelen ham tamsayı değerini alır.

Bu ham değer, 0 ile 27648 arasındaki bir aralığa normalize edilir. Fonksiyonun çıktısı %MD102 (AnalogYuzde) adlı bir gerçek (REAL) değişkene atanır.

Sıcaklık Değerini Skalalama: Sinyal normalize edildikten sonra, bu değerin gerçek sıcaklık karşılığına dönüştürülmesi gerekir. SCALE_X fonksiyonu bu işlemi gerçekleştirir.

Giriş (IN): %MD102 (AnalogYuzde) değişkenindeki normalize edilmiş değer.

Minimum (MIN): 0.0

Maksimum (MAX): 100.0

Bu fonksiyon, normalize edilmiş değeri 0.0 ile 100.0 arasında bir yüzdelik değere ölçeklendirir. Çıktı %MD106 (ScaIeYuzdesi) değişkenine atanır. Bu değer, muhtemelen prosesin ilerleyen kısımlarında kullanılmak üzere yüzde olarak bir sıcaklık veya seviye değerini temsil etmektedir.

2. Güvenlik (Safety) Yönetimi
Bu projenin kritik bir bileşeni de güvenlik programıdır. Fail-safe bir CPU kullanılması, makine veya prosesin güvenli bir şekilde kapatılmasını gerektiren durumlarda ek bir emniyet katmanı sağlar.

F-Runtime Grubu: Safety Administration (Güvenlik Yönetimi) ekranında görüldüğü gibi, proje F-runtime group 1 [RTG1] adlı bir güvenlik grubu içerir.

Güvenlik Fonksiyon Blokları: Güvenlik programı, FOB_RTG1 [OB123] ve Main_Safety_RTG1 [FB1] gibi özel güvenlik blokları kullanılarak yazılmıştır. Bu bloklar, uluslararası güvenlik standartlarına (örn. IEC 61508) uygun olarak tasarlanmış programlama yönergelerini takip eder.

Güvenlik Veri Bloğu (DB): Güvenlik programına özgü veriler ve değişkenler Main_Safety_RTG1_08 [DB1] veri bloğunda saklanır. Bu blok, güvenlik programının çalışması için gerekli olan emniyetli giriş/çıkış verilerini veya durum bilgilerini içerebilir.

3. Kullanıcı Arayüzü (HMI)
HMI ekranı, operatörün sistemin durumunu görsel olarak izlemesini ve sensör verilerini anlamasını sağlar.

Sıcaklık Gösterimi: Root screen adlı HMI ekranı, bir PT100 sensörünü ve bir SITRANS TH100 sıcaklık transmitterini sembolik olarak gösterir.

Analog Değerin Görselleştirilmesi: Ekranda, PLC'den gelen ham analog değeri (AnalogGiris - 0-27648 aralığı) gösteren bir dikey çubuk grafik bulunur. Bu, operatörün sıcaklık sinyalinin anlık durumunu görsel olarak takip etmesini sağlar.

Sayısal Değer Gösterimi: Grafiğin altında, anlık ham analog değerin sayısal karşılığı gösterilir (11402). Bu, operatörün hassas bir şekilde değeri görmesine olanak tanır.
