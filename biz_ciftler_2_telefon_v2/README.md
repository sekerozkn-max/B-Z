# BİZ – Çiftler Oyunu V2
İki farklı telefondan aynı oyuna bağlanabilen PWA prototipi.

## Nasıl kullanılır?
1. Projeyi HTTPS ile yayınla (Netlify/Vercel/GitHub Pages gibi).
2. İki telefonda aynı web adresini aç.
3. Birinci telefonda Oda Oluştur.
4. Çıkan 6 haneli kodu ikinci telefona gönder.
5. İkinci telefonda kodu girip Koda Katıl.
6. Oda sahibi oyun modunu seçip Oyunu Başlatır.

Bağlantı için PeerJS WebRTC kullanılır. PeerJS Cloud, iki cihazın bağlantı kurmasına yardımcı olan sinyalleme katmanıdır; oyun verisi WebRTC data channel üzerinden doğrudan cihazlar arasında aktarılır.
