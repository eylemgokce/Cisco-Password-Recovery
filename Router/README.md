# Cisco Router Parola Kurtarma (Password Recovery) İşlemi

Bu depo, Cisco Router cihazlarında unutulan veya kaybedilen yönetim parolalarının, cihazın mevcut yapılandırmasına (configuration) zarar vermeden nasıl kurtarılacağını adım adım belgelemektedir. 

## 📌 Parola Kurtarmanın Önemi ve Gerekliliği

Ağ altyapılarında güvenlik her şeyden önce gelir, ancak zaman zaman cihazların parolaları kaybedilebilir veya eski bir ağ yöneticisinden devralınan cihazlara erişim sağlanamayabilir. Bu tür durumlarda parola kurtarma işlemi hayati bir önem taşır.

* **Sıfır Veri Kaybı:** Cihazı fabrika ayarlarına döndürmek (hard reset) IP adreslerinin, yönlendirme (routing) protokollerinin ve güvenlik kurallarının silinmesine yol açar. Parola kurtarma, mevcut `startup-config` dosyasını korur.
* **Fiziksel Güvenlik Prensibi:** Cisco'nun güvenlik felsefesine göre, "bir cihaza fiziksel olarak erişemeyen biri, o cihazın parolasını değiştiremez." Bu işlem yalnızca konsol bağlantısı (Console Cable) ile cihaza fiziksel olarak bağlı olan bir yönetici tarafından gerçekleştirilebilir.
* **Acil Durum Müdahalesi:** Ağ kesintilerinde veya acil yapılandırma değişikliklerinde cihaza hızla tekrar yönetici (Privileged EXEC) haklarıyla erişim sağlanmasını garanti eder.

---

## 🛠️ Ön Koşullar
* Yönlendiriciye (Router) fiziksel erişim.
* Cihaza bağlı bir Konsol Kablosu (Console Cable).
* PuTTY, TeraTerm veya Packet Tracer CLI gibi bir terminal emülatörü.

---

## 🚀 Adım Adım Kurtarma Süreci

Aşağıdaki adımlar, cihazın başlangıç (boot) aşamasında araya girilerek NVRAM'deki ayarların geçici olarak yok sayılması ve ardından parolaların ezilmesi mantığına dayanır.

### Adım 1: ROMMON Moduna Geçiş
Cihazı fiziksel olarak yeniden başlatın (kapatıp açın). Cihaz boot edilirken terminal ekranında klavyenizden kesme sinyali gönderin:
* `Ctrl + C` veya `Ctrl + Break`
* Ekranda `rommon 1 >` komut satırını görmelisiniz.

### Adım 2: Configuration Register Değerini Değiştirme
Cihazın açılışta kayıtlı ayarları (`startup-config`) yok sayması için register değerini `0x2142` olarak değiştirin:

```text
rommon 1 > confreg 0x2142
```

### Adım 3: Cihazı Yeniden Başlatma
Yeni register değeriyle cihazı başlatın:

```text
rommon 2 > reset
```

### Adım 4: Başlangıç Sihirbazını Atlama
Cihaz boş bir konfigürasyonla açılacağı için size kurulum sihirbazını soracaktır. Bunu reddederek `User EXEC` moduna geçin:

```text
Would you like to enter the initial configuration dialog? [yes/no]: no
Router>
```

### Adım 5: Ayrıcalıklı Moda Geçiş ve Eski Ayarları Kurtarma (Kritik Adım)
Cihaz şifre sormadan yönetici moduna geçmenize izin verecektir. **Ancak**, yeni bir şifre belirlemeden önce eski ayarları NVRAM'den RAM'e çekmeniz şarttır!

```text
Router> enable
Router# copy startup-config running-config
```

> ⚠️ **Uyarı:** `copy running-config startup-config` komutunu bu aşamada **kesinlikle kullanmayın**, aksi takdirde boş ayarları eskinin üstüne yazar ve tüm konfigürasyonu kaybedersiniz.

### Adım 6: Parolaları Yenileme
Eski konfigürasyon RAM'e yüklendi. Artık global yapılandırma moduna geçip unutulan parolayı güvenli bir şekilde ezebiliriz:

```text
Router# configure terminal
Router(config)# enable secret [YENİ_PAROLANIZ]
```

### Adım 7: Register Değerini Normale Döndürme
Cihazın bir sonraki kapanıp açılmasında ayarları tekrar normal şekilde (`startup-config`'den) okuması için register değerini standart olan `0x2102`'ye geri çekin:

```text
Router(config)# config-register 0x2102
Router(config)# exit
```

### Adım 8: Değişiklikleri Kaydetme
Belirlediğiniz yeni parolayı ve register ayarını kalıcı hale getirin:

```text
Router# copy running-config startup-config
```

veya kısaca:

```text
Router# write memory
```

🎉 **İşlem Tamam!** Cihazın mevcut ağ yapılandırmasına hiçbir zarar vermeden yönetim haklarını başarıyla geri kazandınız.
