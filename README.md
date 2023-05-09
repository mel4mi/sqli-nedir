# SQLi-nedir



## SQL Mantığı:

sql injectionu anlamak için öncellikle sql yapısını anlamımız lazım.

sql Structured Query Language(Yapılandırılmış sorgu dili) diye açılımı olan bir yazılım dilidir. aslına bakarsanız sql daha öncesine bakıldığında açılımı Structured English Query Language(Yapılandırılmış ingilizce sorgu dili) diye geçerdi. Bunun sebebi sql dediğimiz yazılım dilinin büyük bir çoğunluğu matematik operatörlerinden ziyade ingilizce sorgu yazma üzerine tasarlanmasıyla alakalıydı. sorgu kelimesini açmak gerekirse sql dili bildiğimiz satır ve sutündan oluşan bir tablo tutar.

![basit tablo](/resimler/basit%20tablo.png)

yukarıya örnek bir sql tablosu örneğini bıraktım.

## SQL Sorgu Mantığı:

 önceki başlıkta anlattığım tablo sistemi içinde bulunan verileri daha verimli kullanabilmek için özelliştirmiş sorgular yazarız. bu sorgular bahsettiğim gibi ingilizce cümleleri kullanarak çalışır örnek verirse yukarıdaki tablo esas alınarak içindeki bütün verileri çekmek için yazdığımız sorgu
 
 ```
  SELECT * FROM sqli_anlatim;
 ```
 
 yazdığımız sorguyu inceleyim;
 
 ``` 
 SELECT 
 ```
 >"SELECT" ibaresini tablodan hangi sutünleri çekmek istediğimizi belirtmek için kullanırız. "FROM" ibaresi ise çekeceğimiz verilerin hangi tablodan geleceğini belirtir. ilk yazdığım sorgudaki " * " ibaresi bütün sutünları çekmek için kullanılır
 
 ``` 
 SELECT kisi_adi, kisi_soyadi FROM sqli_anlatim;
 ```
 
 hızlı bir şekilde yukarıdaki sorguyu açıklayalım. 
 
 >"select kisi_adi, kisi_soyadi" ibaresi tablodan sadece kisi_adi ve kisi_soyadi sutünlerini çekmemizi sağlar.
 "from sqli_anlatim" ibaresi verileri hangi tablodan çekeceğimizi belirtiler.
 
 
``` 
SELECT username,password FROM accounts WHERE username = 'User1' AND password = '123456' LIMIT 1;
```
 >Bu sorgu "accounts" adlı tablodan kullanıcı adının "user1" olduğu ve şifresinin "123456" olduğu satırı getirir. En sondaki "LIMIT 1" ibaresi ise tek satırda tek sorgu çalıştırmak için sınır koyar(sqli önlemek için). Bu tarz sorgular genellikle hesap giriş işlemleri için kullanılır. Eğer kullanıcı adı ve şifresi tabloda bulunursa "true" döndürür ve hesabımıza giriş yapabiliriz.
 
 ## SQL Injection Nedir?
 
SQL Injection, kendi sorgularımızı SQL sorgularının içine karıştırarak manipüle etmeyi amaçlar. Bu sayede sitelerde, şirketlerde veya kurumlarda bulunan bütün veriler çalınabilir. Örneklendirmek gerekirse, [Reon Sağlık Hizmetleri](https://www.kvkk.gov.tr/Icerik/7523/Kamuoyu-Duyurusu-Veri-Ihlali-Bildirimi-Reon-Saglik-Hizmetleri-Ins-Tur-San-ve-Tic-A-S-Ozel-Aktif-Hastanesi-) şirketinin veritabanı sızdırılmış ve hastaların adı, soyadı, TC kimlik numarası gibi hassas bilgiler ifşa edilmiştir.

SQL Injection'un ayrıca bir kullanım alanı daha vardır ki bu da "login bypass" dediğimiz giriş yapmamız gereken yerlerde kullanıcı adı ve şifre bilmeden admin veya istediğimiz kullanıcı adına giriş yapmamıza olanak sağlar.


not: sql injection ile backdoor açma işlemi bu yazıda anlatılmayacaktır..


### union attack:

union saldırısı var olan bir sql sorgunun içine 2. bir sorgu yazmayı amaçlar. bu sayede site kendi sorgusunu çalıştırırken bizde kendi istediğimiz 2. sorguyu çalıştırabiliriz. 


peki bu nasıl yapılıyor ?


wildcard dedğimiz özel semboller vardır örneklendirmek gerekirse " ' ", " " ", " ) ", " ] ", " - " gibi wildcardlar sayesinde var olan sorgunun yapısını bozabilir ve kendi isteklerimiz üzere tekrardan şekillendirebiliriz. Örneklendirmek gerekirse

``` 
SELECT posters, authors FROM product WHERE id = ' ' LIMIT 1 ;
```
>1. Aşama: sitemiz tablolar ve sanatçıların bilgilerini getirir. istediğimiz sanatçının bilgilerini bulmak için id değerlerini kullanır.
>2. Aşama: bizim bu sorguda kontrol edebildiğimiz alan 2 tane tek tırnak arasındaki kısımdır.
>3. Aşama: id kısmına tek tırnak atarak sorgu yapısı bozulur
```  
SELECT posters, authors FROM product WHERE id = ' ' ' LIMIT 1 ;
```
>4. Aşama: attığımız tek tırnak sayesinde sorgunun dışına çıktık ve yazdığımız kelimeler artık komut olarak algılanacak(eğer yanlış bir şeyler yazarsanız sorgu error verir (error-based sql injection))
>5. Aşama: attığımız tek tırnak sonrasına "UNION SLECT NULL,NULL --" ibaresi eklenir
``` 
SELECT posters, authors FROM product WHERE id = ' 'UNION SLECT NULL,NULL --  ' LIMIT 1 ;
```
>
