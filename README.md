# Lab OSINT — SpiderFoot & Recon-ng

Lingkungan praktikum OSINT untuk kelas Ethical Hacking. Lab ini berisi dua alat yang sudah siap pakai dalam Docker:

| Alat | Versi | Cara pakai |
|---|---|---|
| **SpiderFoot** | 4.0 | Antarmuka web di `http://127.0.0.1:5001` |
| **Recon-ng** | 5.1.2 | Konsol interaktif, semua modul marketplace sudah terpasang |

Image tersedia untuk **amd64** (Windows/Linux/Mac Intel) dan **arm64** (Mac M1–M4). Docker otomatis memilih yang sesuai dengan laptop kamu.

---

## ⚠️ Aturan lab — baca dulu

- Gunakan alat ini **hanya** pada target yang ditentukan pengajar atau milik kamu sendiri.
- Mengumpulkan data orang lain atau organisasi tanpa izin bisa melanggar **UU ITE** dan **UU Pelindungan Data Pribadi**.
- Jangan pernah membagikan API key milikmu ke orang lain atau mengunggahnya ke GitHub.

---

## 1. Persiapan (lakukan sebelum hari kelas)

1. Install **Docker Desktop**: https://www.docker.com/products/docker-desktop/
   - **Windows:** aktifkan WSL2 saat diminta installer, dan pastikan *Virtualization* menyala di BIOS.
2. Pastikan Docker berjalan. Buka terminal (PowerShell di Windows), lalu jalankan:
   ```bash
   docker version
   ```
   Bagian *Server* harus muncul tanpa error.
3. Unduh repo ini, lewat tombol **Code → Download ZIP** atau dengan:
   ```bash
   git clone https://github.com/andirifqialnur/osint-lab.git
   cd osint-lab
   ```
4. Unduh image-nya. Lakukan ini di rumah, karena WiFi kelas biasanya lambat:
   ```bash
   docker compose pull
   ```

---

## 2. Menjalankan SpiderFoot

```bash
docker compose up -d spiderfoot
```

Buka browser ke **http://127.0.0.1:5001**, lalu klik **New Scan**.

> Untuk latihan, pilih mode **Passive** atau **Footprint**. Mode **All** bisa berjalan berjam-jam.

Menghentikan SpiderFoot:
```bash
docker compose stop spiderfoot
```

---

## 3. Menjalankan Recon-ng

```bash
docker compose run --rm recon-ng
```

Contoh latihan pertama:
```
[recon-ng][default] > workspaces create latihan1
[recon-ng][latihan1] > modules load recon/domains-hosts/hackertarget
[recon-ng][latihan1][hackertarget] > options set SOURCE example.com
[recon-ng][latihan1][hackertarget] > run
[recon-ng][latihan1][hackertarget] > show hosts
[recon-ng][latihan1][hackertarget] > exit
```

Menambahkan API key, misalnya Shodan:
```
[recon-ng][default] > keys add shodan_api ISI_API_KEY_KAMU
[recon-ng][default] > keys list
```

Workspace, hasil, dan API key tetap tersimpan walaupun container ditutup.

---

## 4. Perintah berguna

| Kebutuhan | Perintah |
|---|---|
| Update image ke versi terbaru | `docker compose pull` |
| Lihat log SpiderFoot | `docker compose logs -f spiderfoot` |
| Matikan semua | `docker compose down` |
| **Reset total** (hapus semua hasil scan & API key) | `docker compose down -v` |

---

## 5. Troubleshooting

| Masalah | Solusi |
|---|---|
| `Cannot connect to the Docker daemon` | Docker Desktop belum dibuka. Jalankan dulu dan tunggu sampai statusnya *running*. |
| `port is already allocated` (5001) | Ada aplikasi lain di port 5001. Ubah `127.0.0.1:5001:5001` menjadi `127.0.0.1:5002:5001` di `docker-compose.yml`, lalu buka port 5002. |
| `denied` / `unauthorized` saat pull | Lapor ke pengajar, karena package mungkin belum dibuat publik. |
| Modul Recon-ng: *key not set* | Modul itu butuh API key. Tambahkan dengan `keys add`, atau pakai modul lain. |

---

## Untuk pengajar

Image dibangun otomatis oleh GitHub Actions (`.github/workflows/build-images.yml`) setiap kali isi folder `spiderfoot/` atau `recon-ng/` berubah. Build juga bisa dijalankan manual dari tab **Actions → Build & publish lab images → Run workflow**.

Struktur repo:
```
osint-lab/
├── docker-compose.yml          # dipakai peserta
├── spiderfoot/Dockerfile
├── recon-ng/Dockerfile
├── recon-ng/install-modules.rc # memasang semua modul marketplace saat build
└── .github/workflows/build-images.yml
```

Catatan teknis:
- Kedua image memakai `python:3.11-slim` supaya semua dependensi tersedia untuk amd64 dan arm64.
- SpiderFoot 4.0 mengunci `pyyaml<6`, yang gagal di-build di Python modern. Di sini dinaikkan ke pyyaml 6. SpiderFoot hanya memakai `yaml.safe_load`, jadi aman.
- Dockerfile resmi Recon-ng tidak menyalin kode programnya ke image, jadi di sini dibuat Dockerfile sendiri. `censys` dikunci `<2.2` supaya semua modul censys aktif.
