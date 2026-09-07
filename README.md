[README.md](https://github.com/user-attachments/files/31903828/README.md)
# BİZ – Çiftler Oyunu V2

İki farklı telefondan aynı oyuna bağlanabilen mobil-first PWA. Host oda açar,
guest 6 haneli kodla katılır; PeerJS/WebRTC üzerinden doğrudan cihazdan
cihaza (P2P) veri akışı ile sorular senkron ilerler.

## Dosya yapısı

```
public/
  index.html      ana uygulama (UI + CSS + JS + bağlantı mantığı + soru havuzu)
  manifest.json   PWA manifest
vercel.json       Vercel static deployment ayarı
README.md         bu dosya
```

## Yerelde çalıştırma

Statik dosyalardır, herhangi bir static server yeterlidir:

```
npx serve public
```

HTTPS zorunludur (WebRTC güvenlik gereksinimi) — bu yüzden `localhost`
dışında mutlaka Vercel/Netlify/GitHub Pages gibi HTTPS sağlayan bir ortamda
test edin.

## Vercel'e deploy

1. Bu repoyu `sekerozkn-max/B-Z` GitHub deposuna push edin.
2. Vercel'de "Import Project" ile bu repoyu seçin.
3. `vercel.json` zaten `public/` klasörünü statik olarak servis edecek şekilde
   ayarlı — ekstra build komutu gerekmez.
4. Deploy sonrası verilen HTTPS adresini iki telefonda açın.

## Nasıl oynanır?

1. Bir telefonda **Oyuna Başla** → oyun modunu seç.
2. O telefonda **Oda Oluştur**a bas, çıkan 6 haneli kodu diğer telefona
   gönder (WhatsApp/SMS).
3. İkinci telefonda kodu gir, **Koda Katıl**a bas.
4. Oda sahibi **Oyunu Başlat**a basınca sorular iki cihazda eşzamanlı gelir.

## V2'de yapılan güvenilirlik iyileştirmeleri

- **TURN sunucusu eklendi.** Önceki sürüm sadece Google STUN kullanıyordu;
  mobil operatör ağlarında (simetrik NAT) bu genelde doğrudan bağlantıyı
  başaramıyordu. Şimdi Google STUN + ücretsiz Open Relay TURN sunucuları
  devrede. **Not:** Open Relay ücretsiz kota ile çalışır ve gerçek yoğun
  production trafiği için garantili değildir. Gerçek yayına almadan önce
  ücretli bir TURN sağlayıcısına (Twilio Network Traversal, Xirsys,
  metered.ca ücretli plan) geçmenizi öneririz — sadece `ICE_SERVERS`
  dizisini `index.html` içinde güncellemeniz yeterli.
- **Oda kodu çakışması otomatik çözülüyor.** Seçilen kod zaten kullanımdaysa
  (`unavailable-id`) yeni bir kodla otomatik olarak yeniden denenir (5 deneme).
  Kod alfabesi ayrıca 0/O/1/I/L gibi karışan karakterler çıkarılarak
  daraltıldı.
- **Yeniden bağlanma mantığı eklendi.** Bağlantı koparsa (ağ değişimi, sekme
  arka plana alınması, sinyal sunucusu kopması) uygulama artan gecikmeyle
  6 kez otomatik yeniden bağlanmayı dener ve durumu ekranda gösterir.
- **iOS Safari arka plan sorunu ele alındı.** Sekme arka plana alınıp tekrar
  öne geldiğinde (`visibilitychange`) bağlantı kontrol edilip gerekirse
  yeniden kurulur.
- **Cevap senkron mantığı sadeleştirildi.** Eski kodda `window` global
  değişkenleri ve sonradan üstüne yamanmış (patch) ikinci bir `handle`
  fonksiyonu vardı; biri hiç tetiklenmeyen ölü kod içeriyordu. Artık tek,
  okunabilir bir akış var: her iki taraf cevabını gönderir, **sadece host**
  sonucu hesaplar ve iki tarafa aynı anda gönderir — böylece hiçbir oyuncu
  diğerinin cevabını erken göremez.
- **Production dosya yapısı + Vercel config.** `public/` klasörü ve
  `vercel.json` eklendi.

## Bilinen sınırlamalar / sonraki adımlar

- State (skor, soru sırası) sadece bellekte tutuluyor; sayfa yenilenirse
  oyun sıfırlanır. Kalıcılık için `localStorage`'a oyun durumu yazılabilir.
- Ücretsiz TURN sunucusu tek nokta arıza riski taşıyor; production'da kendi
  TURN hesabınızı kullanmanız önerilir.
- Oda kodları şu an sunucu tarafında değil, PeerJS Cloud'un kendi ID
  çakışma kontrolüyle yönetiliyor; çok yoğun eşzamanlı kullanımda kendi
  signaling sunucunuzu (basit bir WebSocket relay) kurmak daha sağlam olur.
