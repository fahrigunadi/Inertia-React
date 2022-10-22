# CRUD LARAVEL INERTIA & REACT

## Apa itu Inertia.js ?
Inertia.js merupakan adapter yang menghubungkan antara backend dan frontend tanpa perlu menggunakan Rest API.
<br>
<br>
<br>
<br>
<br>
## Instalasi dan konfigurasi

### Langkah 1 - Membuat Project Laravel dengan Composer

```shell
composer create-project --prefer-dist laravel/laravel:9.3.0 inertia-react
```

### Langkah 2 - Menjalankan Project Laravel

```shell
cd inertia-react
```
```shell
php artisan serve
```

### Langkah 3 - Konfigurasi Database

Sekarang lakukan konfigurasi koneksi database-nya di dalam Laravel. Silahkan buka file .env, kemudian cari kode berikut ini.
```php
DB_DATABASE=laravel
DB_USERNAME=root
DB_PASSWORD=
```
Dan ubahlah menjadi seperti berikut ini.

```php
DB_DATABASE=db_inertia_react
DB_USERNAME=root
DB_PASSWORD=
```
Silahkan buka http://localhost/phpmyadmin, kemudian buat database baru dengan nama db_inertia_react.

### Langkah 4 - Membuat Model dan Migration

Buat Model dan Migration untuk table posts.
```shell
php artisan make:model Post -m
```

Silahkan buka file database/migrations/2022_09_14_014412_create_posts_table.php, kemudian pada function up silahkan ubah menjadi seperti berikut ini.

```php
public function up()
{
    Schema::create('posts', function (Blueprint $table) {
        $table->id();
        $table->string('title');
        $table->text('content');
        $table->timestamps();
    });
}
```

Silahkan buka file app/Models/Post.php, kemudian ubah kode-nya menjadi seperti berikut ini.

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Post extends Model
{
    use HasFactory;

    /**
     * fillable
     *
     * @var array
     */
    protected $guarded = [];
}
```
### Langkah 5 - Menjalankan Migrate

```shell
php artisan migrate
```
membuat project Laravel baru beserta melakukan konfigurasi database telah selesai.
<br>
<br>
<br>
<br>
## Installasi & Konfigurasi Inertia.js "server-side"

### Langkah 1 - Installasi Inertia Server Side di Laravel

```shell
composer require inertiajs/inertia-laravel:0.6.3
```

### Langkah 2 - Publish dan Konfigurasi Middleware

```shell
php artisan inertia:middleware
```

Silahkan buka file app/Http/Kernel.php, kemudian tambahkan kode berikut ini di dalam $middlewareGroups dan di dalam array web.

```php
protected $middlewareGroups = [
    'web' => [

        //...

        \App\Http\Middleware\HandleInertiaRequests::class,
    ],

    'api' => [

        //...

    ]
]
```

### Langkah 3 - Konfigurasi Shared Data

Sekarang silahkan buka file app/Http/Middleware/handleInertiaRequest.php, kemudian cari kode berikut ini :

```php
public function share(Request $request): array
{
    return array_merge(parent::share($request), [
        //
    ]);
}
```

kemudian ubahlah menjadi seperti berikut ini.

```php
public function share(Request $request): array
{
    return array_merge(parent::share($request), [
        //session
        'session' => [
            'success'   => fn () => $request->session()->get('success'),
        ],
    ]);
}
```

### Langkah 4 - Konfigurasi Root Template

Silahkan buat file baru dengan nama app.blade.php di dalam folder resources/views, kemudian masukkan kode berikut ini di dalamnya.

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0" />
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.2/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-EVSTQN3/azprG1Anm3QDgpJLIm9Nao0Yz1ztcQTwFspd3yD65VohhpuuCOmLASjC" crossorigin="anonymous">
    @viteReactRefresh
    @vite('resources/js/app.jsx')
    @inertiaHead
    <style>
      body {
        background-color: lightgray
      }
    </style>
  </head>
  <body>

      @inertia
      
  </body>
</html>
```

konfigurasi Inertia.js di Laravel secara server side telah selesai.
<br>
<br>
<br>

## Installasi & Konfigurasi Inertia.js "client-side"

### Langkah 1 - Installasi Inertia Client Side di Laravel

jalankan perintah berikut ini di dalam terminal/CMD

```shell
npm install
```

```shell
npm install @inertiajs/inertia@0.11.0
```

```shell
npm install @inertiajs/inertia-react@0.8.1
```

```shell
npm install @inertiajs/progress@0.2.7
```

