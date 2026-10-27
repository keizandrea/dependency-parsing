# Mini Assignment — Dari *membaca* ke *dibaca*

## Memahami *Universal Dependencies* dalam Bahasa Indonesia melalui *active–passive voice*

> **Mata kuliah:** Pengantar NLP / Computational Linguistics / Data Visualization  
> **Level:** Beginner–intermediate  
> **Durasi:** 90 menit untuk analisis + 60–90 menit untuk coding (dapat dibagi menjadi dua sesi)  
> **Bentuk tugas:** Individu atau kelompok berisi 2 orang  
> **Luaran:** *Dependency tree visualization*, perbandingan *active–passive*, dan refleksi singkat

## 1. Latar belakang

*Universal Dependencies (UD)* adalah skema anotasi lintas bahasa untuk menjelaskan hubungan sintaksis antarkata. Dalam *dependency tree*, setiap *token* memiliki satu *head* (kecuali *root*) dan satu *dependency relation* (`DEPREL`) yang menyatakan hubungannya terhadap *head* tersebut.

Paper **de Marneffe et al., “Universal Dependencies: A cross-linguistic typology”** menawarkan dua gagasan penting: *universal grammatical relations* yang dapat dipakai lintas bahasa dan *language-specific subtypes* ketika dibutuhkan. Paper tersebut juga menekankan *lexicalism*, yaitu gagasan bahwa relasi sintaksis terutama menghubungkan kata utuh (*whole words*), bukan memisahkan setiap afiks menjadi node sintaksis.

Dalam Bahasa Indonesia, perubahan dari *active voice* menjadi *passive voice* menarik untuk divisualisasikan. Makna peristiwanya dapat tetap serupa, tetapi *grammatical relations* berubah.

## 2. Pertanyaan utama

> **Bagaimana *Universal Dependencies* merepresentasikan perubahan *grammatical relations* ketika kalimat Bahasa Indonesia diubah dari *active voice* menjadi *passive voice*?**

Gunakan dua kalimat berikut:

- **Active:** “Siti membaca buku di perpustakaan.”
- **Passive:** “Buku dibaca oleh Siti di perpustakaan.”

Contoh ini menggunakan **anotasi ilustratif yang disiapkan untuk pembelajaran**, bukan kalimat yang dikutip langsung dari *UD Indonesian-GSD treebank*.

## 3. Learning outcomes

Setelah menyelesaikan mini assignment, mahasiswa diharapkan mampu:

1. Menjelaskan konsep *head*, *dependent*, *root*, dan *dependency relation*.
2. Membaca kolom-kolom utama *CoNLL-U*: `ID`, `FORM`, `LEMMA`, `UPOS`, `FEATS`, `HEAD`, dan `DEPREL`.
3. Membandingkan `nsubj`, `obj`, `nsubj:pass`, `obl`, `obl:agent`, dan `case` dalam konteks Bahasa Indonesia.
4. Membedakan *syntactic relations* dari *semantic roles* seperti *agent* dan *patient*.
5. Menghubungkan contoh Bahasa Indonesia ke prinsip *lexicalism* dan *treatment of prepositions* dari paper.
6. Membuat *dependency tree visualization* sederhana dari data *CoNLL-U*.

## 4. Bacaan wajib dan terminologi

Baca bagian berikut dari paper:

| Bagian paper | Konsep utama | Fokus pada Bahasa Indonesia |
|---|---|---|
| **§2.1 — The representation builds on lexicalism** | *Lexicalism* / *whole words* | `dibaca` adalah satu kata dalam *dependency tree*, bukan node `di-` + `baca`. |
| **§2.2 — Dependents of clausal predicates** | *Core arguments* dan relasi subjek/objek | Perubahan `nsubj`/`obj` menjadi `nsubj:pass`/`obl:agent`. |
| **§2.5 — Treatment of prepositions and case marking** | *Content-word head* dan `case` | `di` terhubung ke `perpustakaan`; `oleh` terhubung ke `Siti`. |
| **§2.7 — Language-particular relations** | Prinsip *subtypes* | Diskusikan alasan memakai relasi yang lebih khusus. **Catatan:** `obl:agent` adalah *subtype* UD v2 lintas bahasa, bukan relasi yang khusus Bahasa Indonesia. |

