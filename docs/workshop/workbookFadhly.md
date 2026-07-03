# Workshop Workbook — Fadhly Andriawan Kairupan

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
```
TIDAK ADA
```
```
Karena masih menggunakan data mock, belum melakukan request dan response`
```

**1.2.B** — Field apa di log `[Credit Scoring — API Dummy]`? URL?

**Jawaban:**
![alt text](<1.2.B .png>)

**1.2.C** — Baris keputusan `mock` vs `uat` di `creditScoring.js`?

**Jawaban:**
```
@param {CreditScoringInput} input
 @returns {Promise<CreditScoringResponse>}
export async function scoreCreditApplication(input) {
  if (SCORING_MODE === 'uat') {
    return callUatBackend(input);
  }
  return scoreWithMock(input);
}
```

### Lab 1.3 — Diagram layer

```
[User click "Lanjut scoring otomatis"] → Store di-state "step: scoring" di function attachVerifikasiDokumen file VerifikasiDokumen.js → Memanggil function scheduleScoring di App.js → Render hasil scoring di file HasilScoring.js → [UI render hasil]
```

### Quiz Sesi 1
```
1. Browser -> Frontend -> Back End/API -> Database
2. Karena mock API tidak sesuai/tidak sync dengan API aslinya
3. scoreWithMock menggunakan data mock/data dummy, sedangkan callUatBackend melakukan call ke API Backend menggunakan method fetch dari JavaScript dan datanya bersifat real
```

---

## Sesi 2 — Env & CORS

### Lab 2.1 — Matriks konfigurasi

| # | Konfigurasi | Hasil yang diamati | ✓/✗ |
|---|-------------|-------------------|-----|
| A | `mock` | | |
| B | `uat`, backend mati | | |
| C | `uat`, URL `:9999` | | |
| D | `uat`, path salah | | |

**2.1.A** — Pesan error skenario B (salin teks persis):

**2.1.B** — Status Network skenario C:

### Lab 2.2 — Debugging checklist (skenario 2.1.C)

| Layer | Temuan | Fix |
|-------|--------|-----|
| UI | | |
| Network | | |
| API | | |
| Config | | |
| Fix | | |

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

**Skenario __** — Hipotesis 1 + cara bukti / Hipotesis 2 + cara bukti

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