```shell
npm install react@17.0.2 react-dom@17.0.2
```

### Langkah 2 - Konfigurasi dan Inisialisasi App

rename file resources/js/app.js menjadi resources/js/app.jsx, kemudian masukkan kode berikut ini di dalamnya.

```jsx
import React from 'react';
import { render } from 'react-dom';
import { createInertiaApp } from '@inertiajs/inertia-react';
import { InertiaProgress } from '@inertiajs/progress';
import { resolvePageComponent } from 'laravel-vite-plugin/inertia-helpers';

createInertiaApp({
    resolve: (name) => resolvePageComponent(`./Pages/${name}.jsx`, import.meta.glob('./Pages/**/*.jsx')),
    setup({ el, App, props }) {
        return render(<App {...props} />, el);
    },
});

InertiaProgress.init();
```

### Langkah 3 - Konfigurasi Vite

 buka file vite.config.js, kemudian ubah kode-nya menjadi seperti berikut ini.
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
 
 ### Langkah 4 - Melakukan Proses Compiling (Vite)

```shell
npm run dev
```

installasi dan konfigurasi Inertia.js telah selesai.
<br>
<br>
<br>
<br>

## Menampilkan Data (Read)

### Langkah 1 - Membuat Controller Post

```shell
php artisan make:controller PostController
```
kita akan mendapatkan 1 file controller baru yang berada di dalam folder app/Http/Controllers/PostController.php.

Silahkan buka file tersebut dan ubah kode-nya menjadi seperti berikut ini :

```php
<?php

namespace App\Http\Controllers;

use App\Models\Post;
use Illuminate\Http\Request;

class PostController extends Controller
{    
    /**
     * index
     *
     * @return void
     */
    public function index()
    {
        //get all posts
        $posts = Post::latest()->get();

        //return view
        return inertia('Posts/Index', [
            'posts' => $posts
        ]);
    }
}
```

### Langkah 2 - Menambahkan Route

buka file routes/web.php, kemudian ubah kode-nya menjadi seperti berikut ini.

```php
<?php

use Illuminate\Support\Facades\Route;


Route::get('/', function () {
    return view('welcome');
});

/**
 * route resource /posts
 */
Route::resource('/posts', \App\Http\Controllers\PostController::class);
```

### Langkah 3 - Membuat Layout Template

Silahkan buat folder baru dengan nama Layouts di dalam folder resources/js. Kemudian di dalam folder Layouts buatlah file baru dengan nama Default.jsx dan masukkan kode berikut ini di dalamnya.

```jsx
//import React
import React from 'react';

//import Link
import { Link } from '@inertiajs/inertia-react';

function Layout({ children }) {

    return (
        <>
            <header>
                <nav className="navbar navbar-expand-md navbar-dark fixed-top bg-dark">
                    <div className="container">
                        <Link className="navbar-brand" href="/">LARAVEL + INERTIA.JS</Link>
                        <button className="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarCollapse" aria-controls="navbarCollapse" aria-expanded="false" aria-label="Toggle navigation">
                            <span className="navbar-toggler-icon"></span>
                        </button>
                        <div className="collapse navbar-collapse" id="navbarCollapse">
                            <ul className="navbar-nav me-auto mb-2 mb-md-0">
                                <li className="nav-item">
                                    <Link className="nav-link" href="/posts/">POSTS</Link>
                                </li>
                            </ul>
                            <form className="d-flex">
                                <input className="form-control me-2" type="search" placeholder="Search" aria-label="Search"/>
                                <button className="btn btn-success" type="submit">Search</button>
                            </form>
                        </div>
                    </div>
                </nav>
            </header>

            <main className="container mt-5">
                { children }
            </main>
        </>
    )

}

export default Layout

```

### Langkah 4 - Menampilkan Data Posts

Silahkan buat folder baru dengan nama Posts di dalam folder resources/js/Pages, kemudian di dalam folder Posts silahkan buat file baru dengan nama Index.jsx dn masukkan kode berikut ini di dalamnya.

