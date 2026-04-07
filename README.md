# Ollama Flask Chat API

Backend API sederhana berbasis Flask untuk meneruskan prompt ke Ollama, plus client CLI Python untuk uji kirim prompt.

## Daftar Isi
- [Ringkasan](#ringkasan)
- [Fitur](#fitur)
- [Arsitektur Singkat](#arsitektur-singkat)
- [Prasyarat](#prasyarat)
- [Struktur Proyek](#struktur-proyek)
- [Instalasi](#instalasi)
- [Konfigurasi](#konfigurasi)
- [Menjalankan Aplikasi](#menjalankan-aplikasi)
- [Kontrak API](#kontrak-api)
- [Menjalankan Client](#menjalankan-client)
- [Pengujian Cepat](#pengujian-cepat)
- [Troubleshooting](#troubleshooting)
- [Roadmap Pengembangan](#roadmap-pengembangan)
- [Kontribusi](#kontribusi)
- [Lisensi](#lisensi)

## Ringkasan
Proyek ini menyediakan:
1. Server Flask dengan endpoint health check dan endpoint chat.
2. Integrasi ke Ollama melalui endpoint `generate`.
3. Client CLI interaktif untuk mengirim prompt ke server API.

Use case utama:
- Prototype layanan chat AI lokal.
- Bridge service antara aplikasi eksternal dan Ollama.
- Praktik pemrograman jaringan (HTTP client-server).

## Fitur
- Validasi input JSON pada endpoint `/chat`.
- Timeout request ke Ollama (`60` detik).
- Error handling untuk kegagalan koneksi HTTP.
- Health endpoint (`/health`) untuk observability dasar.
- Konfigurasi berbasis environment variable.

## Arsitektur Singkat
Alur request:
1. Client mengirim `POST /chat` ke server Flask.
2. Flask memvalidasi body JSON dan field `prompt`.
3. Flask meneruskan prompt ke Ollama (`/api/generate`).
4. Flask mengembalikan response ringkas ke client.

Komponen:
- `app.py`: API server Flask.
- `client.py`: client CLI untuk mengetes endpoint chat.

## Prasyarat
- Python `3.10+` (disarankan).
- Ollama terpasang dan berjalan.
- Model Ollama sudah tersedia lokal (contoh: `gemma3:270m`).

Contoh menyiapkan model di Ollama:
```bash
ollama pull gemma3:270m
```

## Struktur Proyek
```text
ollama-test/
|-- app.py
|-- client.py
|-- requirements.txt
`-- README.md
```

## Instalasi
1. Clone repository.
2. (Opsional) Buat virtual environment.
3. Install dependency.

```bash
python -m venv .venv
.venv\Scripts\activate
pip install -r requirements.txt
```

## Konfigurasi
Server membaca konfigurasi dari environment variable (mendukung `.env` via `python-dotenv`).

Buat file `.env` di root proyek:

```env
OLLAMA_URL=http://localhost:11434/api/generate
MODEL_NAME=gemma3:270m
PORT=5004
```

Penjelasan variabel:
- `OLLAMA_URL`: endpoint API Ollama.
- `MODEL_NAME`: nama model yang dipakai server.
- `PORT`: port server Flask.

## Menjalankan Aplikasi
Jalankan server:

```bash
python app.py
```

Secara default server aktif di:
- `http://localhost:5004`

## Kontrak API
### `GET /`
Informasi service dan daftar endpoint.

Contoh response:
```json
{
  "message": "API Chat Ollama aktif",
  "model": "gemma3:270m",
  "endpoints": {
    "health": "GET /health",
    "chat": "POST /chat"
  }
}
```

### `GET /health`
Health check sederhana.

Contoh response:
```json
{
  "status": "ok",
  "service": "flask-api",
  "model": "gemma3:270m"
}
```

### `POST /chat`
Mengirim prompt ke model Ollama.

Request body:
```json
{
  "prompt": "Jelaskan TCP/IP secara singkat"
}
```

Contoh sukses (`200`):
```json
{
  "success": true,
  "model": "gemma3:270m",
  "prompt": "Jelaskan TCP/IP secara singkat",
  "response": "..."
}
```

Contoh error validasi (`400`):
```json
{
  "error": "Field 'prompt' tidak boleh kosong."
}
```

Contoh error koneksi Ollama (`500`):
```json
{
  "success": false,
  "error": "Gagal menghubungi Ollama: ..."
}
```

## Menjalankan Client
File `client.py` adalah client interaktif berbasis terminal.

1. Pastikan `API_URL` di `client.py` mengarah ke endpoint server yang benar.
2. Jalankan client.

```bash
python client.py
```

Catatan:
- Saat ini `client.py` menggunakan URL Cloudflare tunnel hardcoded.
- Untuk pengujian lokal, ubah `API_URL` menjadi `http://localhost:5004/chat`.

## Pengujian Cepat
### Uji endpoint root
```bash
curl http://localhost:5004/
```

### Uji chat endpoint
```bash
curl -X POST http://localhost:5004/chat \
  -H "Content-Type: application/json" \
  -d "{\"prompt\":\"Halo, perkenalkan dirimu\"}"
```

## Troubleshooting
- Error `Gagal menghubungi Ollama`:
  - Pastikan service Ollama berjalan.
  - Verifikasi `OLLAMA_URL` benar.
- Response lama atau timeout:
  - Gunakan model lebih ringan.
  - Cek resource CPU/RAM.
- `client.py` tidak bisa connect:
  - Pastikan `API_URL` sesuai host dan port server aktif.

## Roadmap Pengembangan
- Tambah endpoint streaming (`stream=true`).
- Tambah logging terstruktur.
- Tambah unit test dan integration test.
- Tambah autentikasi API key untuk produksi.
- Tambah Dockerfile + docker-compose.

## Kontribusi
1. Fork repository.
2. Buat branch fitur/perbaikan.
3. Commit dengan pesan yang jelas.
4. Buat Pull Request dengan deskripsi perubahan dan cara tes.

## Lisensi
Belum ditentukan. Tambahkan file `LICENSE` jika proyek akan dipublikasikan.
