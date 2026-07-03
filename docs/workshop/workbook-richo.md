# Workshop Workbook — [Richo Setiawan]

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

- [x] `[data-component="form-pengajuan"]`
- [x] `[data-component="verifikasi-dokumen"]`
- [x] `[data-component="scoring-loading"]`
- [x] `[data-component="hasil-scoring"]`
- [x] Trace ID: `...`

### Lab 1.2 — Network & Console

**1.2.A** — Ada POST ke `/api/v1/credit-scoring/simulate` saat `VITE_SCORING_MODE=mock`? Mengapa?

**Jawaban:**
Tidak ada karena belum ada backend atau service yang di hit API nya karena masih menggunakan data simulasi

**1.2.B** — Field apa di log `[Credit Scoring — API Dummy]`? URL?

**Jawaban:**
Terdapat
URL: http://localhost:8080/api/v1/credit-scoring/simulate
Method: POST
Headers: {Content-Type: 'application/json', Accept: 'application/json'}
Request body: {applicant: {…}, financial: {…}, credit_profile: {…}}applicant: {full_name: 'Budi Santoso', phone_number: '08123456789'}credit_profile: {payment_history: 'baik'}financial: {monthly_income: 8500000, monthly_expense: 0, existing_installment: 2200000, requested_amount: 40000000, requested_tenure: 12, …} Response: {trace_id: 'mock-1783049810944-242gkx', evaluated_at: '2026-07-03T03:36:50.944Z', calculation: {…}, eligibility: {…}, result: {…}, …}
![Jawaban 1.2.B](/docs/workshop/Jawaban-1.2.B.png)


**1.2.C** — Baris keputusan `mock` vs `uat` di `creditScoring.js`?

**Jawaban:**
Pada baris 158 dengan function 

/**
 * Public API untuk dipakai komponen UI.
 * @param {CreditScoringInput} input
 * @returns {Promise<CreditScoringResponse>}
 */
export async function scoreCreditApplication(input) {
  if (SCORING_MODE === 'uat') {
    return callUatBackend(input);
  }
  return scoreWithMock(input);
}


### Lab 1.3 — Diagram layer

```
[User click "Lanjut scoring otomatis"] → ... → ... → ... → [UI render hasil]
```

### Quiz Sesi 1

1. melakukan setState ke step scoring
const lanjut = container.querySelector('#btn-lanjut-scoring');
  if (lanjut) {
    lanjut.addEventListener('click', () => {
      store.setState({ step: 'scoring', scoringPhase: 'loading' });
    });
  }
2. Lanjut ke function schedule scoring
function scheduleScoring() {
    if (scoringTimer) clearTimeout(scoringTimer);
    scoringTimer = setTimeout(() => {
      scoringTimer = null;
      runScoring(store);
    }, 1650);
  }
3. Lalu memanggil score credit application dengan di creditScoring.js
export async function scoreCreditApplication(input) {
  if (SCORING_MODE === 'uat') {
    return callUatBackend(input);
  }
  return scoreWithMock(input);
}
4. Lalu memanggil render hasil scoring
function render() {
    const state = store.getState();

    if (
      state.step === 'scoring' &&
      state.scoringPhase === 'loading' &&
      !(
        previous.step === 'scoring' &&
        previous.scoringPhase === 'loading'
      )
    ) {
      scheduleScoring();
    }
}

---

## Quiz Sesi 1 (10 menit)

1. Sebutkan **4 layer** yang disebut di materi workshop dan contoh masalah masing-masing pada creditappdemo.
Browser, Frontend, Backend, Database
2. Mengapa bug integrasi sering tidak terlihat saat unit test frontend saja?
Karena Mock tidak sesuai dengan real case di production atau tidak sesuai dengan API yang ada di production
3. Apa perbedaan perilaku `scoreWithMock()` dan `callUatBackend()`?
scoreWithMock hanya melakukan perhitungan dengan mengambil state store dan kalkulasi di FE lalu mengembalikan response. Sementara uatBackEnd akan mengirimkan request body kepada backend dengan mengambil data dari store dan set object pada function request dan melakukan pengembalian response yang didapat dari fetching API sesuai dengan url dari file environement.

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
