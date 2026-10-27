# Panduan Coding — Interactive Indonesian UD Dependency Tree

## Project: *Active–Passive Voice Visualizer* (HTML + CSS + JavaScript)

Panduan ini mendampingi [`01_mini_assignment.md`](01_mini_assignment.md). Kita membuat visualisasi *Universal Dependencies* yang **membaca anotasi CoNLL-U**, menggambar *dependency edges*, dan membandingkan *active voice* dengan *passive voice*. Istilah teknis dalam bahasa Inggris dipertahankan agar cocok dengan paper dan dokumentasi UD.

**Alasan memilih HTML/CSS/JavaScript:** hasilnya dapat dibuka pada browser umum tanpa instalasi framework atau library visualization eksternal. **Python digunakan hanya untuk menjalankan local web server**, bukan untuk melakukan *dependency parsing*.

## 0. Hasil yang akan dibuat

Aplikasi contoh mempunyai:

- Dua panel *dependency tree* (*active* dan *passive*) dan pilihan untuk melihat salah satunya.
- Warna yang konsisten untuk *Agent*, *Patient*, dan *Location* (**manual semantic-role overlay**, bukan label UD).
- Garis merah pada `DEPREL` yang berubah fungsi gramatikalnya saat transformasi.
- *Token inspector*: ketika kata diklik, tampil `ID`, `FORM`, `LEMMA`, `UPOS`, `FEATS`, `HEAD`, dan `DEPREL`.
- *Comparison table*: `Siti: nsubj → obl:agent` dan `buku: obj → nsubj:pass`.
- Opsi menampilkan atau menyembunyikan `punct`.

## 1. Prerequisites dan struktur file

Siapkan **browser modern**, **text editor** (misalnya VS Code), dan **Python 3**. Tidak perlu `pip install`.

```text
mini_ud_indonesia/
├── README.md
├── 01_mini_assignment.md
├── 02_panduan_coding.md
├── index.html              # halaman dan kontrol visualisasi
├── style.css               # layout, warna, dan node styling
├── app.js                  # parse CoNLL-U + gambar SVG + interaksi
├── validate_conllu.py      # validator sederhana berbasis Python standard library
└── data/
    └── contoh_kalimat.conllu
```

> Folder ini sudah berisi **working reference implementation**. Untuk pengerjaan mandiri, mahasiswa dapat menelusuri dan memodifikasi kode, atau menulis ulang tahap-tahapnya dari awal.

## 2. Menjalankan aplikasinya

Buka Terminal / Command Prompt, masuk ke folder project:

```bash
cd mini_ud_indonesia
python -m http.server 8000
```

Pada beberapa komputer, perintah Python adalah `python3`:

```bash
python3 -m http.server 8000
```

Di browser, buka **<http://localhost:8000/>**. Pastikan kedua *dependency trees* muncul.

**Penting:** jangan mengandalkan `file:///.../index.html`. Browser sering membatasi pembacaan file `.conllu` menggunakan `fetch()` dari skema `file://`; jalankan *local server* seperti di atas.

## 3. Memahami data CoNLL-U

File `data/contoh_kalimat.conllu` memuat dua kalimat, dipisahkan oleh baris kosong. Setiap *word line* memiliki **10 kolom yang dipisahkan dengan TAB**, bukan spasi:

| Kolom | Nama | Artinya untuk program |
|---:|---|---|
| 1 | `ID` | Nomor *syntactic word* |
| 2 | `FORM` | Kata sebagaimana muncul dalam kalimat |
| 3 | `LEMMA` | Bentuk dasar |
| 4 | `UPOS` | *Universal part-of-speech* |
| 5 | `XPOS` | *Language-specific POS* (opsional) |
| 6 | `FEATS` | *Morphological features*, contoh `Voice=Pass` |
| 7 | `HEAD` | `ID` kata yang menjadi *head*; nilai `0` untuk *root* |
| 8 | `DEPREL` | Relasi dari *head* ke *dependent* |
| 9 | `DEPS` | *Enhanced dependencies* (opsional) |
| 10 | `MISC` | Metadata lain, misalnya `SpaceAfter=No` |

Contoh baris secara konseptual (*TAB* ditulis sebagai pemisah `|` agar mudah dibaca):

```text
2 | dibaca | baca | VERB | _ | Voice=Pass | 0 | root | _ | _
4 | Siti   | Siti | PROPN | _ | _        | 2 | obl:agent | _ | _
```

Artinya: `dibaca` adalah *root* dan `Siti` menjadi *dependent* dari `dibaca` dengan label `obl:agent`.

**Kenapa penting?** Bentuk panah yang kita gambar **berasal dari data**: `HEAD → ID`, bukan hasil tebakan berdasarkan urutan kata.

