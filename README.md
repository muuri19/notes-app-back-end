# Kriteria Proyek
Seperti yang sudah Anda ketahui, kita akan membangun RESTful API untuk aplikasi catatan sederhana. Aplikasi ini berfungsi untuk menyimpan (create), melihat (read), mengubah (update), dan menghapus (delete) catatan. Fungsionalitas ini dikenal sebagai operasi CRUD.

Dari segi Front-End (client), kami telah membuat aplikasi webnya. Kami juga telah men-deploy aplikasi tersebut sehingga Anda dapat mengaksesnya melalui tautan ini http://notesapp-v1.dicodingacademy.com/.

Saat Anda mengaksesnya, aplikasi tersebut belum bisa digunakan. Anda tidak bisa melihat dan menambahkan catatan apa pun. Namun, percayalah! Aplikasi tersebut akan berfungsi dengan baik tatkala Anda sudah membuat RESTful API sesuai dengan kriteria yang dibutuhkan. Lantas, apa saja kriterianya?

## Kriteria 1: Web Server Dapat Menyimpan Catatan
riteria pertama adalah web server dapat menyimpan catatan yang ditambahkan melalui aplikasi web. Tenang, untuk memenuhi kriteria ini, Anda tak perlu menggunakan database. Cukup simpan pada memory server dalam bentuk array JavaScript.

Berikut struktur dari objek catatan yang perlu disimpan oleh server:
```
{
 id: string,
 title: string,
 createdAt: string,
 updatedAt: string,
 tags: array of string,
 body: string,
},
```

Berikut contoh data nyatanya:
```
{
 id: 'notes-V1StGXR8_Z5jdHi6B-myT',
 title: 'Sejarah JavaScript',
 createdAt: '2020-12-23T23:00:09.686Z',
 updatedAt: '2020-12-23T23:00:09.686Z',
 tags: ['NodeJS', 'JavaScript'],
 body: 'JavaScript pertama kali dikembangkan oleh Brendan Eich dari Netscape di bawah nama Mocha, yang nantinya namanya diganti menjadi LiveScript, dan akhirnya menjadi JavaScript. Navigator sebelumnya telah mendukung Java untuk lebih bisa dimanfaatkan para pemrogram yang non-Java.',
},
```

Agar web server dapat menyimpan catatan melalui aplikasi client, web server harus menyediakan route dengan path `‘/notes’` dan method `POST`. 

Dalam menyimpan atau menambahkan notes, client akan mengirimkan permintaan ke path dan method tersebut dengan membawa data JSON berikut pada request body:

```
{
 "title": "Judul Catatan",
 "tags": ["Tag 1", "Tag 2"],
 "body": "Konten catatan"
}
```

Untuk properti `id`, `createdAt`, dan `updatedAt` harus diolah di sisi server, jadi client tidak akan mengirimkan itu. Server harus memastikan properti `id` selalu unik.

Jika permintaan client berhasil dilakukan, respons dari server harus memiliki status code 201 (created) dan mengembalikan data dalam bentuk JSON dengan format berikut:

```
{
  "status": "success",
  "message": "Catatan berhasil ditambahkan",
  "data": {
    "noteId": "V09YExygSUYogwWJ"
  }
}
```

Nilai dari properti `noteId` diambil dari properti `id` yang dibuat secara unik. 

Bila permintaan gagal dilakukan, berikan status code 500 dan kembalikan dengan data JSON dengan format berikut:

```
{
  "status": "error",
  "message": "Catatan gagal untuk ditambahkan"
}
```

## Kriteria 2: Web Server Dapat Menampilkan Catatan
Kriteria selanjutnya adalah web server dapat menampilkan catatan. Kriteria ini mengharuskan web server untuk mengirimkan seluruh atau secara spesifik data notes yang disimpan.

Ketika client melakukan permintaan pada path `‘/notes’` dengan method `‘GET’`, server harus mengembalikan status code **200** (ok) serta seluruh data notes dalam bentuk array menggunakan JSON. Contohnya seperti ini:

```
{
  "status": "success",
  "data": {
    "notes": [
      {
        "id":"notes-V1StGXR8_Z5jdHi6B-myT",
        "title":"Catatan 1",
        "createdAt":"2020-12-23T23:00:09.686Z",
        "updatedAt":"2020-12-23T23:00:09.686Z",
        "tags":[
          "Tag 1",
          "Tag 2"
        ],
        "body":"Isi dari catatan 1"
      },
      {
        "id":"notes-V1StGXR8_98apmLk3mm1",
        "title":"Catatan 2",
        "createdAt":"2020-12-23T23:00:09.686Z",
        "updatedAt":"2020-12-23T23:00:09.686Z",
        "tags":[
          "Tag 1",
          "Tag 2"
        ],
        "body":"Isi dari catatan 2"
      }
    ]
  }
}
```

Jika belum ada catatan satu pun pada array, server bisa mengembalikan data notes dengan nilai array kosong seperti ini:

```{
  "status": "success",
  "data": {
    "notes": []
  }
}
```

Selain itu, client juga bisa melakukan permintaan untuk mendapatkan catatan secara spesifik menggunakan id melalui path `‘/notes/{id}’` dengan method ‘GET’. Server harus mengembalikan status code 200 (ok) serta nilai satu objek catatan dalam bentuk JSON seperti berikut:

```{
  "status": "success",
  "data": {
    "note": {
      "id":"notes-V1StGXR8_Z5jdHi6B-myT",
      "title":"Catatan 1",
      "createdAt":"2020-12-23T23:00:09.686Z",
      "updatedAt":"2020-12-23T23:00:09.686Z",
      "tags":[
        "Tag 1",
        "Tag 2"
      ],
      "body":"Isi dari catatan 1"
    }
  }
}
```

Bila client melampirkan `id` catatan yang tidak ditemukan, server harus merespons dengan status code **404** dan data dalam bentuk JSON seperti ini:

```{
  "status": "fail",
  "message": "Catatan tidak ditemukan"
}
```

## Kriteria 3: Web Server Dapat Mengubah Catatan
Kriteria ketiga adalah web server harus dapat mengubah catatan. Perubahan yang dimaksud bisa berupa judul, isi, ataupun tag catatan. Ketika client meminta perubahan catatan, ia akan membuat permintaan ke path `‘/notes/{id}’`, menggunakan method `‘PUT’`, serta membawa data JSON pada body request yang merupakan data catatan terbaru.

```{
  "title":"Judul Catatan Revisi",
  "tags":[
    "Tag 1",
    "Tag 2"
  ],
  "body":"Konten catatan"
}
```

Jika perubahan data berhasil dilakukan, server harus menanggapi dengan status code **200 (ok)** dan membawa data JSON objek berikut pada body respons.

```{
  "status": "success",
  "message": "Catatan berhasil diperbaharui"
}
```
Perubahan data catatan harus disimpan ke catatan yang sesuai dengan `id` yang digunakan pada path parameter. Bila id catatan tidak ditemukan, server harus merespons dengan status code **404** (not found) dan data JSON seperti ini:

```{
  "status": "fail",
  "message": "Gagal memperbarui catatan. Id catatan tidak ditemukan"
}
```

## Kriteria 4: Web Server Dapat Menghapus Catatan
Kriteria terakhir adalah web server harus bisa menghapus catatan. Untuk menghapus catatan, client akan membuat permintaan pada path `‘/notes/{id}`’ dengan method `‘DELETE’`. Ketika permintaan tersebut berhasil, server harus mengembalikan status code **200** (ok) serta data JSON berikut:

```{
  "status": "success",
  "message": "Catatan berhasil dihapus"
}
```

Catatan yang dihapus harus sesuai dengan id catatan yang digunakan client pada path parameter. Bila id catatan tidak ditemukan, server harus mengembalikan respons dengan status code **404** dan membawa data JSON berikut:

```{
  "status": "fail",
  "message": "Catatan gagal dihapus. Id catatan tidak ditemukan"
}
```