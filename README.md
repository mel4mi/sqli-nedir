# SQLi-nedir



### SQL Mantığı:

sql injectionu anlamak için öncellikle sql yapısını anlamımız lazım.

sql Structured Query Language(Yapılandırılmış sorgu dili) diye açılımı olan bir yazılım dilidir. aslına bakarsanız sql daha öncesine bakıldığında açılımı Structured English Query Language(Yapılandırılmış ingilizce sorgu dili) diye geçerdi. Bunun sebebi aslında sql dediğimiz yazılım dilinin büyük bir çoğunluğu matematik operatörlerinden ziyade ingilizce sorgu yazma üzerine tasarlanmasıyla alakalıydı. sorgu kelimesini açmak gerekirse sql dili bildiğimiz satır ve sutündan oluşan bir tablo tutar.

![basit tablo](/resimler/basit%20tablo.png)

yukarıya örnek bir sql tablosu örneğini bıraktım.

### SQL Sorgu Mantığı:

 önceki başlıkta anlattığım tablo sistemi içinde bulunan verileri daha verimli kullanabilmek için özelliştirmiş sorgular yazarız. bu sorgular bahsettiğim gibi ingilizce cümleleri kullanarak çalışır örnek verirse yukarıdaki tablo esas alınarak içindeki bütün verileri çekmek için yazdığımız sorgu
 
 ```
  SELECT * FROM sqli_anlatim;
 ```
 
 yazdığımız sorguyu inceleyim;
 
 ``` 
 SELECT 
 ```
 "SELECT" ibaresini tablodan hangi sutünleri çekmek istediğimizi belirtmek için kullanırız. ilk yazdığım sorgudaki " * " ibaresi bütün sutünları çekmek için kullanılır
 
  
 ``` 
 FROM 
 ```
 
 "FROM" ibaresi çekeceğimiz verilerin hangi tablodan geleceğini belirtir. Bizim örneğimizdeki "sqli_anlatim" tablo ismi benim bu yazıyı yazmak için kullandığım tablonun adı.
 
 
 ``` 
 SELECT kisi_adi, kisi_soyadi FROM sqli_anlatim;
 ```
 
 hızlı bir şekilde yukarıdaki sorguyu açıklayalım. 
 
 "select kisi_adi, kisi_soyadi" ibaresi tablodan sadece kisi_adi ve kisi_soyadi sutünlerini çekmemizi sağlar.
 "from sqli_anlatim" ibaresi verileri hangi tablodan çekeceğimizi belirtiler.
 
 
 ### SQL Injection Nedir?
 
SQL Injection, kendi sorgularımızı SQL sorgularının içine karıştırarak manipüle etmeyi amaçlar. Bu sayede sitelerde, şirketlerde veya kurumlarda bulunan bütün verileri çalabiliriz. örneklendirmek gerekirse [Reon Sağlık Hizmetleri](https://www.kvkk.gov.tr/Icerik/7523/Kamuoyu-Duyurusu-Veri-Ihlali-Bildirimi-Reon-Saglik-Hizmetleri-Ins-Tur-San-ve-Tic-A-S-Ozel-Aktif-Hastanesi-) şirketinin veritabanı sızdırılmış ve hastaların ad, soyad, tc kimlik numarası gibi hassas bilgiler ifşa edildi. 

sql injectionun ayrıca bir kullanım alanı daha vardır ki bu da "login bypass" dediğimiz giriş yapmamız gereken yerlerde kullanıcı adı ve şifre bilmeden admin veya istediğimiz kullanıcı adına giriş yapmamıza olanak sağlar.

not: sql injection ile backdoor açma işlemi bu yazıda anlatılmayacaktır..

