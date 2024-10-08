1. Proses Registrasi dan Login
   
   Registrasi
   
  •	Pengguna mengisi formulir registrasi yang terdiri dari nama, email, dan password. Formulir ini menggunakan widget TextFormField untuk masing-masing input, dengan validasi       untuk memastikan data yang diinput sesuai.
  
  •	Setelah pengguna menekan tombol registrasi, input akan divalidasi terlebih dahulu.

    Mengirim Data Registrasi ke API:
   • Setelah input valid, data (nama, email, password) dikirim ke server melalui REST API.
   
   •	Fungsi _submit() mengumpulkan data dari form dan menggunakan RegistrasiBloc.registrasi() untuk mengirim permintaan HTTP POST ke endpoint API yang sesuai.

```
RegistrasiBloc.registrasi(
nama: _namaTextboxController.text, 
email: _emailTextboxController.text, 
password: _passwordTextboxController.text
).then((value) {
// Tampilkan pesan berhasil
}, onError: (error) {
// Tampilkan pesan gagal
});
```
Respons dari Server:

•	Jika registrasi berhasil, server akan mengirim respons sukses, dan aplikasi akan menampilkan dialog sukses. Pengguna akan diarahkan untuk login setelah itu.

•	Jika terjadi kesalahan, seperti email yang sudah terdaftar, dialog peringatan akan muncul.
```
showDialog(
context: context, 
barrierDismissible: false, 
builder: (BuildContext context) => SuccessDialog(
description: "Registrasi berhasil, silahkan login"
)
);
```
Penyelesaian:

Setelah registrasi berhasil atau gagal, status loading dihentikan, dan aplikasi kembali ke keadaan normal.
```
setState(() {
_isLoading = false;
});
```
SS Registrasi:
![form_registrasi](https://github.com/user-attachments/assets/2e8349a2-95bc-4868-9e01-5a8742e26158)
![registrasi pop up sukses](https://github.com/user-attachments/assets/cd3ddfc5-a3a0-4a39-b407-0e8b605dccbf)

Login

- Menginputkan Username dan Password
- Form login berisi dua input field: email dan password.
- Kode ini ditampilkan pada file login_page.dart, yang memproses input dari pengguna.
- Jika email dan password diisi dan valid, tombol login ditekan untuk memulai proses.
- Source code:

```
Widget _buttonLogin() {
return ElevatedButton(
child: const Text("Login"),
onPressed: () {
var validate = _formKey.currentState!.validate();
if (validate) {
if (!_isLoading) _submit();
}
}
);
}

```
Proses Verifikasi
- Setelah menekan tombol login, LoginBloc.login dipanggil untuk mengirim permintaan POST ke endpoint /login.
- Data input dikirim dalam bentuk JSON.
```

void _submit() {
_formKey.currentState!.save();
setState(() {
_isLoading = true;
});
LoginBloc.login(
email: _emailTextboxController.text,
password: _passwordTextboxController.text
).then((value) async {
if (value.code == 200) {
await UserInfo().setToken(value.token.toString());
Navigator.pushReplacement(context,
MaterialPageRoute(builder: (context) => const ProdukPage()));
} else {
showDialog(context: context, barrierDismissible: false, builder: (BuildContext context) => const WarningDialog(description: "Login gagal, silahkan coba lagi"));
}
});
}
```
Notifikasi Berhasil atau Gagal
Jika login berhasil, pengguna diarahkan ke halaman produk. Jika gagal, dialog peringatan muncul.
```
if (value.code == 200) {
await UserInfo().setToken(value.token.toString());
Navigator.pushReplacement(context, MaterialPageRoute(builder: (context) => const ProdukPage()));
} else {
showDialog(context: context, barrierDismissible: false, builder: (BuildContext context) => const WarningDialog(description: "Login gagal, silahkan coba lagi"));
}

```
SS Login:
![form login](https://github.com/user-attachments/assets/264eb03b-c6bb-452e-a33a-3132820d0829)
berhasil login:
![tampilan setelah berhasil login](https://github.com/user-attachments/assets/073eca1c-4642-41ee-8123-3d48caeb692b)

2. Proses tambah data produk

Form Input Data Produk

Menggunakan form dengan input untuk kode_produk, nama_produk, dan harga_produk.
```
Widget _formProduk() {
return Column(
children: [
TextFormField(decoration: InputDecoration(labelText: "Kode Produk"), controller: _kodeProdukController),
TextFormField(decoration: InputDecoration(labelText: "Nama Produk"), controller: _namaProdukController),
TextFormField(decoration: InputDecoration(labelText: "Harga Produk"), controller: _hargaProdukController),
],
);
}

```
Menampilkan Daftar Produk

Data produk diambil menggunakan ProdukBloc.getProduks(), yang mengirim permintaan GET ke endpoint /produk.

```
FutureBuilder<List>(
future: ProdukBloc.getProduks(),
builder: (context, snapshot) {
if (snapshot.hasData) {
return ListView.builder(
itemCount: snapshot.data!.length,
itemBuilder: (context, i) {
return ItemProduk(produk: snapshot.data![i]);
}
);
} else {
return Center(child: CircularProgressIndicator());
}
}
);
```
SS tambah produk:
![tambah produk](https://github.com/user-attachments/assets/3059ab8b-243f-4a01-99e1-04d09bac5de6)
setelah berhasil tambah produk:
![tampilan setelah berhasil tambah produk](https://github.com/user-attachments/assets/38aa9e10-cf95-4415-87e2-bb898c81763e)

3. Proses Tampil Data
Menampilkan Detail Produk

Ketika produk diklik, detail produk ditampilkan. Data ini diambil dari 

```
ProdukBloc.showProduk.
class ProdukDetail extends StatelessWidget {
final Produk produk;
ProdukDetail({required this.produk});

@override
Widget build(BuildContext context) {
return Scaffold(
appBar: AppBar(title: Text(produk.namaProduk!)),
body: Column(children: [Text("Harga: ${produk.hargaProduk}")])
);
}
}
```

Proses Edit 

Edit Produk

Data produk diedit melalui form yang sama dengan tambah produk.

Setelah form disimpan, data dikirim menggunakan method ProdukBloc.updateProduk.

```
ProdukBloc.updateProduk(produk: Produk(
id: produk.id,
kodeProduk: _kodeProdukController.text,
namaProduk: _namaProdukController.text,
hargaProduk: int.parse(_hargaProdukController.text)
));
```

Delete Produk
Data produk dihapus dengan memanggil ProdukBloc.deleteProduk
```
ProdukBloc.deleteProduk(id: produk.id).then((value) {
Navigator.pop(context);
});
```
SS tampil data:
detail:
![detail produk](https://github.com/user-attachments/assets/101a6f09-e8ef-4a33-94f1-e0854343ddf6)

edit:
![ubah produk](https://github.com/user-attachments/assets/e13f1f00-d84f-477c-b00e-1d9ebddd952a)


setelah diedit:
![setelah berhasil edit](https://github.com/user-attachments/assets/99494fe9-2d94-46b0-96a5-a8cf666e400d)

hapus:
![hapus](https://github.com/user-attachments/assets/0c17b1c0-5e10-4e09-b6ee-9b584e637e7e)

setelah hapus:
![setelah hapus](https://github.com/user-attachments/assets/9ccd1b7f-3355-4c71-b164-03e9948faecb)

pop up hapus:
![pop up hapus](https://github.com/user-attachments/assets/ac9211bf-9a5d-4ca4-8166-de81b0aaefbf)

logout

Proses Logout

Menghapus token dari penyimpanan lokal menggunakan SharedPreferences untuk mengakhiri sesi pengguna dan mencegah akses tidak sah. Contoh fungsi logout:
```
Future<void> logout() async {
  final SharedPreferences pref = await SharedPreferences.getInstance();
  pref.clear(); // Menghapus semua data
}
```
ss logout:
![form logout](https://github.com/user-attachments/assets/64c788d7-3c3a-4517-b1e8-46a82cf1d671)

berhasil logout:
![setelah berhasil logout](https://github.com/user-attachments/assets/8fce9310-4ac3-4141-a031-651f2b65e89a)









