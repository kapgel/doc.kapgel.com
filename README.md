# KapGel İş Gönderme ve Takip Sistemi API dökümantasyonu
KapGel API, kullanıcıların HTTP üzerinden güvenli bir şekide KapGel sistemine 
programatik olarak erişmesini sağlar. KapGel API hem son kullanıcıların yeni 
gönderi girmeleri için hem de girilmiş olan gönderilerin bir admin tarafından 
yönetimi için kullanılabilir. Bu sistemden beklenebilecek bazı özellikler şu 
anda eksik olsa da nihai amacımız gönderi yapan bütün şirketlerin KapGel'i 
sistemlerine sorunsuz ve rahat bir sekilde entegre edebilmelerini sağlamaktır. 
Eğer bir sorunuz olursa ve ya eklenmesini istediğiniz bir özellik olursa lütfen 
bizimle irtibata geçin, isteklerinizi bir sonraki versiyonumuzda hayata 
geçirmeye çalışacağız.

KapGel API'ın son versiyonunun temel URL'i https://api.kapgel.com/1 dir(Sondaki
'/1' versiyon numarasını belirtiyor olup gelecekteki versiyonlarda
değişebilir).
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
(Yukarıdakı yetki anahtarı ve aşağıdakiler örnek içindir. Test ederken kendi
yetki anahtarınızı kullanmayı unutmayınız :) )

İstek bodyleri her zaman json formatında gönderilmelidir.

## KapGel Adres Formatı
KapGel'deki bütün adreslerde aşağıdaki alanlar bulunur: 
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
Her ne kadar sistemimiz açık adresi kabul etse de, adres hatalarını en aza
indirebilmek için ayrıntılı adres formatını kullanmanızı tavsiye ederiz.

## KapGel Nesne Tipleri ve Kavramlar
KapGel'i kullanan kullanıcılar, oluşturulan şirketler, şubeler ve gönderiler
KapGel nesneleri olarak adlandırılır. Her KapGel nesnesinin bir id si vardır ve
bu *_id* alaninda saklanir. Aşağıda KapGel nesnelerinin *_id* dışındaki
alanları tanımlanmıştır. 

### *User* 
KapGel sistemini kullanan kullanıcı. Kullanıcı adı (*username*) ve şifreye 
(*password*) sahiptir. Aynı zamanda bir *store* la alakalıdır. Bir kullanıcı
sadece bir *store* un  gönderilerini yönetebilir. Bir *store* birden fazla
kullanıcıya sahip olabilir. Aynı kullanıcı adı ve şifreyi kullanarak web
arayüzünden ve ya API'dan, kendi *store* u için yeni gönderi oluşturabilir ve
ya varolan gönderileri yönetebilir.

### *Contact* 
Adı, soyadı (*fullName*) ve telefon numarası (*phone*) olan bir kişiyi temsil
eder. Gönderiler teslim alınırken, teslim edilirken ve ya bir hata olduğunda
aranacak kişidir.

Örnek *contact*
```
{
  fullName: "Ahmet Yıldırım",
  phone: "+902123232233"
}
```

### *Store* 
Gönderilerini KapGel kullanarak yapan bir mağazalar ve ya dükkanlar
zincirini temsil eder. Bir ve ya birden fazla *user* tarafından yönetilir.
Bir adı (*name*), logosu (*logo*), URL'i (*url*) ve *contact* i bulunur.

### *Branch* 
Gönderilerini KapGel kullanarak yapan bir *store* un bir tane şubesini temsil
eder. Adı (*name*), adresi (*address*), ve *contact* i bulunur.

Örnek *branch*
```
{
  _id: "543ce39bc525b5a602579697",
  name: "pizzamyheart",
  address: {
    neighborhood: "Cihangir Mah.",
    status: "OK",
    apartment: "Route 39",
    number: "39",
    street: "Mebusan Ykş.",
    district: "Cihangir",
    county: "Beyoğlu",
  },
  contact: {
    fullName: "Ahmet Yıldırım",
    phone: "+902123232233"
  },
  no: 1020
}
```
### *Delivery* 
KapGel kullanılarak yapılan bir gönderiyi temsil eder. Aşağıdaki
alanlara sahiptir:
* *store:* Gönderiyi yapan *store* un *_id* si. *delivery* yi oluşturan 
kullanıcının bağlı olduğu *store*, *delivery* nin *store* u olarak kabul
edilir.
* *branch:* Gönderinin alınacağı şubenin *_id* si. Bu alan opsiyoneldir. Eğer
bu alan kullanıcı tarafından belirtilmişse kurye paketi almak için *branch* in
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
* *desi:* Gönderinin desi olarak boyutu.