**Catatan versi penting:** paper adalah revisi tahun 2015 yang diselaraskan dengan **UD v1**, sedangkan tugas memakai label **UD v2**. Jangan menyalin semua label dalam paper secara literal.

| Label dalam paper (UD v1) | Label dalam tugas (UD v2) | Keterangan |
|---|---|---|
| `dobj` | `obj` | *Direct object* |
| `nsubjpass` | `nsubj:pass` | *Passive nominal subject* |
| `nmod` untuk modifier nominal pada predikat | umumnya `obl` dalam konteks seperti lokasi pada kalimat ini | Perubahan lebih bergantung konteks; **bukan** penggantian satu-ke-satu untuk seluruh `nmod`. |

## 5. Data dan gambaran anotasi

Tugas menggunakan file [`data/contoh_kalimat.conllu`](data/contoh_kalimat.conllu).

### Kalimat A — Active voice

```text
membaca [ROOT; Voice=Act]
├── Siti           [nsubj]
├── buku           [obj]
└── perpustakaan   [obl]
    └── di         [case]
```

### Kalimat B — Passive voice

```text
dibaca [ROOT; Voice=Pass]
├── Buku           [nsubj:pass]
├── Siti           [obl:agent]
│   └── oleh       [case]
└── perpustakaan   [obl]
    └── di         [case]
```

Diagram di atas menyederhanakan visualisasi dengan tidak menampilkan `punct`. File *CoNLL-U* tetap mencakup tanda titik (`.`) dan relasi `punct`.

### Tabel yang diharapkan

| Aspek | Active voice | Passive voice |
|---|---|---|
| *Root verb* | `membaca` | `dibaca` |
| *Morphological feature* | `Voice=Act` | `Voice=Pass` |
| *Agent* (pelaku, sebagai interpretasi makna) | `Siti` → `nsubj` | `Siti` → `obl:agent` |
| *Patient* (yang dikenai tindakan) | `buku` → `obj` | `Buku` → `nsubj:pass` |
| *Location* | `perpustakaan` → `obl` | `perpustakaan` → `obl` |
| *Case marker* | `di` → `case` pada `perpustakaan` | `oleh` → `case` pada `Siti`; `di` → `case` pada `perpustakaan` |

**Perhatikan:** *agent* dan *patient* adalah interpretasi *semantic roles* yang ditambahkan untuk membantu perbandingan. *Basic UD syntax* tidak serta-merta mengkodekan label *semantic roles* tersebut.

## 6. Instruksi pengerjaan

### Task A — Membaca anotasi (±20 menit)

Buka data *CoNLL-U* dan identifikasi *root*, *head*, dan `DEPREL` untuk setiap token. Cocokkan kedua kalimat dengan diagram pada §5. Pada catatan kerja, jelaskan arti minimal empat label yang dipakai dalam kalimat tersebut.

**Checkpoint:** dapat menjawab “Mengapa `HEAD=0` hanya terdapat pada *root*?” dan “Jika `case(perpustakaan, di)`, kata mana yang menjadi *head*?”

### Task B — Membuat visualization (±35 menit)

Bangun atau jalankan program sesuai [`02_panduan_coding.md`](02_panduan_coding.md). Visualisasi harus:

1. Menampilkan dua *dependency trees* dari file *CoNLL-U*.
2. Menunjukkan arah *edge* dari *head* ke *dependent* dan memberi label `DEPREL`.
3. Menyorot perubahan hubungan `Siti` dan `buku` pada *active–passive voice*.
4. Memungkinkan pembaca memeriksa minimal `FORM`, `HEAD`, `DEPREL`, dan `FEATS` pada sebuah *token*.

Program contoh dalam folder ini sudah memuat fitur dasar. **Jika memakai starter tersebut, perubahan kode mahasiswa tetap wajib**, sesuai Task D.

### Task C — Analisis dan interpretasi (±25 menit)

Jawab dalam **200–300 kata**:

1. Mengapa `buku` berubah dari `obj` menjadi `nsubj:pass`?
2. Mengapa `Siti` berubah dari `nsubj` menjadi `obl:agent`, meskipun tetap melakukan tindakan membaca?
3. Mengapa `di` pada “di perpustakaan” menerima node dan label `case`, sedangkan prefiks `di-` pada “dibaca” tidak menjadi node terpisah?
4. Bagian mana dari paper (§2.1, §2.2, §2.5, atau §2.7) yang paling menjelaskan salah satu temuan di atas? Jelaskan dengan contoh.

