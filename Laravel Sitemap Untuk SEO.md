Dalam artikel ini, kita akan belajar bagaimana mengimplementasikan [Laravel Sitemap](https://github.com/spatie/laravel-sitemap) untuk menggenerasi sitemap pada project kita.

Tetapi, bukan berarti tanpa package ini tidak bisa dibuat manual ya, hanya saja package ini mempermudah kita saja.

Yang pertama kali kita akan install terlebih dahulu package nya.

```bash
composer require spatie/laravel-sitemap
```

Nah setelah itu, untuk menggunakan api nya cukup simpel, hanya perlu menjalankan code ini:
```php
use Spatie\Sitemap\SitemapGenerator;

SitemapGenerator::create('https://parsinta.com/')->writeToFile(public_path('sitemap.xml'));
```
Tetapi, jika Anda ingin lebih rapi, maka Anda bisa buat satu `Command` yang kerja nya untuk menjalankan kode itu. Untuk itu, mari kita buat langsung.

```bash
php artisan make:command GenerateSitemap
```
Setelah itu, silahkan buka filenya, pada `app/Console/Commands/GenerateSitemap.php`
```php
use Illuminate\Console\Command;
use Spatie\Sitemap\SitemapGenerator;

class GenerateSitemap extends Command
{
    protected $signature = 'sitemap:generate';
    protected $description = 'Generating the sitemap for the site';
    public function handle()
    {
        SitemapGenerator::create('https://parsinta.com/')->writeToFile(public_path('sitemap.xml'));
        $this->info('The sitemap has been generated');
    }
}
```

Setelah itu, kita bisa trigger dia dari terminal dengan cara
```bash
php artisan sitemap:generate
```
Jika tidak ada yang salah, harusnya sekarang pada folder `public` sudah ada file yang namanya `sitemap.xml` yang isinya kurang lebih seperti ini:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9" xmlns:xhtml="http://www.w3.org/1999/xhtml">
    <url>
        <loc>https://parsinta.com/</loc>
        <lastmod>2022-04-04T23:48:19+07:00</lastmod>
        <changefreq>daily</changefreq>
        <priority>0.8</priority>
    </url>
    <url>
        <loc>https://parsinta.com/series</loc>
        <lastmod>2022-04-04T23:48:19+07:00</lastmod>
        <changefreq>daily</changefreq>
        <priority>0.8</priority>
    </url>
    ...
```
Baik, sekarang mungkin Anda berfikir, jika di production, apa kita harus secara manual menjalankan sitemap generate ini pada terminal. Behind the scene ia, tapi kita tidak akan ngetikkan itu secara manual dong, dan setiap update masak kita harus trigger. Nah dalam case ini, Anda bisa menggunakan yang namanya `scheduler`.

Perhatikan pada directory `app/Console`, pastinya ada file nya `Kernel.php`, nah didalam method `schedule` Anda bisa tambahkan `command` yang kita buat tadi seperti:

```
protected function schedule(Schedule $schedule)
{
    $schedule->command('sitemap:generate')->daily();
}
```
Maka selanjutnya, jika Anda list schedulenya, harusnya sudah pasti ada untuk sitemap ini.

```php
php artisan schedule:list
...
0 0 * * *  php artisan sitemap:generate
```
Selanjutnya, Anda bisa menyuruh [Supervisor](http://supervisord.org?ref=parsinta.com) untuk menjalankan command tersebut.  Dan pastinya, jika menggunakan [Laravel forge](https://forge.laravel.com?ref=parsinta.com) maka untuk menjalankan ini akan sangat mudah.

Semoga artikel ini bermanfaat. dan sampai jumpa di artikel selanjutnya.