Gönderinin teslim alınacağı adres *branch* ten ve ya *sender* ve *from*
alanları kullanılarak tespit edilir. Bu nedenle şubeden gönderilmeyen paketler
için şube idsi belirtilmemesi gerekir.
*branch* kullanılarak oluşturulan bir *delivery*
```
{
  "branch": "543ce39bc525b5a602579697",
  "recipient": {
    "fullName": "Mehmet Yilmaz",
    "phone": "05321234567",
    "notes": "Merdivenden cikinca 5. katta, zili calmayin"
  },
  "to": {
    "number": 21,
    "apartment": 10,
    "address": "Çay Ocağı Sk, Çubuklu Mh, Beykoz, İstanbul, Türkiye"
  },
  "desi": 3
}
```
*branch* kullanılmadan oluşturulan bir *delivery*
```
{
  "desi": 3,
  "sender": {
    "fullName": "Adem Demir",
    "phone": "05053333333",
    "notes": "Adem Bey 12-1 arası ulaşılamayabilir"
  },
  "recipient": {
    "fullName": "Mehmet Yilmaz",
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
  "password": "myheart"
}
```
Cevap:
```
{
  token: "0e34f7b44-4704-4953-bef3-b73fa616b462"
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
  kapgel-auth: 0e34f7b44-4704-4953-bef3-b73fa616b462
}
```
Cevap:
```
[
  {
    _id: "543ce39bc525b5a602579697",
    name: "pizzamyheart",
    address: {
      neighborhood: "Cihangir Mah.",
      status: "OK",
      apartment: "Route 39",
      number: "39",
      street: "Mebusan Ykş.",
      district: "Cihangir",
      county: "Beyoğlu"
    },
    contact: {
      fullName: "Ahmet Yıldırım",
      phone: "02123232233"
    },
    no: 1020
  }
]
```
### Gönderi Oluşturma:
Gönderi oluşturulurken *branch* belirtilirse teslim alma adres bilgileri
*branch* ten alınacaktır. Diğer durum da *sender* ve *from* bilgileri teslim
alma adresi olarak kullanılacaktır.
*branch* kullanılarak oluşturulan bir *delivery*
```
POST /delivery
header: {
  Content-Type: application/json
  kapgel-auth: 0e34f7b44-4704-4953-bef3-b73fa616b462
}
body: {
  "desi": 3,
  "branch": "543ce39bc525b5a602579697",
  "recipient": {
    "fullName": "Mehmet Yilmaz",
    "phone": "05321234567",
    "notes": "Merdivenden cikinca 5. katta, zili calmayin"
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
  "recipient": {
    "type":"customer",
    "fullName":"Mehmet Yilmaz",
    "phone":"+905321234567"
  },
  "sender": {
    "type":"branch","
    fullName":"Ahmet Yıldırım",
    "phone":"02123332233"
  },
  "destination": {
    "county":"Beykoz",
    "neighborhood":"Çubuklu Mah.",
    "street":"Çay Ocağı Sokak (G-34 Sk.)",
    "number":"21",
    "apartment":"10",
    "country":"Turkey",
    "city":"İstanbul"
  },
  "origin": {
    "apartment":"Route 39",
    "number":"39",
    "street":"Cihangir Caddesi",
    "neighborhood":"Cihangir Mah.",
    "district":"Cihangir",
    "county":"Beyoğlu",
    "country":"Turkey",
    "city":"İstanbul"
  },
  "branch": {
    "_id":"543ce39bc525b5a602579697",
    "name":"pizzamyheart"
  },
  "store":"543cdee4efc938760208b105",
  "events":[{
    "name":"created",
    "message":"Gönderi oluşturuldu",
    "loc":[],
    "time":"2014-10-14T10:40:35.939Z"
  }],
  "paid":false,
  "desi":3,
  "to": {
    "address": "Çay Ocağı Sk, Çubuklu Mh, Beykoz, İstanbul, Türkiye",
    "apartment":"10",
    "number":"21"
  },
  "status": "created",
  "approved":false,
  "_id":"543cfda3cb3b375a048a85ac",
  "no":1174,
  "updatedAt":"2014-10-14T10:40:35.945Z",
  "createdAt":"2014-10-14T10:40:35.922Z"
}
```
*branch* kullanılmadan oluşturulan bir *delivery*
```
POST /delivery
header: {
  Content-Type: application/json
  kapgel-auth: 0e34f7b44-4704-4953-bef3-b73fa616b462
}
body: {
  "desi": 3,
  "sender": {
    "fullName": "Adem Demir",
    "phone": "05053333333",
    "notes": "Adem Bey 12-1 arası ulaşılamayabilir"
  },
  "from": {
    "number": 12,
    "apartment" : "Urban Station Galata",
    "address": "Şahkulu Mh., Serdar-ı Ekrem Caddesi",
    "county": "Beyoğlu",
    "city": "İstanbul"
  },
  "recipient": {
    "fullName": "Mehmet Yilmaz",
    "phone": "05321234567",
    "notes": "Merdivenden cikinca 5. katta, zili calmayin"
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
  "recipient": {
    "type": "customer",
    "fullName": "Mehmet Yilmaz",
    "phone": "+905321234567"
  },
  "sender": {
    "type": "store",
    "fullName": "Adem Demir",
    "phone": "+905053333333"
  },
  "destination":{
    "county": "Beykoz",
    "neighborhood": "Çubuklu Mah.",
    "street": "Çay Ocağı Sokak (G-34 Sk.)",
    "number": "21",
    "apartment": "10",
    "country": "Turkey",
    "city": "İstanbul"
  },
  "origin": {
    "neighborhood": "Şahkulu Mah.",
    "street": "Serdar-ı Ekrem Caddesi",
    "county": "Beyoğlu",
    "number": "12",
    "apartment": "Urban Station Galata",
    "country": "Turkey",
    "city": "İstanbul"
  },
  "store": "543cdee4efc938760208b105",
  "events": [{
    "name": "created",
    "message": "Gönderi oluşturuldu",
    "loc": [],
    "time": "2014-10-14T10:47:20.651Z"
  }],
  "paid": false,
  "desi": 3,
  "to": {
    "address": "Çay Ocağı Sk, Çubuklu Mh, Beykoz, İstanbul, Türkiye",
    "apartment": "10",
    "number": "21"
  },
  "from": {
    "address": "Şahkulu Mh., Serdar-ı Ekrem Caddesi",
    "apartment": "Urban Station Galata",
    "number": "12",
    "county": "Beyoğlu",
    "city": "İstanbul"
  },
  "status": "created",
  "approved": false,
  "_id":"543cff38cb3b375a048a85bd",
  "no":1175,
  "updatedAt": "2014-10-14T10:47:20.656Z",
  "createdAt":"2014-10-14T10:47:20.640Z"
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
Cevap:
```
[{
  "_id": "543d0138cb3b375a048a85c9",
  "recipient": {
    "type": "customer",
    "fullName": "Mehmet Yilmaz",
    "phone": "+905321234567"
  },
  "sender": {
    "type":"store",
    "fullName": "Adem Demir",
    "phone": "+905053333333"
  },
  "destination": {
    "county": "Beykoz",
    "neighborhood": "Çubuklu Mah.",
    "street": "Çay Ocağı Sokak (G-34 Sk.)",
    "status": "OK",
    "number": "21",
    "apartment": "10"
  },
  "origin": {
    "neighborhood": "Şahkulu Mah.",
    "street": "Serdar-ı Ekrem Caddesi",
    "status": "OK",
    "county": "Beyoğlu",
    "number": "12",
    "apartment": "Urban Station Galata"
  },
  "branch": null,
  "events": [{
    "name": "created",
    "message": "Gönderi oluşturuldu",
    "loc": [],
    "time": "2014-10-14T10:55:52.244Z"
  }],
  "desi": 3,
  "status": "created",
  "approved": false,
  "no": 1176,
  "updatedAt": "2014-10-14T10:55:52.248Z",
  "createdAt":"2014-10-14T10:55:52.233Z"
}, {
  "_id": "543d014ecb3b375a048a85d7",
  "recipient": {
    "type": "customer",
    "fullName": "Mehmet Yilmaz",
    "phone": "+905321234567"
  },
  "sender": {
    "type": "branch",
    "fullName": "Ahmet Yıldırım",
    "phone": "02123332233"
  },
  "destination": {
    "county":"Beykoz",
    "neighborhood":"Çubuklu Mah.",
    "street":"Çay Ocağı Sokak (G-34 Sk.)",
    "status":"OK",
    "number":"21",
    "apartment":"10"
  },
  "origin": {
    "status": "unsure",
    "apartment": "Route 39",
    "number": "39",
    "street": "Cihangir Caddesi",
    "neighborhood": "Cihangir Mah.",
    "district": "Cihangir",
    "county": "Beyoğlu"
  },
  "branch": {
    "_id": "543ce39bc525b5a602579697",
    "name": "pizzamyheart"
  },
  "events": [{
    "name": "created",
    "message": "Gönderi oluşturuldu",
    "loc": [],
    "time": "2014-10-14T10:56:14.981Z"
  }],
  "desi": 3,
  "status": "created",
  "approved": false,
  "no":1177,
  "updatedAt": "2014-10-14T10:56:14.986Z",
  "createdAt":"2014-10-14T10:56:14.974Z"
}]
```
Tek bir gonderi icin URL'de delivery *_id* sini belirtin:
```
GET /delivery/543d014ecb3b375a048a85d7
header: {
  Content-Type: application/json
  kapgel-auth: 0b833ef40-245c-11e4-8b24-05df52b6cb67
}
```
Cevap:
```
{
  "_id": "543d014ecb3b375a048a85d7",
  "recipient": {
    "type": "customer",
    "fullName": "Mehmet Yilmaz",
    "phone": "+905321234567"
  },
  "sender": {
    "type": "branch",
    "fullName": "Ahmet Yıldırım",
    "phone": "02123332233"
  },
  "destination": {
    "county":"Beykoz",
    "neighborhood":"Çubuklu Mah.",
    "street":"Çay Ocağı Sokak (G-34 Sk.)",
    "status":"OK",
    "number":"21",
    "apartment":"10"
  },
  "origin": {
    "status": "unsure",
    "apartment": "Route 39",
    "number": "39",
    "street": "Cihangir Caddesi",
    "neighborhood": "Cihangir Mah.",
    "district": "Cihangir",
    "county": "Beyoğlu"
  },
  "branch": {
    "_id": "543ce39bc525b5a602579697",
    "name": "pizzamyheart"
  },
  "events": [{
    "name": "created",
    "message": "Gönderi oluşturuldu",
    "loc": [],
    "time": "2014-10-14T10:56:14.981Z"
  }],
  "desi": 3,
  "status": "created",
  "approved": false,
  "no":1177,
  "updatedAt": "2014-10-14T10:56:14.986Z",
  "createdAt":"2014-10-14T10:56:14.974Z"
}
```
### Gönderi onaylama
```
POST /delivery/543d014ecb3b375a048a85d7/approve
header: {
  kapgel-auth: 0b833ef40-245c-11e4-8b24-05df52b6cb67
}
```
Cevap:
```
{
  "_id": "543d014ecb3b375a048a85d7",
  "recipient": {
    "type": "customer",
    "fullName": "Mehmet Yilmaz",
    "phone": "+905321234567"
  },
  "sender": {
    "type": "branch",
    "fullName": "Ahmet Yıldırım",
    "phone": "02123332233"
  },
  "destination": {
    "county":"Beykoz",
    "neighborhood":"Çubuklu Mah.",
    "street":"Çay Ocağı Sokak (G-34 Sk.)",
    "status":"OK",
    "number":"21",
    "apartment":"10"
  },
  "origin": {
    "status": "unsure",
    "apartment": "Route 39",
    "number": "39",
    "street": "Cihangir Caddesi",
    "neighborhood": "Cihangir Mah.",
    "district": "Cihangir",
    "county": "Beyoğlu"
  },
  "branch": {
    "_id": "543ce39bc525b5a602579697",
    "name": "pizzamyheart"
  },
  "events": [{
    "name": "created",
    "message": "Gönderi oluşturuldu",
    "loc": [],
    "time": "2014-10-14T10:56:14.981Z"
  }],
  "desi": 3,
  "status": "created",
  "approved": true,
  "no":1177,
  "updatedAt": "2014-10-14T10:56:14.986Z",
  "createdAt":"2014-10-14T10:56:14.974Z"
}
```
### iptal etme
```
POST /delivery/53edfd328c28863e01c63d06/cancel
header: {
  kapgel-auth: 0b833ef40-245c-11e4-8b24-05df52b6cb67
}
```
Cevap:
```
{
  "_id": "543d014ecb3b375a048a85d7",
  "recipient": "543ceb2e8b7b933203b8069d",
  "sender": "543ce39bc525b5a602579695",
  "destination": "543d014ecb3b375a048a85d8",
  "origin": "543ce39bc525b5a602579696",
  "branch": "543ce39bc525b5a602579697",
  "store": "543cdee4efc938760208b105",
  "events": [{
    "name": "created",
    "message": "Gönderi oluşturuldu",
    "loc": [],
    "time": "2014-10-14T10:56:14.981Z"
  }, {
    "name":"cancelled",
    "message":"The status of delivery changed manually to cancelled",
    "loc":[],
    "time":"2014-10-14T11:07:09.147Z"
  }],
  "paid":false,
  "desi":3,
  "to": {
    "address": "Çay Ocağı Sk, Çubuklu Mh, Beykoz, İstanbul, Türkiye",
    "apartment": "10",
    "number": "21"
  },
  "status":"cancelled",
  "approved":true,
  "no":1177,
  "updatedAt": "2014-10-14T11:07:09.152Z",
  "createdAt": "2014-10-14T10:56:14.974Z"
}
```
