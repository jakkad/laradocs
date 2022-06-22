# Controllers

## Resource Controller

Laravel provides a way to create a controller that has all CRUD functions automatically, by only add a flag while generating it.

```bash
php artisan make:controller UserController --resource
```

## Passing Data to Views

We can send variables from the controller to the view

**Array method:** 

```php
public function index() {
	$title = "Hello";
	return view('test.index', ['name' => 'James']); 
}
```

Then we can use it in the view

```php
{{ $name}}
```

**With Method:**

```php
public function index() {
	$title = "Hello";
	return view('test.index')->with('title', $title);
}
```

Or for multiple variables

```php
public function index() {
	$data = [
		'title' => 'test',
		'description' => 'test'
	];
	return view('test.index')->with('data', $data);
}
```

Then to use it we loop over the array

```php
@foreach ($data as $item)
	{{$item}}
@endforeach
```