## 4. Tahap coding A — Parse data menjadi object JavaScript

Buka `app.js`. Fungsi `parseConllu(raw)` membaca file teks dan menghasilkan `sentence.tokens`, yang berisi *object* seperti:

```js
{
  id: 4,
  form: "Siti",
  lemma: "Siti",
  upos: "PROPN",
  feats: "_",
  head: 2,
  deprel: "obl:agent",
  misc: "_"
}
```

Bagian penting dalam parser:

```js
const fields = line.split("\t"); // sepuluh kolom TAB
if (fields.length !== 10) throw new Error("Invalid CoNLL-U");
if (!/^\d+$/.test(fields[0])) continue; // skip multiword tokens/empty nodes
```

**Pemeriksaan:** lihat komentar `# sent_id = aktif` dan `# sent_id = pasif`. Program memakainya untuk memisahkan kedua kalimat.

> *Scope:* contoh ini menggambar **basic dependency tree** memakai `HEAD` dan `DEPREL`. *Enhanced dependencies* pada kolom `DEPS` tidak divisualisasikan. Baris *multiword token* (`1-2`) dan *empty node* (`3.1`) juga dilewati.

## 5. Tahap coding B — Mengubah `HEAD` menjadi *edge*

Sebuah *edge* ditentukan oleh tiga informasi:

```js
const headId = token.head;     // contoh 2
const dependentId = token.id; // contoh 4
const label = token.deprel;   // contoh "obl:agent"
```

Jika *token* memiliki `HEAD=0`, jangan gambar *edge* dari token bernomor 0. Token tersebut adalah *root*; tampilkan sebagai `ROOT`.

Aplikasi memakai **SVG** agar setiap *node* dan *edge* dapat digambar tanpa library eksternal. Fungsi utamanya adalah `renderTree(sentence, highlightChanges, showPunct)`.

Kode inti untuk membuat *edge* berbentuk kurva:

```js
const path = svgElement("path", {
  d: `M${x1} ${baseY - 17} C${x1} ${arcTop}, ${x2} ${arcTop}, ${x2} ${baseY - 17}`,
  class: changed ? "edge changed" : "edge",
  "marker-end": changed ? "url(#arrow-change)" : "url(#arrow)",
});
```

Di sini `x1` adalah posisi kata *head*, `x2` posisi kata *dependent*, dan `arcTop` mengatur ketinggian kurva. Label `DEPREL` digambar di atasnya.

## 6. Tahap coding C — Warna yang membantu analisis

Dalam `app.js`, ada mapping **manual**:

```js
const SEMANTIC_ROLES = {
  aktif: { 1: "agent", 3: "patient", 5: "location" },
  pasif: { 1: "patient", 4: "agent", 6: "location" },
};
```

Warna *Agent* tetap sama pada kedua kalimat walaupun `DEPREL` berubah. Inilah inti visualisasinya: **warna mengikuti peran makna; label panah mengikuti fungsi sintaksis**.

- `Siti` tetap *Agent*, tetapi berubah dari `nsubj` menjadi `obl:agent`.
- `buku/Buku` tetap *Patient*, tetapi berubah dari `obj` menjadi `nsubj:pass`.
- `perpustakaan` tetap *Location* dan berlabel `obl` pada kedua konstruksi.

Catatan metodologis: karena mapping peran di atas ditetapkan manual, jangan menyatakan bahwa parser UD otomatis mengeluarkan *semantic role labels*.

## 7. Tahap coding D — *Token inspector* dan perubahan *Voice*

Klik sebuah node dan amati nilai yang ditampilkan oleh fungsi `inspectToken(...)`.

Cek dua token *root* berikut:

```text
membaca → FORM=membaca, LEMMA=baca, DEPREL=root, FEATS=Voice=Act
 dibaca → FORM=dibaca,   LEMMA=baca, DEPREL=root, FEATS=Voice=Pass
```

Keduanya adalah satu *syntactic word* per *node*. Tidak ada node `di-` yang terpisah dari `dibaca`. Bandingkan dengan preposisi **`di`**, yang muncul sebagai *word* terpisah (`UPOS=ADP`) dan memiliki `DEPREL=case` terhadap `perpustakaan`.

Hubungkan observasi ini dengan **§2.1** (*lexicalism*) dan **§2.5** (*treatment of prepositions and case marking*) dalam paper.

## 8. Tahap coding E — Memodifikasi program (wajib)

Pilih setidaknya **satu eksperimen** dari bagian Task D pada mini assignment. Berikut contoh terarah untuk menambahkan sorotan khusus *case markers*.

