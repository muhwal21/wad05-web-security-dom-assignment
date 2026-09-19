# Technical Assignment: Web Security & JavaScript DOM Manipulation

**Mata Kuliah:** Web Application Development  
**Kelas:** WAD05  
**Nama:** MUH. AWALUDDIN  
**NIM:** 25120300003

Tugas ini membahas penggunaan `innerHTML` dan `textContent`, contoh celah Cross-Site Scripting (XSS), serta manipulasi DOM melalui fitur live search. Pada bagian implementasi saya menggunakan contoh **Pokémon Card Marketplace** agar proses pencarian dan filtering lebih mudah dilihat.

## 1. Perbedaan `innerHTML` dan `textContent`

`innerHTML` dan `textContent` sama-sama bisa digunakan untuk mengubah isi elemen HTML, tetapi cara browser membacanya berbeda.

### `innerHTML`

`innerHTML` membaca nilai yang diberikan sebagai HTML. Jadi, jika ada tag HTML di dalam string, tag tersebut akan diproses oleh browser.

```javascript
const productName = document.getElementById("productName");
productName.innerHTML = "<strong>Pikachu Card</strong>";
```

Pada contoh di atas, tulisan **Pikachu Card** akan menjadi tebal karena `<strong>` dianggap sebagai tag HTML.

`innerHTML` berguna jika memang ingin menambahkan struktur HTML. Namun, kita harus berhati-hati jika isi yang dimasukkan berasal dari pengguna karena dapat membuka celah XSS apabila datanya tidak ditangani dengan benar.

### `textContent`

`textContent` hanya menampilkan isi sebagai teks biasa. Tag HTML tidak akan diproses.

```javascript
const productName = document.getElementById("productName");
productName.textContent = "<strong>Pikachu Card</strong>";
```

Hasil yang tampil adalah:

```text
<strong>Pikachu Card</strong>
```

Jadi, jika hanya ingin menampilkan teks, terutama data dari pengguna, API, atau database, `textContent` lebih aman dan lebih sesuai.

### Ringkasnya

| Properti | Cara membaca isi | Penggunaan |
| --- | --- | --- |
| `innerHTML` | Diproses sebagai HTML | Saat memang membutuhkan markup HTML yang aman/terpercaya |
| `textContent` | Diproses sebagai teks biasa | Saat hanya ingin menampilkan teks |

## 2. Contoh Kasus Cross-Site Scripting (XSS)

Misalnya sebuah marketplace kartu Pokémon menyediakan fitur ulasan produk. Pengguna dapat menulis komentar pada kartu yang dijual, lalu komentar tersebut disimpan ke database dan ditampilkan kembali di halaman produk.

Masalah bisa muncul jika komentar pengguna langsung dimasukkan ke halaman menggunakan `innerHTML` tanpa sanitasi atau penanganan yang aman.

```javascript
reviewContainer.innerHTML = userReview;
```

### Bagaimana celahnya terjadi?

Aplikasi menganggap isi ulasan sebagai HTML. Jika ada pengguna yang memasukkan konten HTML berbahaya, browser bisa ikut memprosesnya ketika ulasan tersebut ditampilkan.

### Proses eksploitasi

1. Penyerang mengirim ulasan yang berisi konten berbahaya.
2. Website menyimpan ulasan tersebut ke database tanpa penanganan yang aman.
3. Pengguna lain membuka halaman produk yang sama.
4. Ulasan diambil dari database dan ditampilkan sebagai HTML.
5. Browser pengguna memproses isi tersebut.
6. Script berbahaya dapat berjalan di halaman korban.

Jika konten berbahaya disimpan di database lalu dijalankan kembali ketika pengguna lain membuka halaman, kasus tersebut termasuk **Stored XSS**.

### Dampak

XSS dapat digunakan untuk mengubah isi halaman, menampilkan form login palsu, membaca data yang dapat diakses JavaScript, atau menjalankan aksi melalui sesi pengguna yang sedang aktif.

Cookie yang memakai atribut `HttpOnly` biasanya tidak bisa dibaca langsung oleh JavaScript. Namun, XSS tetap berbahaya karena script masih dapat berinteraksi dengan halaman dan melakukan request menggunakan sesi pengguna dalam kondisi tertentu.

### Pencegahan

Beberapa cara yang dapat dilakukan:

- Gunakan `textContent` jika hanya perlu menampilkan teks.
- Jangan langsung memasukkan input pengguna sebagai HTML.
- Validasi input sesuai kebutuhan.
- Gunakan sanitasi jika aplikasi memang harus menerima HTML.
- Gunakan `createElement()` dan `textContent` saat membuat elemen secara dinamis.

## 3. Live Search Pokémon Card Marketplace

Bagian ketiga dibuat dalam satu file `index.html`. Data kartu disimpan dalam array JavaScript yang berisi nama Pokémon, tipe, rarity, dan harga contoh.

Pencarian dapat dilakukan berdasarkan **nama**, **tipe**, atau **rarity**. Hasil akan berubah langsung saat pengguna mengetik.

### Alur program

1. Data disimpan dalam array `pokemonCards`.
2. Event `input` membaca perubahan pada kolom pencarian.
3. `Array.filter()` mencari data yang sesuai dengan keyword.
4. `renderResults()` menampilkan ulang hasil pencarian.
5. Elemen hasil dibuat menggunakan `document.createElement()`.
6. Teks dimasukkan menggunakan `textContent`.
7. Hasil lama dibersihkan sebelum hasil baru ditampilkan.

Contoh bagian filtering:

```javascript
searchInput.addEventListener("input", (event) => {
  const keyword = event.target.value.toLowerCase().trim();

  const filteredCards = pokemonCards.filter((pokemon) => {
    return (
      pokemon.name.toLowerCase().includes(keyword) ||
      pokemon.type.toLowerCase().includes(keyword) ||
      pokemon.rarity.toLowerCase().includes(keyword)
    );
  });

  renderResults(filteredCards);
});
```

Harga yang digunakan pada project ini hanya data contoh, bukan harga pasar kartu Pokémon sebenarnya.