```jsx
//import React
import React from 'react';

//import layout
import Layout from '../../Layouts/Default';

//import Link
import { Link } from '@inertiajs/inertia-react';

export default function PostIndex({ posts, session }) {

  return (
    <Layout>
        <div style={{ marginTop: '100px' }}>
            
            <Link href="/posts/create" className="btn btn-success btn-md mb-3">TAMBAH POST</Link>
            
            {session.success && (
                <div className="alert alert-success border-0 shadow-sm rounded-3">
                    {session.success}
                </div>
            )}

            <div className="card border-0 rounded shadow-sm">
                <div className="card-body">
                    <table className="table table-bordered table-striped">
                        <thead>
                            <tr>
                                <th scope="col">TITLE</th>
                                <th scope="col">CONTENT</th>
                                <th scope="col">ACTIONS</th>
                            </tr>
                        </thead>
                        <tbody>
                        { posts.map((post, index) => (
                            <tr key={ index }>
                                <td>{ post.title }</td>
                                <td>{ post.content }</td>
                                <td className="text-center">
                                    
                                </td>
                            </tr>
                        )) }
                        </tbody>
                    </table>
                </div>
            </div>
        </div>
    </Layout>
  )
}
```

### Langkah 5 - Ujia Coba menampilkan Data
buka di http://localhost:8000/posts, pastikan berhasil.
<br>
<br>
<br>
<br>
##  Insert Data ke Database

### Langkah 1 - Menambahkan Method Create dan Store

Silahkan buka file app/Http/Controllers/PostController.php, kemudian ubah kode-nya menjadi seperti berikut ini.
```php
<?php

namespace App\Http\Controllers;

use App\Models\Post;
use Illuminate\Http\Request;

class PostController extends Controller
{    
    /**
     * index
     *
     * @return void
     */
    public function index()
    {
        //get all posts
        $posts = Post::latest()->get();

        //return view
        return inertia('Posts/Index', [
            'posts' => $posts
        ]);
    }
    
    /**
     * create
     *
     * @return void
     */
    public function create()
    {
        return inertia('Posts/Create');
    }
    
    /**
     * store
     *
     * @param  mixed $request
     * @return void
     */
    public function store(Request $request)
    {
        //set validation
        $request->validate([
            'title'   => 'required',
            'content' => 'required',
        ]);

        //create post
        Post::create([
            'title'     => $request->title,
            'content'   => $request->content
        ]);

        //redirect
        return redirect()->route('posts.index')->with('success', 'Data Berhasil Disimpan!');
    }
}
```

### Langkah 2 - Membuat View Tambah Data
Silahkan buat file baru dengan nama Create.jsx di dalam folder resources/js/Pages/Posts, kemudian masukkan kode berikut ini di dalamnya.

```jsx
//import hook useState from react
import React, { useState } from 'react';

//import layout
import Layout from '../../Layouts/Default';

//import inertia adapter
import { Inertia } from '@inertiajs/inertia';

export default function CreatePost({ errors }) {

    //define state
    const [title, setTitle] = useState('');
    const [content, setContent] = useState('');

    //function "storePost"
    const storePost = async (e) => {
        e.preventDefault();
        
        Inertia.post('/posts', {
            title: title,
            content: content
        });
    }

    return (
        <Layout>
            <div className="row" style={{ marginTop: '100px' }}>
                <div className="col-12">
                    <div className="card border-0 rounded shadow-sm border-top-success">
                        <div className="card-header">
                            <span className="font-weight-bold">TAMBAH POST</span>
                        </div>
                        <div className="card-body">
                            <form onSubmit={storePost}>

                                <div className="mb-3">
                                    <label className="form-label fw-bold">Title</label>
                                    <input type="text" className="form-control" value={title} onChange={(e) => setTitle(e.target.value)} placeholder="Masukkan Judul Post" />
                                </div>
                                {errors.title && (
                                    <div className="alert alert-danger">
                                        {errors.title}
                                    </div>
                                )}

                                <div className="mb-3">
                                    <label className="form-label fw-bold">Content</label>
                                    <textarea className="form-control" value={content} onChange={(e) => setContent(e.target.value)} placeholder="Masukkan Judul Post" rows={4}></textarea>
                                </div>
                                {errors.content && (
                                    <div className="alert alert-danger">
                                        {errors.content}
                                    </div>
                                )}

                                <div>
                                    <button type="submit" className="btn btn-md btn-success me-2"><i className="fa fa-save"></i> SAVE</button>
                                    <button type="reset" className="btn btn-md btn-warning"><i className="fa fa-redo"></i> RESET</button>
                                </div>
                            </form>
                        </div>
                    </div>
                </div>
            </div>
        </Layout>
    )
}
```

### Langkah 3 - Uji Coba Insert Data Post

Silahkan klik button TAMBAH POST yang ada pada halaman posts index, pastikan berhasil
<br>
<br>
<br>
<br>
## Edit dan Update Data ke Database

