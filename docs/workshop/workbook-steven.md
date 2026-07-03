# Workshop Workbook — [NAMA ANDA]

**Frontend–Backend Integration & Debugging** · CreditApp Demo

> **Peserta:** salin file ini ke branch Anda, lalu isi bagian **Jawaban**.
>
> ```bash
> git checkout -b workshop/nama-anda
> cp docs/workshop/workbook.md.example docs/workshop/workbook-nama-anda.md
> ```
>
> Commit `workbook-<nama>.md` + tes Playwright di PR ke `main`.
>
> **Fasilitator** memakai file terpisah `workbook-fasilitator.md` (kunci jawaban, tidak di repo) — struktur sama, bagian 🔒 sudah terisi.

---

## Sesi 1 — Integration & Network

### Lab 1.1 — Happy path (checklist)

- [V] `[data-component="form-pengajuan"]`
- [V] `[data-component="verifikasi-dokumen"]`
- [V] `[data-component="scoring-loading"]`
- [V] `[data-component="hasil-scoring"]`
- [V] Trace ID: `...`

### Lab 1.2 — Network & Console

**1.2.A** — Ada POST ke `/api/v1/credit-scoring/simulate` saat `VITE_SCORING_MODE=mock`? Mengapa?

**Jawaban:**

```text
Tidak Ada, karena kita hanya melakukan hit api POST mock yang sudah disiapkan untuk menghasilkan logic yang akan di return ke user, jatuhnya kaya pake  function untuk perhitungan bukan fetch service backend asli.
```

**1.2.B** — Field apa di log `[Credit Scoring — API Dummy]`? URL?

**Jawaban:**
![CreditScoring](Credit-Scoring-Dummy.png)

**1.2.C** — Baris keputusan `mock` vs `uat` di `creditScoring.js`?

**Jawaban:**

```
158
```

### Lab 1.3 — Diagram layer

```
[User click "Lanjut scoring otomatis"] → Store State Step:scoring di func Attach VerifikasiDokumen.js →  Panggil Func ScheduleScoring di app.js → Render hasil scoring di HasilScoring.js![alt text](image.png) → [UI render hasil]
```

### Quiz Sesi 1

1. Browser - Frontend - Backend/API - Database
2. Karena menggunakan API Mock dan terkadang API MOCK berbeda dengan service/api asli yang dibuat oleh tim backend -> misal request bodynya beda (field/dtype dll).
3. Mock dan API Beneran -> artinya jika menggunakan mock hanya menghitung dengan mock function yang sudah disiapkan yang bersifat dummy, sedangkan kalo menggunakan CallUatBackend menggunakan api asli.

---

## Sesi 2 — Env & CORS

### Lab 2.1 — Matriks konfigurasi

| #   | Konfigurasi         | Hasil yang diamati | ✓/✗ |
| --- | ------------------- | ------------------ | --- |
| A   | `mock`              |                    |     |
| B   | `uat`, backend mati |                    |     |
| C   | `uat`, URL `:9999`  |                    |     |
| D   | `uat`, path salah   |                    |     |

**2.1.A** — Pesan error skenario B (salin teks persis):

**2.1.B** — Status Network skenario C:

### Lab 2.2 — Debugging checklist (skenario 2.1.C)

| Layer   | Temuan | Fix |
| ------- | ------ | --- |
| UI      |        |     |
| Network |        |     |
| API     |        |     |
| Config  |        |     |
| Fix     |        |     |

### Lab 2.3 — Playwright

- [ ] `tests/workshop/intro/` — TODO peserta selesai, `npm run test:e2e:intro` hijau
- [ ] `02-env-config.spec.js` — hapus `test.skip`, tes lulus
- [ ] `03-network-failure.spec.js` — `route.abort` (jika dikerjakan)

```
(paste output: npm run test:e2e)
```

### Lab 2.4 / Quiz Sesi 2

**2.4.A** — Mengapa browser memeriksa CORS?

**2.4.B** — Header response apa yang dibutuhkan untuk POST JSON?

Quiz 1–3:

---

## Sesi 3 — Trace ID & debugging

### Lab 3.1

- Trace ID di UI: `...`
- `trace_id` di Console: `...`
- Sama? Jika tidak, di layer mana hilang?

**3.1.B** — Cara mencari log di ELK/Grafana:

### Lab 3.3 — Case study (berpasangan)

**Skenario \_\_** — Hipotesis 1 + cara bukti / Hipotesis 2 + cara bukti

### Quiz Sesi 3

1.
2.
3.

---

## Sesi 4 — Trace & pipeline

### Lab 4.1 — Playwright Trace Viewer

- Ada request scoring di tab Network trace? ...
- Step Snapshots: kapan `[data-component="hasil-scoring"]` muncul? ...
- Screenshot (opsional): `docs/workshop/bukti/<nama>/trace-4.1.png`

### Lab 4.2 — Happy path

- [ ] Tes lulus mode `mock`
- [ ] Tanpa `page.waitForTimeout()`
- [ ] Selector `data-component` atau role/label stabil

### Lab 4.3 — Pipeline (konseptual)

1.
2.
3.

---

## Assessment

### Bagian A — Pilihan ganda

1. _a b c d_
2. _a b c d_
3. _a b c d_
4. _a b c d_
5. _a b c d_

### Bagian B — Esai (502 di staging)

Langkah debugging terurut (min. 5):

1.
2.
3.
4.
5.

### Bagian C — Checklist praktik (fasilitator)

- [ ] Happy path manual
- [ ] Minimal 1 skenario error (Lab 2.1)
- [ ] Playwright happy-path lulus
- [ ] Checklist Lab 2.2 terisi
