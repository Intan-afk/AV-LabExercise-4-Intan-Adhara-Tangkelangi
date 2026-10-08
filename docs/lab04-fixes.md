# Lab 04 — Fix Log

Aturan acuan: **constraints go down, sizes go up, parent sets position.**

| Widget | Error / symptom | Rule broken | Fix |
|--------|-----------------|-------------|-----|
| `StoreHeader` | Overflow di kanan pada 320 dp (nama toko + rating sebaris) | `Row` memberi `Column` dan `Text` lebar tak terbatas, tidak ada yang mau mengalah | `Column` dibungkus `Expanded` + `maxLines` + `ellipsis`; rating dipindah ke bawah dalam `Row` + `Flexible` |
| `CategoryBar` | Overflow di kanan (6 chip lebih lebar dari 320 dp) | Anak-anak `Row` meminta lebar total lebih besar dari yang diberikan parent; `Row` tidak bisa scroll | `SingleChildScrollView(scrollDirection: Axis.horizontal)` |
| `PromoStrip` | Overflow di kanan (200 + 16 + 200 + padding > 320 dp) | Child memaksa ukuran sendiri (`width: 200`) padahal parent hanya memberi 320 dp | `Expanded` untuk tiap kartu; lebar dibagi dari yang tersedia |
| `PromoCard` | Ukuran tetap 200×150, teks tanpa batas | Ukuran ditentukan dari dalam (`SizedBox`), bukan oleh parent | `SizedBox(width, height)` dihapus; tinggi dari `IntrinsicHeight` di parent; `maxLines` + `ellipsis` |
| `MenuScreen` (promos) | Crash `RangeError` saat data kosong atau promo < 2 | Kode mengasumsikan data ada (`promos[0]`, `promos[1]`) | `promos.take(2)`; `PromoStrip` hanya dibuat jika tidak kosong |
| `MenuScreen` (body) | Overflow di bawah saat landscape dan keyboard terbuka | `Column` berisi bagian tetap yang lebih tinggi dari ruang tersisa; `Column` tidak scroll | Seluruh body jadi satu `CustomScrollView` dengan sliver |
| `MenuScreen` (list) | 500 item dibangun sekaligus (`ListView(children: [...])`) | List yang panjangnya tidak dikontrol harus malas | `SliverList` dengan `SliverChildBuilderDelegate` |
| `MenuScreen` (breakpoint) | Layout diputuskan dari `MediaQuery` dan `> 600` | Layout harus memutuskan dari constraint yang diberikan parent, bukan dari layar | `LayoutBuilder` dengan `constraints.maxWidth >= 600`; struktur berubah list → grid |
| `MenuScreen` (grid) | `crossAxisCount: 4` tetap; kartu meluap di sel sempit | Jumlah kolom dipaksa, bukan dihitung dari lebar tersedia | `SliverGridDelegateWithMaxCrossAxisExtent(maxCrossAxisExtent: 240)` |
| `MenuCard` | Overflow di bawah pada sel grid | `height: 110` tetap di dalam sel yang tingginya ditentukan grid | Area gambar jadi `Expanded` (ambil sisa tinggi); nama `maxLines: 2`; `ellipsis` |
| `MenuTile` | Overflow di kanan, terutama nama 200 karakter | `Column` + `Spacer` + harga + tombol dalam `Row` tanpa batas lebar | `Column` dibungkus `Expanded` + `maxLines: 2` + `ellipsis`; harga dan "Promo" dipindah ke `Wrap` |
| `CartBar` | Overflow di kanan; tinggi dan lebar tombol dipaksa | Teks tanpa batas bersaing dengan `SizedBox(width: 160)`; `height: 72` memaksa ukuran | Teks `Expanded` + `maxLines: 2` + `ellipsis`; tinggi dan lebar tetap dihapus |
| `CartBar` (bonus) | Tombol bisa berada di bawah gesture bar | Posisi tidak memperhitungkan area aman dari parent | `Material` + `SafeArea` |
| `MenuScreen` (landscape) | Konten tertutup notch di sisi kiri | Posisi tidak memperhitungkan inset kiri | `SafeArea(top: false, bottom: false)` pada body |
| `MenuScreen` (zero items) | Layar kosong tanpa penjelasan | Kode mengasumsikan data ada | `EmptyState` (ikon + pesan + aksi) dengan `key: const Key('empty-state')` |

## Dark mode: semua warna diambil dari `ColorScheme`, tidak ada warna hard-coded, jadi tetap terbaca.
