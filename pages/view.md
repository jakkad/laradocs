# Views

## Blade

Blade offers a set of ready to use directives

### Assets

To access the assets that are stored in the public folder

```html
<img src="{{ URL('img/icon.jpg') }}">

OR
<img src="{{ asset('img/icon.jpg') }}">
```

### Conditions

**If Statement**

```php
@if(5>10)
	..
@elseif(5==10)
	..
@else
	..
@endif
```

**Unless**

```php
@unless (empty($name))
	..
@endunless
```

**Switch:**

```php
@switch($name)
	@case('value1')
		...
		@break
	@default
		..
@endswitch
```

**For:**

```php
@for($i = 0; $i <=10; $i++)
	...

@endfor
```

**Foreach**

```php
@foreach ($names as $name)
	{{$name}}
@endforeach
```

**Forelse**

```php
@forelse ($names as $name)
	...
$empty
	..
@endforelse
```

**While**

```php
{{ $i = 0}}
@while ($i<10)
	...
@endwhile
```

### Utility

```php
@empty
@isset
```

## Master Layout

A common way to build a template is to group the common content in a master layout file and call in each individual page, layouts are stored in a **layout folder** under the main views folder

To create fillable areas inside the layout we use

```php
...
@yield('content');
...
```

In the View file we call it using extends and then place the dynamic content in the proper section.

```php
@extends('layouts.app')

@section('content')

@endsection
```

## Partials

Laravel allows us to create template sections (instead of full page) to include and use anywhere (Master, page) it can be stored in the same folder or a separate folder (partials), and to use it

```php
@include('layouts.header')
```

## Components

## Compiling Assets

- Laravel uses Mix which is a WebPack based frontend build system that allows you to run js, css, sass.
- Inside the root folder we have a file called `webpack.mix.js` that has the configurations
- To use Sass we switch it to
    
    ```php
    mix.js('resources/js/app.js', 'public/js')
        .sass('resources/scss/app.scss', 'public/css', [
            //
        ]);
    ```
    
    and we create a folder called scss and at the files in it.
    
- To compile
    
    ```php
    npm run dev // once
    npm run watch // keep running
    ```
    
- To use the asset in the view
    
    ```html
    <link rel="stylesheet" href="{{ asset('css/app.css') }}">
    ```
    
- Resolve Images if we use storage folder
    
    ```css
    background: url('/storage/icon.jpg');
    ```
    

## Use Frontend Preset Library

We can use this [Github repo](https://github.com/laravel-frontend-presets) for a detailed steps for each library.