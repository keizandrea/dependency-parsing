# Laporan Mini Assignment — Universal Dependencies Bahasa Indonesia

**Nama / Kelompok:** Kelompok 11  (Keiza dan Almer) 
**Kelas:** NLP Probation Lab KC  
**Tanggal:** 27 Oktober 2022

## A. Ringkasan hasil

Dependency tree yang telah dibuat menjelaskan mengenai bagaimana setiap kata berhubungan di dalam suatu kalimat dan bagaimana kalimat aktif dan pasif akan memiliki struktur sintaks yang berbeda. Namun, walaupun ada perubahan kalimat dari aktif ke pasif akan memiliki semantic role yang tetap sama walaupun sintaksisnya berubah. Visualiasi juga memperlihatkan bahwa di pada kata seperti "di perpustakaan" dan perfiks di- pada dibaca dinotasi berbeda walaupun memiliki bentuk yang mirip.

## B. Perbandingan *active–passive voice*
| Semantic role (interpretasi manual) | Active: token + `DEPREL` | Passive: token + `DEPREL` | Apa yang berubah? |
|---|---|---|---|
| Agent | Siti -> nsubj | Siti -> obl:agent |Siti yang awalnya merupakan nsubj atau subjek utama pada kalimat aktif berubah menjadi obl:agent atau oblique di kalimat pasif. Dari hal ini, Siti tidak lagi menjadi subjek yang wajib ada di dalam kalimat, jika Siti didrop dari kalimat pasif tersebut, kalimat akan tetap memiliki struktur yang baik (contoh: "Buku dibaca" akan masih menjadi kalimat yang utuh). Dalam hal ini, meskipun relasi sintaksisnya berubah, secara makna, Siti tetap merupakan agent atau pelaku yang membaca.  |
| Patient | buku -> obj | Buku -> nsubj: pass | Pada kalimat aktif, buku merupakan objek yang dibaca oleh Siti. Di sini, buku tidak harus ditambahkan dalam kalimat karena tanpa kata "buku", sudah terbentuk kalimat utuh "Siti membaca". Pada kalimat pasif, buku berubah menjadi subjek inti yang mana harus ada di dalam kalimat untuk membentuk kalimat yang utuh.  |
| Location | perpustakaan -> obl | perpustakaan -> obl |Kata perpusatakaan tidak berubah di kedua kalimat, hal ini karena perpustakaan sendiri merupakan kata pelengkap yang menjelaskan lokasi.  |

## C. Jawaban analisis (200–300 kata total)

**C1.** Mengapa `buku` berubah dari `obj` menjadi `nsubj:pass`?

Pada kalimat aktif pada contoh, buku merupakan objek langsung atau obj, yang mana dia merupakan argumen inti yang berhubungan dengan kata membaca (verba). Ketika kalimat diubah menjadi pasif, verba akan berubah sebagai subjek inti, sehingga kini merupakan unsur wajib. Tanpa buku, kalimat akan terasa tidak lengkap. Label yang dipakai adalah nsubj:pass, bukan nsubj, karena buku sendiri adalah subjek yang bukan pelaku tindakan. Pada paper, bagian 2.2 menyebut bahwa label perlu menanadai subjek pada non-canonical voice atau paasid. Secara makna di real-life, buku tetap patient, yang berubah hanya relasi sintaksisnya.

**C2.** Mengapa `Siti` berubah dari `nsubj` menjadi `obl:agent`?

Pada kalimat aktif, Siti merupakan nsubj karena dia merupakan subjek inti yang mana kalimat tidak akan utuh jika Siti dihilangkan (Siti berperan sebagai pelaku di dalam kalimat aktif). Sedangkan pada kalimat pasif, Siti berubah menjadi obl: agent( (oblique), yang mana Siti berubah menjadi tidak wajib untuk ditambahkan karena dia hanya merupakan pelengkap, di mana jika Siti tidak ditambahkan pada kalimat pasif, kalimat akan tetap utuh. Keterangan "agent" itu sendiri artinya Siti merupakan pelaku di kalimat tersebut.

**C3.** Mengapa `di` di “di perpustakaan” menjadi token `case`, tetapi `di-` pada “dibaca” tidak menjadi node sintaksis tersendiri?

Kata "di" pada kalimat "di perpustakaan" merupakan token case karena kalimat tersebut berperan sebagai keterangan tempat dan adalah kata terpisah berupa perposisi dan ADP, sehingga mendapatkan node sendiri, sedangkan "di" pada "dibaca" merupakan prefiks yang menempel pad verba. Berdasarkan prinsip lexicalism yang  ada pada paper subsection 2.1, relasi sintaksis hanya menghubungkan kata utuh sehingga dibaca tetap satu node. 

**C4.** Bagaimana temuan Anda terhubung dengan minimal satu bagian paper (§2.1, §2.2, §2.5, §2.7)?

Temuan yang terhubung dengan paper menurut penemuan kami ada dua bagian, yaitu 2.1 dan 2.7. Pada 2.1, yaiut mengenai lexicalism, relasi sintaksis hanya menghubungkan kata utuh, sehingga dibaca tetap daam satu node, dan prefiks di- merupakan kata terpisah yang mendapat node case. Lalu pad 2.7, label spesifik ditulis sebagai subtupe seperti nsubj:pass dari nsubj dan obl:agent dari obl, sehingga tetap kompatibel lintas bahasa namun ditandai pasif.

## D. Modifikasi kode

**Fitur yang ditambahkan:** ...

**File dan bagian yang diubah:** ...

**Hasil pengujian:** ...

**Screenshot before/after:** (sertakan gambar dengan Markdown jika tersedia)

## E. Refleksi

Apa satu hal tentang Bahasa Indonesia atau UD yang baru Anda pahami setelah melihat visualisasi?

Dari percobaan dan visualisasi, kami menyadari bahwa membaca dan dibaca memiliki kata dasar yang sama (baca) dan sama-sama menjadi root. Bahkan, head dari Siti dan buku tidak berubah, yaitu tetap verba utama (HEAD = 2) pada kedua kalimat. Yang berubah hanyalah label relasinya: Siti dari nsubj menjadi obl:agent, dan buku dari obj menjadi nsubj:pass. Padahal, pelaku dan benda yang dibaca tetap sama. Ini menunjukkan bahwa UD memisahkan struktur sintaksis dari makna.

## F. Referensi

- de Marneffe et al. (2015 revision). [*Universal Dependencies: A cross-linguistic typology*](https://nlp.stanford.edu/~manning/papers/USD_LREC14_UD_revision.pdf).
- [Universal Dependencies — Indonesian-GSD](https://universaldependencies.org/treebanks/id_gsd/index.html).
- Dokumentasi UD v2 lain yang Anda gunakan: ...
