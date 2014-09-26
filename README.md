# Kapgel İş Gönderme ve Takip Sistemi API dökümantasyonu
Kapgel API, kullanıcıların HTTP üzerinden güvenli bir şekide kapgel sistemine 
programatik olarak erişmesini sağlar. Kapgel API hem son kullanıcıların yeni 
gönderi girmeleri için hem de girilmiş olan gönderilerin bir admin tarafından 
yönetimi için kullanılabilir. Bu sistemden beklenebilecek bazı özellikler şu 
anda eksik olsa da nihai amacımız gönderi yapan bütün şirketlerin kapgel'i 
sistemlerine sorunsuz ve rahat bir sekilde entegre edebilmelerini sağlamaktır. 
Eğer bir sorunuz olursa ve ya eklenmesini istediğiniz bir özellik olursa lütfen 
bizimle irtibata geçin, isteklerinizi bir sonraki versiyonumuzda hayata 
geçirmeye çalışacağız.

Kapgel API'ın son versiyonunun temel URL'i https://api.kapgel.com/1 dir.
Aşağıdaki dökümantasyonda URL kısımlarında bu temel URL'in ardına eklenecek
adresler belirtilmiştir. Bütün isteklerde header'da
```
Content-type: application/json
```
olarak içerik türü belirtilmelidir. Kimlik doğrulama dışındaki bütün isteklerde
de yetki anahtarı aşağıdaki gibi belirtilmelidir:
```
Content-type: application/json
kapgel-auth: 03f1372d9-a534-4b79-8acf-c9ee3a88bfe4
```
İstek bodyleri her zaman json formatında gönderilmelidir.

## Kapgel Adres Formatı
Kapgel'deki bütün adreslerde aşağıdaki alanlar bulunur: 
* *number*: Sokak numarası, opsiyonel.
* *apartment*: Apartman ve ya bina adı, opsiyonel.
* *street*: Sokak adı, zorunlu. Açık adres alınıyorsa bu alana açık adres
yazılabilir. Diğer alanların belirtilmemesi halinde diğer alanlar bu alandan
belirlenecektir.
* *neigborhood*: Mahalle adı, opsiyonel.
* *district*: Semt adı, opsiyonel.
* *county*: İlçe adı, opsiyonel. Gönderilerin planlanması için en azından ilçenin
belirli olması gerekmektedir. Eğer adreslerinizi gönderirken ilçeyi
belirlemiyorsanız, ilçe *street* alanından çıkarılmaya çalışılacaktır.
* *city*: İl adı, belirtilmemişse "İstanbul" olarak kabul edilir.
* *country*: Ülke adı, belirtilmemişse "Türkiye" olarak kabul edilir.
Örnek ayrıntılı adres:
```
{
  number: "39",
  apartment: "Route 39",
  street: "Mebusan Ykş.",
  neighborhood: "Cihangir Mah.",
  district: "Cihangir",
  county: "Beyoğlu",
  city: "İstanbul"
}
```
Örnek açık adres:
```
{
  street: "Cihangir Mah, Mebuşan Ykş, No:39, Route 39",
  county: "Beyoğlu",
  city: "İstanbul"
}
```
Her ne kadar açık adres göndermek kabul edilse de, adres hatalarını en aza
indirebilmek için ayrıntılı adres kullanmanızı tavsiye ederiz.
## Kapgel Nesne Tipleri ve Kavramlar
Kapgeli kullanan kullanıcılar, oluşturulan şirketler, şubeler ve gönderiler
kapgel nesneleri olarak adlandırılır. Her kapgel nesnesinin bir id si vardır ve
bu *_id* alaninda saklanir. Aşağıda kapgel nesnelerinin *_id* dışındaki
alanları tanımlanmıştır. 

### *User* 
Kapgel sistemini kullanan kullanıcı. Kullanıcı adı ve şifreye sahiptir.
Aynı zamanda bir *store* la alakalıdır. Bir kullanıcı sadece bir *store* un 
gönderilerini yönetebilir. Aynı *store* birden fazla kullanıcıya sahip
olabilir. Aynı kullanıcı adı ve şifreyi kullanarak web arayüzünden ve ya
API'dan, kendi *store* u için yeni gönderi oluşturabilir ve ya varolan
gönderileri yönetebilir.

### *Contact* 
Adı, soyadı ve telefon numarası olan bir kişiyi temsil eder. 
Gönderiler yerine ulaşamadığında ve ya bir hata olduğunda aranacak kişidir.

### *Store* 
Gönderilerini KapGel kullanarak yapan bir mağazalar ve ya dükkanlar
zincirini temsil eder. Bir ve ya birden fazla *user* tarafından yönetilir.
Bir adı, logosu, URL'i ve *contact* i bulunur.

### *Branch* 
Gönderilerini KapGel kullanarak yapan bir *store* un bir tane şubesini temsil
eder. Adı, adresi, ve *contact* i bulunur.

### *Delivery* 
KapGel kullanılarak yapılan bir gönderiyi temsil eder. Aşağıdaki
alanlara sahiptir:
* *store:* Gönderiyi yapan *store*. Bu alan zorunludur, kullanıcının bağlı
olduğu *store*, *delivery* nin *store* u olarak kabul edilir.
* *branch:* Gönderinin alınacağı şubenin *id* si. Bu alan opsiyoneldir. Eğer bu
alan kullanıcı tarafından belirtilmişse kurye paketi almak için *branch* in
adresine gider.
* *orderNo:* Gönderinin sipariş/paket numarası. Bu numara kullanıcı tarafından
belirtilmelidir. Kurye gönderiyi teslim alırken bu numarayı belirterek
alacaktır.
* *sender:* Gönderinin teslim alınacağı *contact*. Bu alan opsiyoneldir. 
*sender* kullanıcı tarafından belirtilmemişse *branch* in *contact* i *sender*
olarak kullanılır. *branch* belirtilmemişse *sender* belirtilmek zorundadır.
* *recipient:* Gönderinin teslim edileceği *contact*. Bu alan zorunludur.
* *from:* Gönderinin teslim alınacağı adres. Bu alan belirtilmemişse *branch*
in adresi kullanılır. *branch* belirtilmemişse *from* belirtmek zorunludur.
* *to:* Gönderinin teslim edileceği adres. Bu alan belirtilmek zorundadır.