### Langkah 1 - Menambahkan Method Edit dan Update
tambahkan 2 method baru di dalam controller. buka file app/Http/Controllers/PostController.php, kemudian ubah kode-nya menjadi seperti berikut ini.
```php 
<?php

namespace App\Http\Controllers;

use App\Models\Post;
use Illuminate\Http\Request;

class PostController extends Controller
{    
    /**
     * index
     *
     * @return void
     */
    public function index()
    {
        //get all posts
        $posts = Post::latest()->get();

        //return view
        return inertia('Posts/Index', [
            'posts' => $posts
        ]);
    }
    
    /**
     * create
     *
     * @return void
     */
    public function create()
    {
        return inertia('Posts/Create');
    }
    
    /**
     * store
     *
     * @param  mixed $request
     * @return void
     */
    public function store(Request $request)
    {
        //set validation
        $request->validate([
            'title'   => 'required',
            'content' => 'required',
        ]);

        //create post
        Post::create([
            'title'     => $request->title,
            'content'   => $request->content
        ]);

        //redirect
        return redirect()->route('posts.index')->with('success', 'Data Berhasil Disimpan!');
    }
    
    /**
     * edit
     *
     * @param  mixed $post
     * @return void
     */
    public function edit(Post $post)
    {
        return inertia('Posts/Edit', [
            'post' => $post,
        ]);
    }
    
    /**
     * update
     *
     * @param  mixed $request
     * @param  mixed $post
     * @return void
     */
    public function update(Request $request, Post $post)
    {
        //set validation
        $request->validate([
            'title'   => 'required',
            'content' => 'required',
        ]);

        //update post
        $post->update([
            'title'     => $request->title,
            'content'   => $request->content
        ]);

        //redirect
        return redirect()->route('posts.index')->with('success', 'Data Berhasil Diupdate!');
    }
}
```

### Langkah 2 - Menambahkan Button Edit

Silahkan buka file resources/js/Pages/Posts/Index.jsx, kemudian cari kode berikut ini.

```jsx
{ posts.map((post, index) => (
    <tr key={ index }>
        <td>{ post.title }</td>
        <td>{ post.content }</td>
        <td className="text-center">

        </td>
    </tr>
)) }
```

Dan ubahlah menjadi seperti berikut ini.

```jsx
{ posts.map((post, index) => (
    <tr key={ index }>
        <td>{ post.title }</td>
        <td>{ post.content }</td>
        <td className="text-center">
            <Link href={`/posts/${post.id}/edit`} className="btn btn-sm btn-primary me-2">EDIT</Link>
        </td>
    </tr>
)) }
```

### Langkah 3 - Membuat View Edit Data

buat file baru dengan nama Edit.jsx di dalam folder resources/js/Pages/Posts, kemudian masukkan kode berikut ini di dalamnya.

```jsx
//import hook useState from react
import React, { useState } from 'react';

//import layout
import Layout from '../../Layouts/Default';

//import inertia adapter
import { Inertia } from '@inertiajs/inertia';

export default function EditPost({ errors, post }) {

    //define state
    const [title, setTitle] = useState(post.title);
    const [content, setContent] = useState(post.content);

    //function "updatePost"
    const updatePost = async (e) => {
        e.preventDefault();

        Inertia.put(`/posts/${post.id}`, {
            title: title,
            content: content
        });
    }

    return (
        <Layout>
            <div className="row" style={{ marginTop: '100px' }}>
                <div className="col-12">
                    <div className="card border-0 rounded shadow-sm border-top-success">
                        <div className="card-header">
                            <span className="font-weight-bold">EDIT POST</span>
                        </div>
                        <div className="card-body">
                            <form onSubmit={updatePost}>

                                <div className="mb-3">
                                    <label className="form-label fw-bold">Title</label>
                                    <input type="text" className="form-control" value={title} onChange={(e) => setTitle(e.target.value)} placeholder="Masukkan Judul Post" />
                                </div>
                                {errors.title && (
                                    <div className="alert alert-danger">
                                        {errors.title}
                                    </div>
                                )}

                                <div className="mb-3">
                                    <label className="form-label fw-bold">Content</label>
                                    <textarea className="form-control" value={content} onChange={(e) => setContent(e.target.value)} placeholder="Masukkan Judul Post" rows={4}></textarea>
                                </div>
                                {errors.content && (
                                    <div className="alert alert-danger">
                                        {errors.content}
                                    </div>
                                )}

                                <div>
                                    <button type="submit" className="btn btn-md btn-success me-2"><i className="fa fa-save"></i> UPDATE</button>
                                    <button type="reset" className="btn btn-md btn-warning"><i className="fa fa-redo"></i> RESET</button>
                                </div>
                            </form>
                        </div>
                    </div>
                </div>
            </div>
        </Layout>
    )
}
```

