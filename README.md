### Kapgel İş Gönderme ve Takip Sistemi API dökümantasyonu
Kapgel API, kullanıcıların HTTP üzerinden güvenli bir şekide kapgel sistemine 
programatik olarak erişmesini sağlar. Kapgel API hem son kullanıcıların yeni 
gönderi girmeleri için hem de girilmiş olan gönderilerin bir admin tarafından 
yönetimi için kullanılabilir. Bu sistemden beklenebilecek bazı özellikler şu 
anda eksik olsa da nihai amacımız gönderi yapan bütün şirketlerin kapgel'i 
sistemlerine sorunsuz ve rahat bir sekilde entegre edebilmelerini sağlamaktır. 
Eğer bir sorunuz olursa ve ya eklenmesini istediğiniz bir özellik olursa lütfen 
bizimle irtibata geçin, isteklerinizi bir sonraki versiyonumuzda hayata 
geçirmeye çalışacağız.

Kapgel API'ın son versiyonunun temel URL'i https://api.kapgel.com/1 dir. HTTP 
istekleri bu URL'i kullanan adreslere gönderilecektir. Bu dökümantasyondaki 
diğer bütün adresler bu temel adresin ardına eklenmelidir. Bütün isteklerde
```
Content-type: application/json
```
olarak içerik türü belirtilmelidir. Kimlik doğrulama dışındaki bütün isteklerde
de yetki anahtarı aşağıdaki gibi belirtilmelidir:
```
Content-type: application/json
kapgel-auth: 0b833ef40-245c-11e4-8b24-05df52b6cb67
```
İstek bodyleri her zaman json formatında gönderilmelidir. 

### Kimlik Doğrulama ve yetki anahtarına ulaşma
Bu istek sadece bir defa yapılıp yetki anahtarına ulaşmak için kullanılabilir. 
Yetki anahtarı değişmediği için programınızda her zaman kullanıcı adı ve şifre
göndermek yerine, yetki anahtarınızı kaydedip onu bir daha sorgulamamanızı 
tavsiye ediyoruz.

İstek:
```
POST /auth
header: {
  Content-Type: application/json
}
body: {
  "username": "pizzamyheart", 
  "password":"%@9vawB3t2$I"
}
```
Cevap:
```
{
  "result": "0b833ef40-245c-11e4-8b24-05df52b6cb67"
}
```
Cevaptaki 'result' öğesi sizin yetki anahtarınızdır.
### Gönderi oluşturma:
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