**Langkah 1.** Dalam loop pada fungsi `renderTree(...)`, buat variabel:

```js
const isCase = t.deprel === "case";
```

**Langkah 2.** Ketika membuat *label* `DEPREL`, gunakan *CSS class* tambahan berdasarkan `isCase`. Contohnya, tambahkan class `case-label` pada element yang dibuat dengan `svgElement("text", ...)`.

**Langkah 3.** Pada `style.css`, tambahkan aturan:

```css
.edge-label.case-label {
  fill: #16835f;
}
```

**Langkah 4.** Uji bahwa `di` dan `oleh` mendapatkan penanda yang baru, tetapi `nsubj`, `obj`, dan `obl` tidak berubah warna karena aturan tersebut.

**Langkah 5.** Dokumentasikan perubahan dalam `laporan.md`: potongan kode, *before/after screenshot*, dan interpretasi hubungan `case`.

> Eksperimen di atas adalah petunjuk pengembangan, **belum** diimplementasikan secara penuh pada starter. Mahasiswa harus menentukan sendiri bagian kode yang perlu diperbarui dan melakukan pengujian.

## 9. Memvalidasi file CoNLL-U

Jalankan validator sederhana yang disertakan:

```bash
python validate_conllu.py data/contoh_kalimat.conllu
```

Keluaran yang diharapkan:

```text
OK: aktif — 6 syntactic words, root: membaca
OK: pasif — 7 syntactic words, root: dibaca
Validasi dasar selesai: 2 sentence(s) lolos.
```

Pemeriksaan ini memvalidasi **format dasar, jumlah root, HEAD yang merujuk token valid, dan siklus sederhana**. Validator ini **bukan pengganti** validator resmi UD, dan tidak membuktikan kebenaran linguistik suatu anotasi.

## 10. Troubleshooting

| Masalah | Penyebab umum | Solusi |
|---|---|---|
| Halaman terbuka, tetapi tree kosong | Dibuka menggunakan `file://` | Jalankan `python -m http.server 8000` lalu buka `http://localhost:8000/`. |
| Pesan `Format CoNLL-U salah` | Baris data menggunakan spasi, bukan TAB, atau kolom kurang dari 10 | Pastikan **10 kolom** dan gunakan TAB; pertahankan `_` untuk nilai kosong. |
| Ada node tanpa panah | Nilai `HEAD` tidak merujuk ID yang valid | Cek kolom `HEAD` dan jalankan validator. |
| Warna Agent/Patient salah setelah mengubah data | `SEMANTIC_ROLES` masih berdasarkan ID lama | Perbarui mapping sesuai `sent_id` dan `ID` token baru. |
| Label panah bertumpuk | Terlalu banyak token pada ruang SVG terbatas | Gunakan kalimat pendek, tampilkan satu kalimat, atau ubah perhitungan `arcTop` dan ukuran SVG. |
| Port `8000` sudah digunakan | Local server lain masih aktif | Gunakan `python -m http.server 8001`, lalu buka `http://localhost:8001/`. |

## 11. Checklist sebelum mengumpulkan

- [ ] Halaman berjalan melalui `http://localhost:8000/`.
- [ ] Ada *dependency tree* untuk *active* dan *passive*.
- [ ] Arah panah benar: *head → dependent*.
- [ ] Perubahan `nsubj → obl:agent` dan `obj → nsubj:pass` terlihat.
- [ ] `di`/`oleh` muncul sebagai `case`; `dibaca` muncul sebagai satu kata.
- [ ] Kode sudah dimodifikasi minimal satu fitur tambahan.
- [ ] Laporan membedakan *syntactic relations* dari *semantic roles*.
- [ ] Laporan merujuk minimal satu bagian yang tepat dari paper.

## 12. Resources dan batasan

- [Paper utama — de Marneffe et al.](https://nlp.stanford.edu/~manning/papers/USD_LREC14_UD_revision.pdf)
- [UD v2 — CoNLL-U format](https://universaldependencies.org/format.html)
- [UD v2 — `nsubj:pass`](https://universaldependencies.org/u/dep/nsubj-pass.html)
- [UD Indonesia — `obl:agent`](https://universaldependencies.org/id/dep/obl-agent.html)
- [UD Indonesian-GSD](https://universaldependencies.org/treebanks/id_gsd/index.html)

**Batasan implementasi:** visualisasi dibuat untuk pembelajaran dengan dua kalimat pendek; ini **bukan dependency parser otomatis**. Jika Anda memasukkan kalimat baru, anotasi `HEAD`/`DEPREL` harus disediakan secara manual atau dihasilkan dengan alat lain dan diverifikasi. Program tidak otomatis menebak struktur kalimat Bahasa Indonesia.
