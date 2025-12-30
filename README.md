## Tempo Node Kurulum Rehberi

Bu rehber, **Tempo** node'unu sııirdan kurmak isteyenler icin hazirlanmistir. Asagidaki adimlari sirasiyla uygulamaniz yeterlidir.

---

## 2. Sistem Gereksinimleri

| Bilesen  | Gereksinim            |
| -------- | --------------------- |
| **CPU**  | En az 8 cekirdek      |
| **RAM**  | Minimum 16 GB         |
| **Disk** | En az 300 GB bos alan |

---

## 3. Sunucu Kirala

1. Ubuntu 22.04 kurulu bir VPS/Cloud sunucu hazirla.
2. Root veya sudo erisimi oldugunu dogrula.

---

## 4. Sistem guncelleme ve gerekli paketler

1. Sistem guncelle ve gerekli paketleri kur:
```bash
sudo apt update && sudo apt -y upgrade
sudo apt install -y curl screen iptables build-essential git wget lz4 jq make gcc nano \
automake autoconf htop nvme-cli pkg-config libssl-dev libleveldb-dev \
tar clang bsdmainutils ncdu unzip ca-certificates net-tools iputils-ping
```

---

## 5. Proje Dosyalarinin Kurulumu

1. Tempo'yu kur:
```bash
curl -L https://tempo.xyz/install | bash
```
2. Ortam degiskenlerini yenile:
```bash
source ~/.bashrc
```
3. Surumu dogrula:
```bash
tempo --version
```

- Örnek Çıktı:

<img width="736" height="391" alt="image" src="https://github.com/user-attachments/assets/9d5fa9d4-4953-45fa-9f2e-60b86b7e9509" />

---

## 6. ED25519 Key Olustur

1. Key dizinini olustur:
```bash
mkdir -p $HOME/tempo/keys
```
2. Key'i olustur:
```bash
tempo consensus generate-private-key --output $HOME/tempo/keys/signing.key
```
3. Key'i dogrula:
```bash
tempo consensus calculate-public-key --private-key $HOME/tempo/keys/signing.key
```

---

## 7. Snapshot Indir

1. Veri dizinini hazirla:
```bash
mkdir -p $HOME/tempo/data
```
2. Guncel snapshot indir:
```bash
tempo download
```

---

## 8. Node Calistirma

1. Screen oturumu baslat:
```bash
screen -S tempo
```
2. Node'u calistir:
```bash
tempo node --datadir $HOME/tempo/data \
  --port 30303 \
  --discovery.addr 0.0.0.0 \
  --discovery.port 30303 \
  --consensus.signing-key $HOME/tempo/keys/signing.key \
  --consensus.fee-recipient <validator_wallet_address>
```

---

## 9. Gerekli Screen Komutlari

1. Screen oturumundan ayril:
```bash
Ctrl+a d
```
2. Screen oturumuna geri don:
```bash
screen -r tempo
```

---

## 10. Dashboard / Login / Peer ID

1. Web panel bulunmaz; durum takibi loglar uzerinden yapilir.
2. Sync ve blok yuksekligi icin explorer kontrolu:
`https://explorer.tempo.xyz/`

---

## 11. Ek Notlar / Tavsiyeler

1. Validator set yonetimi Tempo ekibi tarafindan izinlidir; validator olmak icin `partners@tempo.xyz` ile iletisim gerekir.
2. Validator aktif sete hemen girmeyebilir; genelde 48-72 saat surebilir.
