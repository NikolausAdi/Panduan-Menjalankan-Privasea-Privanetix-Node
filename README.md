# Panduan Menjalankan Privasea Privanetix Node
Persyaratan Minimum Sistem
Sebelum memulai, pastikan VPS atau komputer Anda memenuhi persyaratan berikut:
- Sistem Operasi: Ubuntu (Direkomendasikan)
- Arsitektur CPU: amd64 (x86)
- Penyimpanan: Minimal 100GB ruang kosong
- RAM: 8GB
- Prosesor: 6 core

## **Langkah 1: Instalasi Docker**
Jika Docker belum terinstal di sistem Anda, ikuti langkah berikut:
1. Perbarui sistem dan instal dependensi yang diperlukan:
   ```
   sudo apt update && sudo apt install -y apt-transport-https ca-certificates curl software-properties-common
   ```
2. Tambahkan kunci GPG resmi Docker:
   ```
   curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
   ```
3. Tambahkan repository Docker ke sistem:
   ```
   sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
   ```
4. Perbarui daftar paket dan instal Docker:
   ```
   sudo apt update
   sudo apt install -y docker-ce
   ```
5. Verifikasi instalasi Docker:
   ```
   sudo docker --version
   ```
   Jika output seperti `Docker version 20.xx.xx` muncul, berarti Docker sudah terinstal dengan benar.
6. Jalankan dan aktifkan layanan Docker:
   ```
   sudo systemctl start docker
   sudo systemctl enable docker
   ```

## **Langkah 2: Menjalankan Privanetix Node**
1. Tarik (pull) gambar Docker Privasea:
   ```
   docker pull privasea/acceleration-node-beta:latest
   ```
2. Buat direktori dan masuk ke dalamnya:
   ```
   mkdir -p ~/privasea/config && cd ~/privasea
   ```
3. Buat file keystore baru:
   ```
   docker run --rm -it -v "$HOME/privasea/config:/app/config" privasea/acceleration-node-beta:latest ./node-calc new_keystore
   ```
   Setelah menjalankan perintah ini, Anda akan diminta memasukkan password. Catat `password` dan `alamat node` yang muncul.
4. Pindahkan file keystore yang dibuat:
   ```
   mv $HOME/privasea/config/UTC--* $HOME/privasea/config/wallet_keystore
   ```
   Buka [Privanetix Dashboard](https://deepsea-beta.privasea.ai/privanetixNode) dan hubungkan wallet Anda sebagai penerima reward. Masukkan `alamat node` yang sudah dicatat, atur nama node, serta komisi (misalnya 1%), lalu klik ``Set up my node``.
5. Jalankan node dengan perintah berikut (ganti `ENTER_YOUR_KEYSTORE_PASSWORD` dengan password keystore Anda):
   ```
   KEYSTORE_PASSWORD=ENTER_YOUR_KEYSTORE_PASSWORD && docker run -d --name privanetix-node -v "$HOME/privasea/config:/app/config" -e KEYSTORE_PASSWORD=$KEYSTORE_PASSWORD privasea/acceleration-node-beta:latest
   ```

## **Langkah 3: Memantau Status Node**
Anda bisa mengecek status node menggunakan:
```
docker ps
```
Jika kontainer masih muncul dalam daftar dengan status "Up", berarti node berjalan dengan normal.

## **Langkah 4: Menghentikan Node**
1. Jika ingin menghentikan node, jalankan perintah berikut:
   ```
   docker ps -q --filter "ancestor=privasea/acceleration-node-beta:latest" | xargs --no-run-if-empty docker stop
   ```
2. Periksa apakah node telah berhenti dengan perintah:
   ```
   docker ps | grep privasea/acceleration-node-beta:latest
   ```
   Jika tidak ada output, berarti node telah berhasil dihentikan.


#### Source : 
1. https://github.com/zunxbt/privasea-privanetix-node
2. https://privasea.gitbook.io/user-node-usage-documentation/comprehensive-guide-to-privanetix-node-acceleration-node-and-workheart-node-setup-and-operation/privanetix-node#id-7.-view-the-node-health
3. https://www.privasea.ai/privanetix-node
