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

![error based](/resimler/error_based.png)

[PhpVuln](http://testphp.vulnweb.com/listproducts.php?cat=1) sitesinde kategori listelemek için bir parametre alıyor. Bunu " ?cat=1 " ibaresinden anlayabiliriz. cat kelimesi de category kelimesinin kısaltırması olduğunu tahmin ediyorum.

![tek_tirnak](/resimler/tek_tirnak.png)

Sitedeki url nin sonuna bir tane tek tırnak atarak sitede çalışan sorguyu bozmayı deneyeceğim.

![error based](/resimler/error_based.png)

ve site bize sql hatası verdi. Bu sonuca bakarsak bu sitede sql injection var diyebiliriz.

sonuç olarak arkadaki sorguyu şekillendirmeye çalışırsam şöyle olurdu

```
SELECT Productname, Description, Authors, ... FROM Listing(tamamen sallama tablo adını öğrenmek için başka metodlar kullanmalıyız) WHERE category = '1'
```

Error Based Sql İnjection için bazı wildcardlar:
``` 

'
"
)
]
--

```



### union attack:

union saldırısı var olan bir sql sorgunun içine 2. bir sorgu yazmayı amaçlar. bu sayede site kendi sorgusunu çalıştırırken bizde kendi istediğimiz 2. sorguyu çalıştırabiliriz. 


peki bu nasıl yapılıyor ?


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
