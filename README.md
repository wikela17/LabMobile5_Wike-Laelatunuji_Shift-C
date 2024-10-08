1. Proses Registrasi dan Login
   Registrasi
  •	Pengguna mengisi formulir registrasi yang terdiri dari nama, email, dan password. Formulir ini menggunakan widget TextFormField untuk masing-masing input, dengan validasi       untuk memastikan data yang diinput sesuai.
  •	Setelah pengguna menekan tombol registrasi, input akan divalidasi terlebih dahulu.

    Mengirim Data Registrasi ke API:
   • Setelah input valid, data (nama, email, password) dikirim ke server melalui REST API.
   •	Fungsi _submit() mengumpulkan data dari form dan menggunakan RegistrasiBloc.registrasi() untuk mengirim permintaan HTTP POST ke endpoint API yang sesuai.

RegistrasiBloc.registrasi(
nama: _namaTextboxController.text, 
email: _emailTextboxController.text, 
password: _passwordTextboxController.text
).then((value) {
// Tampilkan pesan berhasil
}, onError: (error) {
// Tampilkan pesan gagal
});

