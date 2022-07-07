Tepat pada tanggal [9:32 PM · Jun 22, 2022](https://twitter.com/enunomaduro/status/1539617336973574145), tim inti laravel baru saja merilis package yang digunakan untuk merapikan kode php kita.

Package ini juga dilahirkan dari plugin [PHP-CS-Fixer](https://github.com/FriendsOfPHP/PHP-CS-Fixer), yang mana sebelumnya plugin ini biasa digunakan di editor text, ataupun sebagai CLI.

Nah, untuk menggunakan [Laravel pint](https://github.com/laravel/pint) ini, kita bisa dengan install langsung ke project yang kita punya sebagai `--dev` dependency.

```bash
composer require laravel/pint --dev
```

Nah, untuk menjalankannya, Anda bisa pakai langsung panggil dari vendor seperti berikut.
```bash
./vendor/bin/pint
```
Nah, karena saya tidak suka mengetikkan itu, saya akan masukkan dia di alias seperti:
```bash
alias pint='./vendor/bin/pint'
```
Untuk standardnya, `pint` sendiri akan menggunakan `psr12`, namun kita bisa merubahnya dengan membuat membuat `pint.json` pada _root directory_. `your-project/pint.json`
```json
{
    "preset": "psr12"
}
```
Selain itu, kita juga bisa membuat preset nya ke laravel dengan cara
```bash
pint --preset laravel
```
Atau dengan cara `./vendor/bin/pint --preset laravel`, atau bisa dengan cara memasukkannya ke file json tadi.

```json
{
    "preset": "laravel",
    "rules": {
        "braces": false
    }
}
```
Semoga package dan artikel ini bermanfaat ya.