### Task D — Modifikasi kode (±30 menit)

Pilih **minimal satu** perubahan yang dapat ditunjukkan saat demo:

- Tambahkan penyorotan khusus untuk *case markers* `di` dan `oleh` serta teks penjelasan singkat pada *Token inspector*.
- Tambahkan tombol atau opsi untuk menampilkan/menyembunyikan `UPOS` di bawah tiap node.
- Tambahkan kolom baru pada *comparison table* yang menjelaskan apakah *semantic role* tetap sama walaupun `DEPREL` berubah.
- **Challenge:** tambahkan satu pasangan kalimat lain (konsultasikan anotasinya dengan dokumentasi UD dan catat jika ada ketidakpastian).

## 7. Bonus challenge — `di` vs `di-`

Bandingkan dua konstruksi berikut:

- `di perpustakaan`: `di` adalah *preposition* terpisah → node `ADP` dengan `case`.
- `dibaca`: `di-` adalah prefiks *passive voice* yang tergabung dalam satu bentuk verba → direpresentasikan sebagai bagian dari `FORM=dibaca`, dengan `FEATS=Voice=Pass` untuk contoh ini.

Buat satu catatan kecil dalam visualisasi yang menjawab: **Mengapa dua bentuk yang terlihat mirip ini tidak dianotasi dengan cara yang sama?** Hubungkan dengan *lexicalism* (§2.1) dan *case marking* (§2.5).

## 8. Deliverables

Kumpulkan satu folder atau `.zip` berisi:

1. `index.html`, `app.js`, `style.css`, dan `data/contoh_kalimat.conllu` (atau implementasi alternatif yang dijelaskan).
2. `screenshot.png` yang menunjukkan dua *dependency trees*.
3. `laporan.md` berisi ringkasan perbandingan, jawaban Task C, referensi ke bagian paper, dan dokumentasi perubahan kode di Task D. Anda dapat memakai [`laporan_template.md`](laporan_template.md) sebagai titik awal.
4. `README.md` pendek berisi cara menjalankan program.

## 9. Rubrik (100 poin)

| Kriteria | Bobot |
|---|---:|
| Ketepatan memahami `HEAD`, `DEPREL`, *root*, dan *CoNLL-U* | 25 |
| Kualitas serta keterbacaan *dependency tree visualization* | 25 |
| Analisis perubahan *active–passive* dan pembedaan syntax–semantics | 20 |
| Kaitan eksplisit dengan §2.1/§2.2/§2.5/§2.7 paper | 20 |
| Modifikasi kode, dokumentasi, dan reproduktibilitas | 10 |
| **Total** | **100** |

**Bonus maksimal +10** untuk penjelasan `di` vs `di-` yang akurat dan divisualisasikan dengan jelas.

## 10. Resources

- **Paper utama:** [de Marneffe et al., *Universal Dependencies: A cross-linguistic typology* (revisi, 2015)](https://nlp.stanford.edu/~manning/papers/USD_LREC14_UD_revision.pdf)
- **UD overview:** <https://universaldependencies.org/>
- **CoNLL-U format:** <https://universaldependencies.org/format.html>
- **UD v2 relations:** <https://universaldependencies.org/u/dep/index.html>
- **Indonesian-GSD treebank:** <https://universaldependencies.org/treebanks/id_gsd/index.html>
- **Indonesian `nsubj:pass`:** <https://universaldependencies.org/id/dep/nsubj-pass.html>
- **Indonesian `obl:agent`:** <https://universaldependencies.org/id/dep/obl-agent.html>
- **CoNLL-U Viewer (alternatif untuk pemeriksaan):** <https://universaldependencies.org/conllu_viewer.html>

> **Disclaimer akademik:** dua kalimat dan anotasinya dirancang untuk menjelaskan konsep UD v2, bukan diambil verbatim dari Indonesian-GSD. Ketika memeriksa corpus sungguhan, ikuti anotasi dan konvensi *treebank* yang dirujuk. Jangan menganggap semua kalimat pasif Bahasa Indonesia memiliki struktur identik.
