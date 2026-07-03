# Workshop: Frontend–Backend Integration & Debugging

Materi praktik untuk workshop **Frontend–Backend Integration & Debugging Mastery**, menggunakan proyek **creditappdemo** sebagai studi kasus.

## File

| File | Untuk siapa | Isi |
|------|-------------|-----|
| [`playwright-intro.md`](./playwright-intro.md) | Peserta | **Mulai di sini** — 3 latihan Playwright dasar sebelum use case nyata |
| [`frontend-backend-integration-practice.md`](./frontend-backend-integration-practice.md) | Peserta | Lab, quiz, dan skenario debugging langkah demi langkah |
| [`answer-key.md`](./answer-key.md) | Fasilitator | Kunci jawaban & petunjuk debrief |

## Prasyarat peserta

```bash
cd creditappdemo
npm install
npm run dev
```

Buka http://localhost:5173 — alur demo: **Formulir → Verifikasi dokumen → Scoring → Hasil**.

## Tes otomatis

### Intro Playwright (45–60 menit, sebelum lab integrasi)

```bash
npm run test:e2e:intro    # hanya latihan dasar di tests/workshop/intro/
```

Panduan: [`playwright-intro.md`](./playwright-intro.md).

### Use case nyata (Sesi 2 & 4)

```bash
npm install
npx playwright install chromium
npm run test:e2e          # intro + use case (01–03)
npm run test:e2e:ui       # mode interaktif
npm run test:e2e:trace    # dengan trace untuk debugging
```

| Folder / file | Isi |
|---------------|-----|
| [`tests/workshop/intro/`](../../tests/workshop/intro/) | Latihan 1–3: page load, form, navigasi langkah |
| [`tests/workshop/01–03`](../../tests/workshop/) | Happy path, env config, network failure |

## Mapping ke slide workshop

| Sesi slide | Topik | Lab di practice doc |
|------------|-------|---------------------|
| 1 | Integration Reality & Network Fundamentals | Lab 1.1 – 1.3 |
| 2 | CORS, Env Config, Reverse Proxy | Lab 2.1 – 2.4 + Playwright config tests |
| 3 | Cross-Layer Debugging (Correlation ID & Logs) | Lab 3.1 – 3.3 |
| 4 | Systematic Debugging + Pipeline | Lab 4.1 – 4.2 + case study |
