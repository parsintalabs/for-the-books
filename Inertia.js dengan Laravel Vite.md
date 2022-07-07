Karena laravel sekarang by default sudah menggunakan [Vite](https://vitejs.dev/), maka kita akan belajar bagaimana mengintegrasikan nya dengan [Inertia.js](https://inertiajs.com/). Dan kita akan mulai dari awal pastinya dari instalasi laravel.

```bash
laravel new vite-inertia
```

Jika sudah, silakan cd ke `vite-inertia` dan lakukan instalasi untuk `inertiajs/inertia-laravel` seperti:

```bash
composer require inertiajs/inertia-laravel
```

Setelah itu, silakan instal semua *node package* yang ada by default dengan menjalankan perintah berikut:

```bash
npm install
```

Jika sudah, sekarang Anda bisa memilih client-side yang Anda inginkan, jika Anda ingin Vue, maka Anda bisa lakukan perintah seperti:

```bash
npm install @inertiajs/inertia @inertiajs/inertia-vue3
```

Kemudian, jika Anda ingin react maka Anda bisa lakukan

```bash
npm install @inertiajs/inertia @inertiajs/inertia-react react@17.0.2 react-dom@17.0.2 @inertiajs/progress
```

Dalam tutorial ini saya akan mencontohkan dengan menggunakan react ya. Jika sudah, sekarang Anda bisa buka file `vite.config.js` yang di dalam *root directory*. Dan silakan *replace* dengan script berikut:

```js
import { defineConfig } from 'vite';
import laravel from 'laravel-vite-plugin';

export default defineConfig({
    plugins: [
        laravel(['resources/js/app.jsx']),
    ],
    resolve: {
        alias: {
            '@': '/resources/js',
        },
    },
});
```

Disini kita mengarahkan ada folder `resources/js/app.jsx`, sehingga Anda harus memastikan bahwa file tersebut ada, harusnya file tersebut ada. Namun tidak berekstensi `.jsx` melainkan `.js` seperti:

```bash
resources/js
├── app.js
└── bootstrap.js
```

Silakan *rename* `app.js` menjadi `app.jsx`. Jika sudah, pada file tersebut harusnya yang ada saat ini hanyalah `import './bootstrap';`, maka sekarang silahkan replace itu dengan

```js
import './bootstrap';
import '../css/app.css';

import React from 'react';
import { render } from 'react-dom';
import { createInertiaApp } from '@inertiajs/inertia-react';
import { InertiaProgress } from '@inertiajs/progress';
import { resolvePageComponent } from 'laravel-vite-plugin/inertia-helpers';

const appName = window.document.getElementsByTagName('title')[0]?.innerText || 'Laravel';

createInertiaApp({
    title: (title) => `${title} - ${appName}`,
    resolve: (name) => resolvePageComponent(`./Pages/${name}.jsx`, import.meta.glob('./Pages/**/*.jsx')),
    setup({ el, App, props }) {
        return render(<App {...props} />, el);
    },
});

InertiaProgress.init({ color: '#4B5563' });
```

Perhatikan, disini kita langsung melakukan impor untuk css nya, sehingga nanti kita tidak perlu memasukkannya pada file blade nya.

Jika sudah, sekarang Anda bisa rename `welcome.blade.php` menjadi `app.blade.php` dan masukkan markup seperti berikut:

```html
<!DOCTYPE html>
<html lang="{{ str_replace('_', '-', app()->getLocale()) }}">
    <head>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <title inertia>{{ config('app.name', 'Laravel') }}</title>
        <link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Nunito:wght@400;600;700&display=swap">
        {{-- @routes --}}
        @viteReactRefresh
        @vite('resources/js/app.jsx')
        @inertiaHead
    </head>
    <body class="font-sans antialiased">
        @inertia
    </body>
</html>
```

Disini saya sengaja beri komentar untuk directive `@routes` hanya karena kita belum install packagenya. Dan Anda tahu, bahwa sebenarnya juga itu pilihan. Jika Anda tidak ingin memakai fungsi `route` pada `jsx` maka jangan install itu. Tetapi jika Anda tetap ingin memakai nya, Anda bisa lakukan instalasi seperti:

```bash
composer require tightenco/ziggy
```

Maka Anda bisa menghilangkan komentar untuk directive itu seperti:

```html
<!-- Before -->
{{-- @routes --}}

<!-- After -->
@routes
```

Sekarang, Anda bisa generasi middleware yang diberikan inertia dengan cara:

```bash
php artisan inertia:middleware
```

Anda bisa melihat file itu di `app/Http/Middleware/HandleInertiaRequests.php`.

Sekarang, Anda bisa buat 1 controller dengan nama `HomeController`

```bash
php artisan make:controller HomeController -i
```

Setelah itu, buat route untuk pergi ke sana, buka `routes/web.php` dan masukkan route nya seperti:

```php
use Illuminate\Support\Facades\Route;

Route::get('/', \App\Http\Controllers\HomeController::class);
```

Buka `HomeController.php` dan buat return inertia seperti:

```php
class HomeController extends Controller
{
    public function __invoke()
    {
        return inertia('Home');
    }
}
```

Sekarang, silakan buat folder `Pages` di dalam `resources/js` dan di dalamnya buat file `Home.jsx`.

```bash
resources/js
├── Pages
│   └── Home.jsx
├── app.jsx
└── bootstrap.js
```

Buka `Home.jsx` dan masukkan script seperti berikut:

```jsx
import React from 'react'

export default function Home() {
  return (
    <div>
        <h1>Lorem ipsum dolor sit amet.</h1>
        <p>Lorem ipsum dolor sit amet.</p>
        <p>Laborum repudiandae quibusdam ipsam in!</p>
    </div>
  )
}
```

Jika sudah, Anda bisa run sekarang aplikasi nya dengan

```bash
php artisan serve
```

Kemudian buka tab baru, dan jalankan vite nya dengan cara

```bash
npm run dev
```

Sekarang Anda bisa buka di browser `http://127.0.0.1:8000`.

Sekarang mari kita coba untuk *passing* data dari controller ke dalam react nya. Buka `HomeController`.

```php
return inertia('Home', [
    'user' => [
        'name' => 'Irsyad A. Panjaitan',
        'username' => 'irsyadadl'
    ],
    'app_name' => config('app.name'),
]);
```

Buka `Home.jsx` dan silakan replace yang tadi dengan ini.

```jsx
import React from 'react';

export default function Home({ user, app_name }) {
    return (
        <div>
            <h1>{app_name}</h1>
            <p>
                My name is {user.name} you can follow me on twitter: @{user.username}
            </p>
        </div>
    );
}
```

Harusnya sekarang sudah jelas bahwa yang tampil adalah

```bash
Laravel

My name is Irsyad A. Panjaitan you can follow me on twitter: @irsyadadl
```

Jika sudah, berarti Anda sudah berhasil menggunakan inertia dengan vite.

Jika Anda ingin melihat bagaimana integrasi dengan Tailwind CSS, Anda bisa lihat itu [disini](https://parsinta.com/articles/laravel-dengan-vitejs-6gne1j). Dan jika Anda ingin source code nya, Anda bisa fork project ini di [github](https://github.com/teamparsinta/vite-inertia).

Semoga artikel ini bermanfaat, see you next time.
