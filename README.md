## Tempo Node Kurulum Rehberi

Bu rehber, **Tempo** node’unu sifirdan kurmak isteyenler icin hazirlanmistir. Asagidaki adimlari sirasiyla uygulamaniz yeterlidir.

---

## 2. Sistem Gereksinimleri

| Bileşen  | Gereksinim            |
| -------- | --------------------- |
| **CPU**  | En az 8 çekirdek      |
| **RAM**  | Minimum 16 GB         |
| **Disk** | En az 300 GB boş alan |

---

## 3. Sunucu Kirala:

---

## 4. Sistem guncelleme ve gerekli paketler:
```bash
sudo apt update && sudo apt -y upgrade
sudo apt install -y curl screen iptables build-essential git wget lz4 jq make gcc nano \
automake autoconf tmux htop nvme-cli pkg-config libssl-dev libleveldb-dev \
tar clang bsdmainutils ncdu unzip ca-certificates net-tools iputils-ping
```

---

## 5. Proje Dosyalarinin Kurulumu

- Tempo’yu kur:
```bash
curl -L https://tempo.xyz/install | bash
source ~/.bashrc
```
- Surumu dogrula:
```bash
tempo --version
```

---

## 6. Node Calistirma

- Imzalama anahtarini uret:
```bash
mkdir -p $HOME/tempo/keys
tempo consensus generate-private-key --output $HOME/tempo/keys/signing.key
```
- Public key’i hesaplayip dogrula:
```bash
tempo consensus calculate-public-key --private-key $HOME/tempo/keys/signing.key
```
- Veri dizinini hazirla:
```bash
mkdir -p $HOME/tempo/data
```
- Guncel snapshot indir:
```bash
tempo download
```
- Screen oturumu baslat:
```bash
screen -S tempo
```
- Node’u calistir:
```bash
tempo node --datadir $HOME/tempo/data \
  --port 30303 \
  --discovery.addr 0.0.0.0 \
  --discovery.port 30303 \
  --consensus.signing-key $HOME/tempo/keys/signing.key \
  --consensus.fee-recipient <validator_wallet_address>
```
- Screen oturumundan ayril:
```bash
Ctrl+a d
```
- Screen oturumuna geri don:
```bash
screen -r tempo
```

---

## 7. Dashboard / Login / Peer ID

1. Web panel bulunmaz; durum takibi loglar uzerinden yapilir.
2. Sync ve blok yuksekligi icin explorer kontrolu:
`https://explorer.tempo.xyz/`

---

## 10. Ek Notlar / Tavsiyeler

1. Validator set yonetimi Tempo ekibi tarafindan izinlidir; validator olmak icin `partners@tempo.xyz` ile iletisim gerekir.
2. Validator aktif sete hemen girmeyebilir; genelde 48-72 saat surebilir.
