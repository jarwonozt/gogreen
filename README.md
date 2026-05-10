# 🟩 GoGreen — Auto Commit dengan Vite.js

Proyek ini secara otomatis melakukan commit ke GitHub setiap 3 jam menggunakan GitHub Actions, dengan Vite.js sebagai build tool. Tujuannya adalah menjaga GitHub contribution graph tetap hijau.

---

## Daftar Isi

- [Prasyarat](#prasyarat)
- [Struktur Proyek](#struktur-proyek)
- [Deploy ke Hostinger Node.js](#deploy-ke-hostinger-nodejs)
- [Setup Awal](#setup-awal)
- [Cara Kerja](#cara-kerja)
- [Workflow GitHub Actions](#workflow-github-actions)
- [Konfigurasi Jadwal](#konfigurasi-jadwal)
- [Menjalankan Secara Lokal](#menjalankan-secara-lokal)
- [FAQ](#faq)

---

## Prasyarat

Sebelum memulai, pastikan kamu memiliki:

- Akun GitHub
- [Node.js](https://nodejs.org/) versi 18 atau lebih baru
- [npm](https://www.npmjs.com/) versi 8 atau lebih baru
- Git terinstall di komputer

---

## Struktur Proyek

```
gogreen/
├── .github/
│   └── workflows/
│       ├── auto_commit_vite.yml   # Auto commit setiap 3 jam (Vite build)
│       ├── commit_cheat.yml       # Auto commit setiap jam
│       └── commit_now.yml         # Trigger manual commit
├── src/
│   ├── main.js                    # Entry point JavaScript
│   └── style.css                  # Stylesheet utama
├── public/                        # Asset statis (favicon, dll)
├── dist/                          # Output build Vite.js (auto-generated)
├── index.html                     # Entry point HTML
├── package.json                   # Konfigurasi npm & scripts
├── .gitignore                     # File yang diabaikan git
└── LAST_UPDATED                   # Timestamp commit terakhir
```

---

## Setup Awal

### 1. Clone Repository

```bash
git clone https://github.com/jarwonozt/gogreen.git
cd gogreen
```

Ganti `USERNAME` dengan username GitHub kamu.

### 2. Install Dependencies

```bash
npm install
```

### 3. Konfigurasi Email & Username

Buka file [.github/workflows/auto_commit_vite.yml](.github/workflows/auto_commit_vite.yml) dan sesuaikan:

```yaml
git config --local user.email "EMAIL_KAMU@gmail.com"
git config --local user.name "USERNAME_KAMU"
```

### 4. Aktifkan GitHub Actions

Buka repository di GitHub → tab **Actions** → klik **I understand my workflows, go ahead and enable them**.

### 5. Pastikan Actions Punya Izin Write

Di repository GitHub:
1. Buka **Settings** → **Actions** → **General**
2. Scroll ke bawah ke bagian **Workflow permissions**
3. Pilih **Read and write permissions**
4. Klik **Save**

---

## Cara Kerja

Setiap 3 jam, GitHub Actions akan:

```
┌─────────────────────────────────────────────┐
│              GitHub Actions                  │
│                                              │
│  1. Checkout repository                      │
│  2. Setup Node.js 20                         │
│  3. npm install (install Vite.js)            │
│  4. npm run build (build project Vite.js)   │
│  5. Update file LAST_UPDATED (timestamp)    │
│  6. git add + git commit (pesan random)     │
│  7. git push ke branch main                  │
└─────────────────────────────────────────────┘
```

Setiap commit menggunakan pesan acak dari daftar berikut:

| Emoji | Pesan Commit |
|-------|--------------|
| 😂 | chore(bot): 😂 auto commit |
| 😱 | chore(bot): 😱 auto commit |
| 👿 | chore(bot): 👿 auto commit |
| 💩 | chore(bot): 💩 auto commit |
| 🙏 | chore(bot): 🙏 auto commit |
| 🙈 | chore(bot): 🙈 auto commit |
| 🐐 | chore(bot): 🐐 auto commit |
| 🤖 | chore(bot): 🤖 auto commit |
| 🟩 | chore(bot): 🟩 auto commit |
| 👻 | chore(bot): 👻 auto commit |

---

## Workflow GitHub Actions

Proyek ini memiliki 3 workflow:

### `auto_commit_vite.yml` — Auto Commit Vite (Setiap 3 Jam)

- **Trigger:** Jadwal otomatis setiap 3 jam + bisa dijalankan manual
- **Yang dilakukan:** Build Vite.js → update timestamp → commit → push
- **Jadwal:** `0 */3 * * *` (jam 0, 3, 6, 9, 12, 15, 18, 21 UTC)

### `commit_cheat.yml` — Auto Commit (Setiap Jam)

- **Trigger:** Jadwal otomatis setiap jam (jam 1-20 UTC)
- **Yang dilakukan:** Update timestamp → commit → push
- **Jadwal:** `0 1,2,3,...,20 * * *`

### `commit_now.yml` — Commit Manual

- **Trigger:** Manual via GitHub Actions UI
- **Input yang bisa diisi:**
  - `email` — email git config
  - `username` — nama git config
  - `commit` — pesan commit
  - `limit` — jumlah commit yang dibuat

---

## Konfigurasi Jadwal

Jadwal diatur menggunakan sintaks **cron**. Format: `menit jam hari bulan hari-minggu`

| Keinginan | Cron Expression |
|-----------|-----------------|
| Setiap 3 jam | `0 */3 * * *` |
| Setiap jam | `0 * * * *` |
| Setiap 6 jam | `0 */6 * * *` |
| Setiap hari jam 9 pagi UTC | `0 9 * * *` |
| Setiap hari kerja jam 8 pagi | `0 8 * * 1-5` |

Untuk mengubah jadwal, edit baris `cron:` di dalam workflow:

```yaml
schedule:
  - cron: "0 */3 * * *"  # Ubah nilai ini
```

> Referensi: [crontab.guru](https://crontab.guru) untuk membantu menulis ekspresi cron.

---

## Menjalankan Secara Lokal

### Development Server

```bash
npm run dev
```

Buka browser di `http://localhost:5173`

### Build untuk Production

```bash
npm run build
```

Output tersimpan di folder `dist/`.

### Preview Build

```bash
npm run preview
```

### Trigger Commit Manual via GitHub Actions UI

1. Buka repository di GitHub
2. Klik tab **Actions**
3. Pilih workflow **Commit Now**
4. Klik **Run workflow**
5. Isi form input dan klik **Run workflow**

---

## Deploy ke Hostinger Node.js

### Struktur File Tambahan

File [server.js](server.js) berfungsi sebagai entry point untuk Hostinger. File ini menjalankan Express.js yang melayani hasil build Vite.js dari folder `dist/`.

```
gogreen/
├── server.js      ← startup file untuk Hostinger
├── dist/          ← hasil build Vite.js (di-serve oleh server.js)
└── ...
```

---

### Langkah 1 — Upload File ke Hostinger

**Via Git (direkomendasikan):**

1. Login ke **hPanel Hostinger**
2. Buka menu **Website** → pilih domain → **Manage**
3. Buka **Advanced** → **SSH Access** → aktifkan SSH
4. Sambungkan via terminal:

```bash
ssh u123456789@your-server-ip -p 65002
```

5. Masuk ke direktori domain:

```bash
cd ~/domains/namadomain.com/public_html
```

6. Clone repository:

```bash
git clone https://github.com/jarwonozt/gogreen.git .
```

---

### Langkah 2 — Setup Node.js di hPanel

1. Di hPanel, buka **Website** → **Manage** → **Node.js**
2. Isi konfigurasi:

| Field | Nilai |
|-------|-------|
| **Node.js version** | `20.x` (pilih yang tersedia) |
| **Application mode** | `Production` |
| **Application root** | `/domains/namadomain.com/public_html` |
| **Application URL** | `namadomain.com` |
| **Application startup file** | `server.js` |

3. Klik **Create** atau **Save**

---

### Langkah 3 — Install Dependencies & Build

Setelah Node.js app dibuat, klik tombol **Run NPM Install** di hPanel, atau via SSH:

```bash
npm install
npm run build
```

> Pastikan folder `dist/` sudah terbentuk setelah `npm run build`.

---

### Langkah 4 — Jalankan Aplikasi

Di hPanel Node.js, klik tombol **Start** atau **Restart**.

Cek apakah aplikasi berjalan dengan membuka domain di browser.

---

### Variabel Environment (Opsional)

Jika perlu mengatur port atau konfigurasi lain, tambahkan di hPanel:

1. Buka **Node.js** → **Environment Variables**
2. Tambahkan:

| Key | Value |
|-----|-------|
| `PORT` | `3000` |
| `NODE_ENV` | `production` |

---

### Troubleshooting Hostinger

**Aplikasi tidak mau start:**
- Pastikan `server.js` ada di root directory
- Cek log error di hPanel → **Node.js** → **Error logs**
- Pastikan `npm install` sudah dijalankan dan folder `node_modules` ada

**Halaman tidak tampil / 404:**
- Pastikan `npm run build` sudah dijalankan dan folder `dist/` ada
- Cek path di `server.js` sudah benar mengarah ke `dist/`

**Port conflict:**
- Hostinger biasanya assign port otomatis via `process.env.PORT`
- Jangan hardcode port, biarkan `process.env.PORT || 3000`

---

## FAQ

**Q: Mengapa contribution graph tidak berubah?**

A: Pastikan commit menggunakan email yang sama dengan akun GitHub kamu. Cek dan sesuaikan `user.email` di workflow.

**Q: Apakah workflow berjalan tepat waktu?**

A: GitHub Actions dengan trigger `schedule` bisa terlambat hingga 10-30 menit pada saat traffic tinggi. Ini adalah perilaku normal dari GitHub.

**Q: Bisakah jadwal diubah ke timezone lain?**

A: GitHub Actions selalu menggunakan UTC. Konversikan waktu lokal kamu ke UTC sebelum memasukkan ke cron expression. Misalnya WIB (UTC+7): jam 9 pagi WIB = jam 2 pagi UTC (`0 2 * * *`).

**Q: Bagaimana jika tidak ada perubahan file tapi tetap ingin commit?**

A: File `LAST_UPDATED` diperbarui setiap kali workflow berjalan, sehingga selalu ada perubahan yang bisa di-commit.

**Q: Apakah workflow bisa dimatikan sementara?**

A: Ya. Di GitHub → tab **Actions** → pilih workflow → klik tombol **...** → **Disable workflow**.
