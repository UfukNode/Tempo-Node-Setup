# Tempo Node Kurulum Rehberi

Bu rehber, **Tempo** node'unu sıfırdan kurmak isteyenler için hazırlanmıştır. Aşağıdaki adımları sırasıyla uygulamanız yeterlidir.

---

## Sistem Gereksinimleri

| Bileşen  | Gereksinim            |
| -------- | --------------------- |
| **CPU**  | En az 8 çekirdek      |
| **RAM**  | Minimum 16 GB         |
| **Disk** | En az 400 GB boş alan |

---

## 1. Sunucu Kiralama (Serverica)

- Aşağıdaki bağlantıya giderek kayıt ol:
  https://clients.servarica.com/store/bf-2025-kvm-slim-slice
- 14 dolarlık paketi seç.
- VM Template kısmında Ubuntu 24 seçili olsun.
- Hostname alanına istediğin bir isim yaz.
- Ödemeni kripto ile yaparak sunucunu kirala.
- Sunucun aktif olduktan sonra, kayıt olduğun mail adresine bir bilgilendirme maili gelecektir. Mailde verilen IP üzerinden sunucuya giriş yapabilirsin.
- Şifreni öğrenmek için Serverica hesabına giriş yap ve "Ürünlerim" kısmından sunucunu seç.

---

## DNS Ayarları (Opsiyonel):

⚠️ Eğer sunucuyu severicadan kiraladaysan aşağıdaki adımları yapmadan kuruluma geçme.

```bash
sudo mkdir -p /etc/cloud/cloud.cfg.d
echo "network: {config: disabled}" | sudo tee /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg
```

```bash
sudo nano /etc/netplan/50-cloud-init.yaml
```

- `nameservers` bölümünü aşağıdaki gibi değiştirerek ayarla:
```
nameservers:
  addresses:
    - 1.1.1.1
    - 8.8.8.8
```
- "CTRL + X" tuşlayıp enter yaparak çık.

<img width="293" height="135" alt="image" src="https://github.com/user-attachments/assets/af2ffeb7-f9b3-4f30-a859-e4f112c0cf5b" />

```bash
sudo netplan apply
sudo systemctl restart systemd-resolved
```

---

## 2. Sistem güncelleme ve gerekli paketler

```bash
sudo apt update && sudo apt -y upgrade
sudo apt install -y curl screen iptables build-essential git wget lz4 jq make gcc nano openssl \
automake autoconf htop nvme-cli pkg-config libssl-dev libleveldb-dev \
tar clang bsdmainutils ncdu unzip ca-certificates net-tools iputils-ping
```

---

## 3. Rust Kurulum:

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source $HOME/.cargo/env
```

## 4. Tempo Kurulum:

```bash
cargo install --git https://github.com/tempoxyz/tempo.git tempo --root /usr/local --force
```

Örnek Çıktı:

---

## 5. Key Oluştur:

```bash
mkdir -p $HOME/tempo/keys
```

```bash
export PATH=/usr/local/bin:$PATH
hash -r
which tempo
```

```bash
tempo consensus generate-private-key --output $HOME/tempo/keys/signing.key
```

- Aşağıdaki komutu girdikten sonra örnek çıktıdaki gibi "OK" yazılarının çıktığından emin olun.

```bash
tempo consensus calculate-public-key --private-key $HOME/tempo/keys/signing.key
```

Örnek Çıktı:

<img width="761" height="53" alt="Ekran görüntüsü 2025-12-31 180305" src="https://github.com/user-attachments/assets/89ade584-1037-49fa-bb9d-8ed9532bf11c" />

---

## 6. Snapshot İndir

```bash
screen -S tempo
```

```bash
mkdir -p $HOME/tempo/data
tempo download --datadir /root/tempo/data
```

Örnek Çıktı:

<img width="1659" height="122" alt="Ekran görüntüsü 2025-12-31 011738" src="https://github.com/user-attachments/assets/16871381-500c-4903-bf6a-67e608910595" />

---

## 7. Node Çalıştırma

```bash
tempo node --datadir /root/tempo/data \
  --chain testnet \
  --port 30303 \
  --discovery.addr 0.0.0.0 \
  --discovery.port 30303 \
  --consensus.signing-key /root/tempo/keys/signing.key \
  --consensus.fee-recipient <cuzdan_adresi>
```

- Not: `<cuzdan_adresi>` kısmına kendi cüzdan adresini gir.

Örnek Komut Çıktısı:

<img width="604" height="117" alt="Ekran görüntüsü 2025-12-31 011934" src="https://github.com/user-attachments/assets/ce57fa05-0d58-4226-a0b4-89715b912506" />

Örnek Log Çıktısı:

<img width="686" height="154" alt="image" src="https://github.com/user-attachments/assets/fdd25c88-be5c-41c7-8bae-a63676d5f3fb" />

---

## 8. Gerekli Screen Komutları

- Screen oturumunu kapatmadan çıkmak için:
```bash
Ctrl+a d
```

- Oluşturduğunuz screene tekrar dönek için:
```bash
screen -r tempo
```

---

## 9. Dashboard / Login / Peer ID

- Web panel bulunmaz; durum takibi loglar üzerinden yapılır.
- Sync ve blok yüksekliği için explorer kontrolü:
  `https://explorer.tempo.xyz/`

---

## ✅ Ek Notlar / Tavsiyeler

- Validator set yönetimi Tempo ekibi tarafından izinlidir; validator olmak için `partners@tempo.xyz` ile iletişim gerekir.
- Validator aktif sete hemen girmeyebilir; genelde 48-72 saat sürebilir.
