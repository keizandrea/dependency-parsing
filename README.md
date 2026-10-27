# Mini UD Indonesia — Active–Passive Visualizer

Paket mini assignment berbahasa Indonesia untuk mempelajari **Universal Dependencies (UD)** melalui visualisasi perbandingan dua kalimat:

- **Active:** Siti membaca buku di perpustakaan.
- **Passive:** Buku dibaca oleh Siti di perpustakaan.

## Mulai cepat

```bash
cd mini_ud_indonesia
python -m http.server 8000
```

Buka <http://localhost:8000/> di browser. Gunakan `python3` jika perintah `python` tidak tersedia.

Tidak perlu menginstal JavaScript framework atau package tambahan. Python hanya digunakan sebagai *local server*.

## Dokumen utama

1. [`01_mini_assignment.md`](01_mini_assignment.md): latar belakang, tujuan, soal, tahapan, deliverables, dan rubrik.
2. [`02_panduan_coding.md`](02_panduan_coding.md): langkah coding dan pengujian.

## Struktur

```text
mini_ud_indonesia/
├── README.md
├── 01_mini_assignment.md
├── 02_panduan_coding.md
├── index.html
├── style.css
├── app.js
├── validate_conllu.py
├── laporan_template.md
├── preview.png
└── data/
    └── contoh_kalimat.conllu
```

Template laporan: [`laporan_template.md`](laporan_template.md).

Contoh visualisasi: [`preview.png`](preview.png).

## Cek anotasi

```bash
python validate_conllu.py data/contoh_kalimat.conllu
```

## Catatan metodologis

Dataset contoh adalah **anotasi ilustratif** untuk pembelajaran, bukan cuplikan verbatim dari Indonesian-GSD. *Agent*, *Patient*, dan *Location* pada kode adalah **manual semantic-role overlay** untuk membantu perbandingan. `HEAD` dan `DEPREL` adalah anotasi *basic UD syntax*. Referensi paper menggunakan terminologi UD v1, sedangkan data/code memakai label UD v2.

Paper: <https://nlp.stanford.edu/~manning/papers/USD_LREC14_UD_revision.pdf>  
UD Indonesian-GSD: <https://universaldependencies.org/treebanks/id_gsd/index.html>
