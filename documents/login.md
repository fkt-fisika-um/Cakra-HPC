# 🚀 Tutorial: Masuk ke HPC **Cakra** lewat **VS Code** di **Windows**

Dokumen ini memandu kamu login SSH ke **HPC Cakra** langsung dari **Visual Studio Code (VS Code)** di Windows menggunakan ekstensi **Remote - SSH**. Disertai dua skenario koneksi: **langsung (IP/VPN kampus)** dan **melewati Cloudflare Access**.

---

## 🧭 Daftar Isi

* [📦 Prasyarat](#prasyarat)
* [🔧 Opsi Koneksi: Langsung vs Cloudflare](#opsi-koneksi-langsung-vs-cloudflare)
* [🧰 Langkah 1: Pasang VS Code & Ekstensi](#langkah-1-pasang-vs-code--ekstensi)
* [🔑 Langkah 2: Cek OpenSSH & Buat SSH Key](#langkah-2-cek-openssh--buat-ssh-key)
* [📤 Langkah 3: Kirim Public Key ke Cakra](#langkah-3-kirim-public-key-ke-cakra)
* [📝 Langkah 4: Tambah Host di `~/.ssh/config`](#langkah-4-tambah-host-di-ssshconfig)
* [🖥️ Langkah 5: Tes Koneksi dari Terminal Windows](#langkah-5-tes-koneksi-dari-terminal-windows)
* [🧩 Langkah 6: Connect dari VS Code (Remote - SSH)](#langkah-6-connect-dari-vs-code-remote---ssh)
* [📂 Langkah 7: Buka Folder Kerja di Cakra](#langkah-7-buka-folder-kerja-di-cakra)
* [🐍 Opsional: Pilih Interpreter Python (Remote)](#opsional-pilih-interpreter-python-remote)
* [🛣️ Opsional: Tunneling Port (Jupyter/Code-Server)](#opsional-tunneling-port-jupytercode-server)
* [🩺 Troubleshooting Cepat](#troubleshooting-cepat)
* [📎 Lampiran A: Contoh `config` Windows](#lampiran-a-contoh-config-windows)
* [📎 Lampiran B: Perintah Cepat](#lampiran-b-perintah-cepat)

---

## Prasyarat

* **VS Code** terpasang.
* Ekstensi **Remote - SSH** di VS Code.
* **(Opsional)** Akses **Cloudflare** jika koneksi eksternal lewat domain (mis. `cakra.rfa-iyang.online`).

> Info server Cakra yang umum dipakai:
>
> * **IP langsung/LAN/VPN**: `10.8.7.224`
> * **Domain Cloudflare**: `cakra.rfa-iyang.online`

---
## Langkah 1: Pasang VS Code & Ekstensi
1. Pasang **VS Code** dari situs resmi.
2. Buka VS Code → **Extensions** → cari **Remote - SSH** (penerbit: *Microsoft*) → **Install**.
---

# Login Cakra via SSH memakai Password

## Langkah 2: Konfigurasi SSH
Salin dan Sesuikan isi konfiugrasi berikut ini
```bash
Host cakra
	User username
	HostName 10.8.7.224
  IdentityFile C:\Users\<NamaUser>\.ssh\id_rsa
```

## Langkah 2: Buat SSH Key
```powershell
ssh-keygen -t rsa -b 4096 -C "kuncicakra"
```

Tekan **Enter** terus untuk lokasi default: `C:\Users\<NamaUser>\.ssh\id_rsa` dan **passphrase** (opsional tapi disarankan).

---

## Langkah 3: Kirim Public Key ke Cakra

Minta admin menambahkan **public key** kamu ke server **Cakra**. Ambil isi file public key:

```powershell
cat C:\Users\<NamaUser>\.ssh\id_rsa.pub
```

Kirim baris tersebut ke admin. **Atau** kalau sementara bisa login dengan password dan ingin menambahkan sendiri:

```powershell
# Ganti <user> dan <host> sesuai (mis. <host> = 10.8.7.224 atau cakra.rfa-iyang.online)
# Perintah ini membuat ~/.ssh/authorized_keys di server dan menambahkan public key-mu
Get-Content $env:USERPROFILE\.ssh\id_ed25519.pub | ssh <user>@<host> "umask 077; mkdir -p ~/.ssh; cat >> ~/.ssh/authorized_keys"
```

> Catatan: Pastikan permission di server benar: `chmod 700 ~/.ssh && chmod 600 ~/.ssh/authorized_keys`

---

## Langkah 4: Tambah Host di `~/.ssh/config`

Kita akan membuat alias `hpc-cakra` supaya mudah dipanggil dari VS Code.

1. Buka **Command Palette** di VS Code: `Ctrl+Shift+P` → ketik **Remote-SSH: Open SSH Configuration File...** → pilih `C:\Users\<NamaUser>\.ssh\config`.
2. Tempel salah satu (atau keduanya) konfigurasi di bawah.

### (A) Koneksi Langsung (IP/VPN)

```sshconfig
Host hpc-cakra
  HostName 10.8.7.224
  User <username-anda>
  IdentityFile C:\Users\<NamaUser>\.ssh\id_ed25519
  ServerAliveInterval 60
  ServerAliveCountMax 3
```

### (B) Koneksi via Cloudflare Access

> Pastikan **cloudflared** sudah terpasang, mis. di: `C:\Program Files\cloudflared\cloudflared.exe`.

```sshconfig
Host hpc-cakra
  HostName cakra.rfa-iyang.online
  User <username-anda>
  IdentityFile C:\Users\<NamaUser>\.ssh\id_ed25519
  ProxyCommand "C:\\Program Files\\cloudflared\\cloudflared.exe" access ssh --hostname %h --port %p
  ServerAliveInterval 60
  ServerAliveCountMax 3
```

Simpan file `config`.

---

## Langkah 5: Tes Koneksi dari Terminal Windows

Sebelum masuk lewat VS Code, coba dari PowerShell:

```powershell
ssh hpc-cakra
```

Pada koneksi pertama, akan muncul pertanyaan **fingerprint host** → jawab `yes`.
Jika berhasil, kamu akan melihat shell di **Cakra** (contoh prompt `username@cakra:~$`). Ketik `exit` untuk keluar.

---

## Langkah 6: Connect dari VS Code (Remote - SSH)

1. Di VS Code → **Command Palette** (`Ctrl+Shift+P`).
2. Pilih **Remote-SSH: Connect to Host...** → pilih **`hpc-cakra`**.
3. Pilih jenis OS remote: **Linux**.
4. Tunggu VS Code memasang server komponen di remote (sekali saja). Jika diminta, pilih lokasi `~/.vscode-server` default.

Setelah terhubung, pojok kiri-bawah VS Code akan menampilkan **>< SSH: hpc-cakra**.

---

## Langkah 7: Buka Folder Kerja di Cakra

* VS Code (sudah terhubung ke remote) → **File → Open Folder...**
* Contoh lokasi home di Cakra (sesuaikan tipe user):

  * Mahasiswa: `/media/mahasiswa/<username>`
  * Dosen: `/media/data_dosen/<username>`
  * Pengunjung: `/media/pengunjung/<username>`
* Klik **OK**. Sekarang semua edit/simpan file langsung terjadi di server Cakra.

> Tips: Gunakan **Terminal** di VS Code (remote): *Terminal → New Terminal* → jalankan perintah seperti `squeue`, `python`, `sbatch`, dll.

---

## 🐍 Opsional: Pilih Interpreter Python (Remote)

1. Pastikan ekstensi **Python** di VS Code terpasang (akan meng-install di sisi remote juga saat pertama kali).
2. Buat/aktifkan virtualenv di server (contoh):

   ```bash
   python3 -m venv ~/.queen
   source ~/.queen/bin/activate
   pip install --upgrade pip
   ```
3. Di VS Code (remote), tekan `Ctrl+Shift+P` → **Python: Select Interpreter** → pilih interpreter dari path `~/.queen/bin/python`.

---

## 🛣️ Opsional: Tunneling Port (Jupyter/Code-Server)

* Jalankan Jupyter di Cakra (remote terminal):

  ```bash
  jupyter lab --no-browser --port 8888
  ```
* VS Code biasanya **auto-forward** port. Jika belum:

  * Lihat ikon **Ports** (panel bawah) → **Forward a Port** → masukkan `8888` → gunakan dari browser lokal `http://localhost:8888`.
* Untuk layanan lain (mis. code-server di port 9000), cara sama: forward port 9000.

> Jika muncul error **"Address already in use"**, ganti ke port lain (mis. 8889 / 9001) atau hentikan proses lama.

---

## 🩺 Troubleshooting Cepat

* **`Permission denied (publickey)`**

  * Pastikan `~/.ssh/authorized_keys` di server berisi public key yang benar.
  * Cek permission di server: `chmod 700 ~/.ssh && chmod 600 ~/.ssh/authorized_keys`.
  * Pastikan `IdentityFile` di `config` menunjuk ke key yang benar (`id_ed25519`).
* **Tidak bisa akses IP 10.8.7.224**

  * Cek apakah sedang di jaringan kampus/VPN. Jika di luar, gunakan opsi Cloudflare.
* **Cloudflare error**

  * Pastikan `cloudflared.exe` terpasang dan path pada `ProxyCommand` benar.
  * Pastikan akun/email yang diizinkan admin untuk akses.
* **`REMOTE HOST IDENTIFICATION HAS CHANGED!`**

  * Hapus entry lama di `known_hosts`:

    ```powershell
    ssh-keygen -R 10.8.7.224
    ssh-keygen -R cakra.rfa-iyang.online
    ```
  * Lalu coba koneksi lagi dan terima fingerprint baru.
* **VS Code lama sekali saat pertama connect**

  * Wajar di koneksi pertama (install server). Cek stabilitas internet/VPN.

---

## 📎 Lampiran A: Contoh `config` Windows

```sshconfig
# ====== Cakra via IP langsung (LAN/VPN) ======
Host hpc-cakra-ip
  HostName 10.8.7.224
  User <username-anda>
  IdentityFile C:\Users\<NamaUser>\.ssh\id_ed25519
  ServerAliveInterval 60
  ServerAliveCountMax 3

# ====== Cakra via Cloudflare Access ======
Host hpc-cakra-cf
  HostName cakra.rfa-iyang.online
  User <username-anda>
  IdentityFile C:\Users\<NamaUser>\.ssh\id_ed25519
  ProxyCommand "C:\\Program Files\\cloudflared\\cloudflared.exe" access ssh --hostname %h --port %p
  ServerAliveInterval 60
  ServerAliveCountMax 3
```

---

## 📎 Lampiran B: Perintah Cepat

```powershell
# Tampilkan public key (kirim ke admin)
type $env:USERPROFILE\.ssh\id_ed25519.pub

# Tambah public key ke server (butuh akses password ssh sekali)
Get-Content $env:USERPROFILE\.ssh\id_ed25519.pub | ssh <user>@<host> "umask 077; mkdir -p ~/.ssh; cat >> ~/.ssh/authorized_keys"

# Tes koneksi pakai alias di config
ssh hpc-cakra
```

---

Selesai! Setelah ini kamu bisa ngoding, submit job SLURM, dan memantau job langsung dari VS Code yang sudah terhubung ke **HPC Cakra**. Silakan revisi bagian yang perlu disesuaikan (mis. username, jalur folder home, atau kebijakan akses).