### Langkah 4 - Uji Coba Proses Edit dan Update Data

Silahkan klik button EDIT dan pastikan berhasil
<br>
<br>
<br>
<br>

## Delete Data dari Database

### Langkah 1 - Menambahkan Method Destroy
tambahkan method destroy di dalam controller. buka file app/Http/Controllers/PostController.php, kemudian ubah kode-nya menjadi seperti berikut ini.
```php
<?php

namespace App\Http\Controllers;

use App\Models\Post;
use Illuminate\Http\Request;

class PostController extends Controller
{    
    /**
     * index
     *
     * @return void
     */
    public function index()
    {
        //get all posts
        $posts = Post::latest()->get();

        //return view
        return inertia('Posts/Index', [
            'posts' => $posts
        ]);
    }
    
    /**
     * create
     *
     * @return void
     */
    public function create()
    {
        return inertia('Posts/Create');
    }
    
    /**
     * store
     *
     * @param  mixed $request
     * @return void
     */
    public function store(Request $request)
    {
        //set validation
        $request->validate([
            'title'   => 'required',
            'content' => 'required',
        ]);

        //create post
        Post::create([
            'title'     => $request->title,
            'content'   => $request->content
        ]);

        //redirect
        return redirect()->route('posts.index')->with('success', 'Data Berhasil Disimpan!');
    }
    
    /**
     * edit
     *
     * @param  mixed $post
     * @return void
     */
    public function edit(Post $post)
    {
        return inertia('Posts/Edit', [
            'post' => $post,
        ]);
    }
    
    /**
     * update
     *
     * @param  mixed $request
     * @param  mixed $post
     * @return void
     */
    public function update(Request $request, Post $post)
    {
        //set validation
        $request->validate([
            'title'   => 'required',
            'content' => 'required',
        ]);

        //update post
        $post->update([
            'title'     => $request->title,
            'content'   => $request->content
        ]);

        //redirect
        return redirect()->route('posts.index')->with('success', 'Data Berhasil Diupdate!');
    }
    
    /**
     * destroy
     *
     * @param  mixed $post
     * @return void
     */
    public function destroy(Post $post)
    {
        //delete post
        $post->delete();

        //redirect
        return redirect()->route('posts.index')->with('success', 'Data Berhasil Dihapus!');
    }
}
```

### Langkah 2 - Membuat Proses Delete Data

buka file resources/js/Pages/Posts/Index.jsx, kemudian ubah kode-nya menjadi seperti berikut ini.

```jsx
//import React
import React from 'react';

//import layout
import Layout from '../../Layouts/Default';

//import Link
import { Link } from '@inertiajs/inertia-react';

//import inertia adapter
import { Inertia } from '@inertiajs/inertia';

export default function PostIndex({ posts, session }) {

    //method deletePost
    const deletePost = async (id) => {
        Inertia.delete(`/posts/${id}`);
    }

  return (
    <Layout>
        <div style={{ marginTop: '100px' }}>
            
            <Link href="/posts/create" className="btn btn-success btn-md mb-3">TAMBAH POST</Link>
            
            {session.success && (
                <div className="alert alert-success border-0 shadow-sm rounded-3">
                    {session.success}
                </div>
            )}

            <div className="card border-0 rounded shadow-sm">
                <div className="card-body">
                    <table className="table table-bordered table-striped">
                        <thead>
                            <tr>
                                <th scope="col">TITLE</th>
                                <th scope="col">CONTENT</th>
                                <th scope="col">ACTIONS</th>
                            </tr>
                        </thead>
                        <tbody>
                        { posts.map((post, index) => (
                            <tr key={ index }>
                                <td>{ post.title }</td>
                                <td>{ post.content }</td>
                                <td className="text-center">
                                    <Link href={`/posts/${post.id}/edit`} className="btn btn-sm btn-primary me-2">EDIT</Link>
                                    <button onClick={() => deletePost(post.id)} className="btn btn-sm btn-danger">DELETE</button>
                                </td>
                            </tr>
                        )) }
                        </tbody>
                    </table>
                </div>
            </div>
        </div>
    </Layout>
  )
}
```

### Langkah 3 - Uji Coba Delete Data

klik button delete dan pastikan berhasil

<br>
<br>
<br>


