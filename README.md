# 🔓 Insecure Data Storage - Android Security Demo

## 📱 Proje Hakkında

Bu proje, Android uygulamalarında **Insecure Data Storage** (Güvensiz Veri Depolama) zafiyetini göstermek için yapılmıştır.

Basit bir not uygulaması üzerinden, yerel verilerin nasıl güvensiz şekilde saklanabileceğini ve bu verilere nasıl erişilebileceğini gösterir.

---

## 🔓 Zafiyetler

### 1. **Plain Text Data Storage**
- Kullanıcı notları **SharedPreferences**'ta şifresiz saklanıyor
- Hassas bilgiler (şifreler, tokenlar) düz metin olarak görünür

![SharedPreferences Logic](https://i.hizliresim.com/49g3u7u.png)

### 2. **debuggable=true**
- Manifest'te `debuggable="true"` ayarı aktif
- ADB ile `run-as` komutu çalışır
- Internal Storage'a erişim sağlanır

### 3. **allowBackup=true**
- ADB backup ile tüm uygulama verisi çekilebilir
- Offline olarak analiz edilebilir

![allowBackup=true](https://i.hizliresim.com/sutbi90.png)

### 4. **Root Access**
- Root erişimi varsa `/data/data/` klasörüne direkt erişim
- Başka uygulamaların verileri de okunabilir

![adb Komutları](https://i.hizliresim.com/49g3u7u.png)

## 🛡️ Nasıl Korunulur?

### 1. EncryptedSharedPreferences Kullan

**Güvensiz:**
```kotlin
val prefs = getSharedPreferences("notes", MODE_PRIVATE)
prefs.edit().putString("note", "Şifre: 12345").apply()
```

**Güvenli:**
```kotlin
val masterKey = MasterKey.Builder(context)
    .setKeyScheme(MasterKey.KeyScheme.AES256_GCM)
    .build()

val prefs = EncryptedSharedPreferences.create(
    context,
    "secure_notes",
    masterKey,
    EncryptedSharedPreferences.PrefKeyEncryptionScheme.AES256_SIV,
    EncryptedSharedPreferences.PrefValueEncryptionScheme.AES256_GCM
)
