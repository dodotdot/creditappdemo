# Kunci Jawaban — Fasilitator Workshop
## Frontend–Backend Integration & Debugging (CreditApp Demo)

> **Jangan dibagikan ke peserta sebelum sesi debrief.**

---

## Playwright Intro (latihan dasar)

### Latihan 2 — `02-form-interaction.spec.js`

```javascript
test('TODO peserta: isi nomor telepon', async ({ page }) => {
  await page.goto('/');
  await page.getByLabel('Nomor telepon').fill('08123456789');
  await expect(page.getByLabel('Nomor telepon')).toHaveValue('08123456789');
});
```

Opsional penghasilan:

```javascript
await page.getByLabel('Penghasilan bulanan (Rp)').fill('8500000');
await expect(page.locator('#monthlyIncome')).toHaveValue('8500000');
```

### Latihan 3 — `03-step-navigation.spec.js`

```javascript
test('TODO peserta: klik Periksa pada dokumen pertama', async ({ page }) => {
  await page.goto('/');
  await fillMinimalForm(page);
  await page.getByRole('button', { name: 'Lanjut verifikasi dokumen' }).click();
  await expect(page.locator('[data-component="verifikasi-dokumen"]')).toBeVisible();

  await page.locator('[data-verify]').first().click();
  await expect(page.getByText('Terverifikasi').first()).toBeVisible();
});
```

**Debrief singkat:** Latihan 1 memakai visibility saja; 2 menambah interaksi + assert nilai; 3 menambah klik dan perubahan langkah UI — pola yang sama dipakai di `01-happy-path.spec.js` untuk alur penuh sampai scoring.

---

## Sesi 1

### Lab 1.2

**1.2.A** — Tidak ada request HTTP nyata ke endpoint scoring. Mode `mock` memanggil `scoreWithMock()` yang menghitung di browser; `fetch` tidak dijalankan (hanya URL dicetak ke console untuk ilustrasi).

**1.2.B** — Log mencetak: URL (`http://localhost:8080/api/v1/credit-scoring/simulate`), Method POST, Headers, Request body (JSON aegira snake_case), Response (termasuk `trace_id`, `result`, `_uiNormalized`).

**1.2.C** — `scoreCreditApplication()` baris ~158–162: `if (SCORING_MODE === 'uat')` → `callUatBackend`, else `scoreWithMock`.

### Lab 1.3 (alur referensi)

```
[User click "Lanjut scoring otomatis"]
  → VerifikasiDokumen: store.setState({ step: 'scoring', scoringPhase: 'loading' })
  → App.js render: scheduleScoring() → setTimeout → runScoring(store)
  → HasilScoring.runScoring: scoreCreditApplication(input)
  → creditScoring.js: scoreWithMock / callUatBackend
  → store.setState({ step: 'result', scoreResult, scoringPhase: 'done' })
  → App.js render: renderHasilScoring()
```

### Quiz Sesi 1

1. **Layer contoh:** Browser (CORS, network) · Frontend (state async, error UI) · API (validasi, 4xx/5xx) · Database (konsistensi data — di uat via backend).
2. Unit test frontend tidak menyentuh backend nyata, env deployment, atau CORS browser.
3. `scoreWithMock` = hitung lokal, tidak fetch; `callUatBackend` = POST JSON ke `VITE_BACKEND_BASE_URL` + path, normalisasi response.

---

## Sesi 2

### Lab 2.1

| Skenario | Hasil tipikal |
|----------|----------------|
| A mock | Sukses, source `mock`, trace `mock-...` |
| B uat, backend mati | `Failed to fetch` atau serupa → UI "Scoring gagal diproses" |
| C wrong port 9999 | Network failed / connection refused |
| D wrong path | HTTP 404 (jika backend hidup) → "Backend scoring gagal (404): ..." |

### Lab 2.4

**2.4.A** — Cross-origin request; browser same-origin policy.  
**2.4.B** — `Access-Control-Allow-Origin` (sesuai origin frontend atau `*`), dan untuk preflight POST JSON: `Access-Control-Allow-Methods`, `Access-Control-Allow-Headers` (termasuk `Content-Type`).

### Quiz Sesi 2

1. Vite membaca env saat startup; `import.meta.env` di-bundle pada build/dev start.
2. Tidak fleksibel per environment, risiko salah endpoint production, sulit di-test.
3. Mapping slide → creditapp: Missing env → tanpa `VITE_BACKEND_BASE_URL` fallback localhost; Wrong URL → Lab 2.1.C; Happy path → Lab 1.1; Wrong endpoint → Lab 2.1.D; Simulated failure → `03-network-failure.spec.js`.

---

## Sesi 3

### Lab 3.1

**3.1.A** — Harus sama; UI menampilkan `r.traceId` dari object hasil mock.  
**3.1.B** — Search `trace_id:"mock-..."` di log aggregator; filter time range sekitar `evaluatedAt`.

### Case studies

**A — Loading terus:** Hipotesis: `runScoring` throw sebelum setState; timer tidak jalan; subscriber error. Bukti: breakpoint di `runScoring`, cek Console.

**B — Kol 5:** Hipotesis: income 0 (form kosong), paymentHistory mapping salah, DTI tinggi. Bukti: log payload di console.

**C — 200 tapi error UI:** Hipotesis: body tidak punya `result` / field wajib; normalisasi gagal; schema mismatch. Bukti: Response tab di Network, bandingkan dengan `normalizeScoringResponse`.

### Quiz Sesi 3

1. Satu ID menelusuri request di semua sistem log.  
2. UI → Network → API → Backend log → DB.  
3. `normalizeScoringResponse()` di `scoringContract.js`.

---

## Sesi 4

### Pipeline diskusi (contoh jawaban)

1. PR check untuk mock E2E; uat/integration di staging dengan backend test.  
2. `route.abort` untuk failure simulation; uat needs real backend or contract test mock server.  
3. Tag `@mock` / `@uat`, matrix env di CI, jangan jalankan uat tanpa service dependency.

---

## Assessment Akhir

### Bagian A

1. **b** — Browser  
2. **b** — `.env`  
3. **b** — UI → Network → API → Backend → DB  
4. **b** — Menghubungkan log antar layer  
5. **b** — Network failure  

### Bagian B (rubrik esai)

Nilai jika jawaban mencakup:

1. Reproduksi di staging (langkah UI sama)  
2. Network: URL = `VITE_BACKEND_BASE_URL` + `VITE_SCORING_PATH`, method POST  
3. Cek response body 502 (gateway/upstream)  
4. Verifikasi env staging vs `.env.example`  
5. Cek backend/gateway logs dengan timestamp + trace header jika ada  
6. Eskalasi ke tim infra/backend jika upstream down  

---

## Solusi Playwright (referensi implementasi)

Lihat file di `tests/workshop/` — tes `02-env-config` yang `test.skip` dapat diselesaikan peserta dengan:

```javascript
test('wrong backend URL shows scoring error', async ({ page }) => {
  await completeFlowToScoring(page);
  await expect(page.getByRole('heading', { name: 'Scoring gagal diproses' })).toBeVisible({
    timeout: 15000,
  });
});
```

dengan `webServer` env di `playwright.config.js` untuk skenario error (atau `test.use` + project terpisah).
