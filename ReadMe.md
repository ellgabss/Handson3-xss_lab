Pada file vulnerable.html, terdapat baris kode:

container.innerHTML = html; // INI ADALAH CELAH KEAMANANNYA!
innerHTML memerintahkan browser untuk memperlakukan input pengguna sebagai kode HTML yang harus dijalankan.

Sedangkan pada versi Secure, menggunakan metode:

const bodyDiv = document.createElement('div');
bodyDiv.textContent = c.comment; // AMAN: Mengonversi simbol < menjadi teks biasa
textContent melakukan automatic escaping. Karakter seperti < diubah menjadi &lt; di tingkat browser, sehingga browser tidak akan pernah membacanya sebagai awal dari sebuah tag <script>.

Pertanyaan Refleksi

1. Mengapa `<img onerror>` bisa menjalankan JavaScript?
2. Apa perbedaan antara Stored XSS dan Reflected XSS dari perspektif attacker?
3. Mengapa HttpOnly cookie penting dalam konteks XSS?
4. Jika kamu perlu menampilkan rich text (dengan formatting), bagaimana caranya agar tetap aman?
5. Apa kegunaan Content Security Policy (CSP) header?

Jawab
1. Karena onerror adalah event handler atribut resmi di HTML. Browser didesain untuk menjalankan kode JS di dalam atribut tersebut jika elemen gagal dimuat. Attacker memanfaatkan ini untuk menyisipkan kode tanpa menggunakan tag <script>.
2. - Stored XSS: Payload disimpan secara permanen di server (database). Semua orang yang membuka halaman tersebut akan terkena dampaknya.
- Reflected XSS: Payload tidak disimpan di server, melainkan "dipantulkan" melalui URL (biasanya di query parameter). Korban harus mengklik link khusus yang sudah dimanipulasi attacker. 
3. Karena atribut HttpOnly mematikan akses JavaScript ke cookie tersebut. Jika situs terkena XSS, attacker tetap tidak bisa mencuri session ID melalui document.cookie.
4. Gunakan library sanitasi seperti DOMPurify. Library ini akan membuang tag berbahaya (seperti <script>, onerror, <a> dengan javascript link) dan hanya menyisakan tag aman seperti <b> atau <i>.
5. CSP adalah layer keamanan tambahan (via HTTP Header) yang memberi tahu browser sumber mana saja yang diperbolehkan untuk memuat script. CSP bisa mematikan inline script sehingga serangan XSS tetap gagal meskipun ada celah di kode.