## Kimlik Doğrulama ve yetki anahtarına ulaşma
Bu istek sadece bir defa yapılıp yetki anahtarına ulaşmak için kullanılabilir. 
Yetki anahtarı değişmediği için programınızda her zaman kullanıcı adı ve şifre
göndermek yerine, yetki anahtarınızı kaydedip onu bir daha sorgulamamanızı 
tavsiye ediyoruz.
```
POST /auth
header: {
  Content-Type: application/json
}
body: {
  "username": "pizza", 
  "password":"myheart"
}
```
Cevap:
```
{
  token: "03a91ff16-45f5-4579-9375-a5db5ce20fbb"
}
```
Cevaptaki *token* alanı sizin yetki anahtarınızdır. Bundan sonraki
isteklerinizde bu anahtarı header'da *kapgel-auth:* olarak belirtmelisiniz.
## *Branch* leri listeleme
Kullanıcının *store* una bağlı olan bütün *branch*leri listeler:
```
GET /branch
header: {
  Content-type: application/json
  kapgel-auth: 03a91ff16-45f5-4579-9375-a5db5ce20fbb
}
```
Cevap:
```
[
  {
    _id: "5425d510f233b13d25f9ada8"
    name: "pizzamyheart"
    address: {
      neighborhood: "Cihangir Mah."
      status: "OK"
      apartment: "Route 39"
      number: "39"
      street: "Mebusan Ykş."
      district: "Cihangir"
      county: "Beyoğlu"
    }
    contact: {
      fullName: "Ahmet Yıldırım"
      phone: "+905071460685"
    }
    no: 1019
  }
]
```

### Gönderi Doğrulama ve Fiyat Kontrol
```
POST /delivery/validate
header: {
  Content-Type: application/json
  kapgel-auth: 0b833ef40-245c-11e4-8b24-05df52b6cb67  
}
body: {

}
```

### Gönderi Oluşturma:
```
POST /delivery
header: {
  Content-Type: application/json
  kapgel-auth: 0b833ef40-245c-11e4-8b24-05df52b6cb67
}
body: { 
  "branch": "53b169b05b4856ab90e05d15",
  "desi": 3,
  "sender": {
    "name": "Adem Demir",
    "phone": "05053333333",
    "notes": "Adem Bey 12-1 arası ulaşılamayabilir"
  },
  "recipient": {
    "name": "Mehmet Yilmaz",
    "phone": "05321234567",
    "notes": "Merdivenden cikinca 5. katta, zili calmayin"
  },
  "from": {
    "number": 12,
    "apartment" : "Urban Station Galata",
    "address": "Şahkulu Mh., Serdar-ı Ekrem Caddesi",
    "county": "Beyoğlu",
    "city": "İstanbul"
  },
  "to": {
    "number": 21,
    "apartment": 10,
    "address": "Çay Ocağı Sk, Çubuklu Mh, Beykoz, İstanbul, Türkiye"
  }
}
```
Cevap: 
```
{
  "count":1,
  "result":{
    "id":"53edfd328c28863e01c63d06",
    "status":"created",
    "store":"53b15f7d23603387906b6e8e",
    "branch":"53b169b05b4856ab90e05d15",
    "desi":3,
    "origin": {
      "number" : "12",
      "apartment" : "Urban Station Galata",
      "street" : "Serdar-ı Ekrem Caddesi",
      "neighborhood" : "Şahkulu Mh.",
      "county" : "Beyoğlu",
      "city" : "Istanbul",
      "country" : "Turkey"
    },
    "destination":{
      "number":"21",
      "apartment":"10",
      "street":"Çay Ocağı Sk",
      "neighborhood" : "Çubuklu Mh.",
      "county":"Beykoz",
      "city":"İstanbul",
      "country":"Türkiye"
    },
    "sender": {
      "name": "Adem Demir",
      "phone": "05053333333",
      "notes": "Adem Bey 12-1 arası ulaşılamayabilir"
    },
    "recipient":{
      "name":"Mehmet Yilmaz",
      "phone":"+905321234567",
      "notes":"Merdivenden cikinca 5. katta, zili calmayin"
    },
    "updatedAt":"2014-08-15T12:29:38.761Z",
    "createdAt":"2014-08-15T12:29:38.047Z"
  }
}
```
### Gönderileri listeleme
Bütün gönderilerin listesi için
```
GET /delivery
header: {
  kapgel-auth: 0b833ef40-245c-11e4-8b24-05df52b6cb67,
  branch: 53b169b05b4856ab90e05d15
}
```
Tek bir gonderi icin:
```
GET /delivery/53edfd328c28863e01c63d06
header: {
  Content-Type: application/json
  kapgel-auth: 0b833ef40-245c-11e4-8b24-05df52b6cb67
}
```

### Gönderi onaylama
```
POST /delivery/53edfd328c28863e01c63d06/approve
header: {
  kapgel-auth: 0b833ef40-245c-11e4-8b24-05df52b6cb67
}
```
### iptal etme
```
POST /delivery/53edfd328c28863e01c63d06/cancel
header: {
  kapgel-auth: 0b833ef40-245c-11e4-8b24-05df52b6cb67
}
```
