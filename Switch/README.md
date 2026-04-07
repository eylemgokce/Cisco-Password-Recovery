# Cisco Switch Parola Kurtarma (Password Recovery) İşlemi

Bu dokümantasyon, Cisco Switch (örn. Catalyst 2960) cihazlarında unutulan veya kaybedilen yönetim parolalarının, cihazın mevcut yapılandırmasına (VLAN'lar, atanmış portlar vb.) zarar vermeden nasıl kurtarılacağını adım adım belgelemektedir.

## 📌 Parola Kurtarmanın Önemi ve Gerekliliği

* **Sıfır Veri Kaybı:** Switch üzerinde tanımlanmış VLAN'lar, trunk portlar veya port güvenlik ayarlarının silinmemesi için cihazı fabrika ayarlarına döndürmek yerine parola kurtarma işlemi uygulanır.
* **Fiziksel Güvenlik Prensibi:** Bu işlem, cihaza fiziksel olarak erişebilen ve konsol bağlantısı kurabilen bir ağ yöneticisi gerektirir.
* **Mimari Farklılık:** Router'lardan farklı olarak Switch'lerde konfigürasyon register (confreg) değeri değiştirilmez. Bunun yerine flaş bellekteki yapılandırma dosyasının (`config.text`) adı geçici olarak değiştirilerek cihazın mevcut ayarları görmezden gelmesi sağlanır.

---

## 🛠️ Ön Koşullar
* Switch'e fiziksel erişim (Power cycle yapabilmek için).
* Cihaza bağlı bir Konsol Kablosu (Console Cable).
* Terminal emülatörü (Packet Tracer CLI, PuTTY vb.).

---

## 🚀 Adım Adım Kurtarma Süreci

### Adım 1: Cihazı Yeniden Başlatma ve Araya Girme
Cihazın gücünü kesin (fişini çekin veya Packet Tracer'da "Power Cycle" yapın). Cihaz yeniden açılırken ön panelindeki **"Mode"** düğmesine basılı tutun.
* Terminal ekranında `switch:` komut satırını görene kadar bekleyin ve sonra düğmeyi bırakın. Bu, ROMMON benzeri donanım modudur.

### Adım 2: Flash Belleği Başlatma
Switch'in dosya sistemine erişebilmek için flash belleği başlatın:

```text
switch: flash_init
```

### Adım 3: Yapılandırma Dosyasının Adını Değiştirme
Flash içindeki dosyaları kontrol edip, cihazın açılışta ayarları bulamaması için `config.text` dosyasının adını değiştirin:

```text
switch: dir flash:
switch: rename flash:config.text flash:config.eski
```

### Adım 4: Cihazı Boot Etme
Dosya adı değiştiği için cihaz boş bir konfigürasyonla açılacaktır:

```text
switch: boot
```

### Adım 5: Başlangıç Sihirbazını Atlama ve Ayrıcalıklı Moda Geçiş
Cihaz açıldığında kurulum sihirbazını reddedin ve şifresiz olarak yönetici moduna geçin:

```text
Would you like to enter the initial configuration dialog? [yes/no]: no
Switch> enable
Switch#
```

### Adım 6: Dosya Adını Eski Haline Getirme
İçeri girdikten sonra flash bellekteki dosyanın adını tekrar orijinal haline döndürün:

```text
Switch# rename flash:config.eski flash:config.text
```

### Adım 7: Eski Ayarları RAM'e Yükleme 
Adını düzelttiğimiz orijinal yapılandırma dosyasını çalışan belleğe çekerek eski ayarları geri getirin:

```text
Switch# copy flash:config.text system:running-config
```

*(Onaylamak için Enter tuşuna basın. Bunu yaptığınızda cihazın adının tekrar eski haline, örneğin `SW1`'e döndüğünü göreceksiniz.)*

### Adım 8: Parolaları Yenileme
Eski konfigürasyon RAM'e yüklendi. Artık global yapılandırma moduna geçip unutulan parolaları güvenle ezebiliriz:

```text
SW1# configure terminal
SW1(config)# enable secret [YENİ_PAROLANIZ]
```

*(İsteğe bağlı olarak Konsol ve VTY parolalarını da bu aşamada değiştirebilirsiniz).*

### Adım 9: Değişiklikleri Kaydetme
Belirlediğiniz yeni parolalarla birlikte ayarları kalıcı belleğe (NVRAM) kaydedin:

```text
SW1# copy running-config startup-config
```

veya kısaca:

```text
SW1# write memory
```

🎉 **İşlem Tamam!** Switch'in mevcut VLAN ve port ayarlarına hiçbir zarar vermeden yönetim haklarını başarıyla geri kazandınız.
