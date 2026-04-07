# Cisco Ağ Cihazları: Parola Kurtarma ve Yönetimsel Erişim Laboratuvarı

Bu depo, yönetim parolaları kaybedilmiş Cisco ağ cihazlarına (Router ve Switch), mevcut yapılandırma dosyalarına ve ağ topolojisine zarar vermeden yeniden erişim sağlama süreçlerini içermektedir.

## 📂 Laboratuvar İçeriği

Bu çalışma iki ana cihaz ailesine ayrılmıştır. İlgili dokümantasyona ve adım adım uygulama adımlarına aşağıdaki bağlantılardan ulaşabilirsiniz:

* 🖧 **[Router Parola Kurtarma (Password Recovery) Rehberi](./Router/README.md)**
  * ROMMON modu kullanımı ve Configuration Register (`0x2142`) manipülasyonu.
* 🖧 **[Switch Parola Kurtarma (Password Recovery) Rehberi](./Switch/README.md)**
  * Flash dosya sistemi müdahalesi ve `config.text` bypass işlemleri.

## 🎯 Projenin Amacı ve Kazanımlar
Bu laboratuvar ortamı sayesinde hedeflenen yetkinlikler:
* Acil durumlarda (Incident Response) ağ cihazlarının kontrolünü kayıpsız bir şekilde geri alma.
* Cisco IOS boot (başlangıç) sürecinin donanım seviyesinde (ROM Monitor) anlaşılması.
* NVRAM ve RAM (startup-config / running-config) arasındaki veri akışının ve dosya sisteminin (Flash) güvenli yönetimi.

---
