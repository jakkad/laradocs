# Validation

## Form Request Validation

We can validate the request on store or update by running a validation method on  the incoming request fields

```php
public function store(Request $request)
{
	$request->validate([
		'name'=> 'required|unique:cars',
		'founded'=> 'required|integer|min:0|max:2021'
	]);
}
```

If it's valid it will proceed (we add the create or update after it), if it fails it will redirect to previous page and throw a ValidationRequest

And to print the error in the view

```php
@if ($errors->any())
		@foreach ($errors->all() as $error)
				{{ $error }}
		@endforeach
@endif
```

## Making Validation Rules

We need to create a new rule using artisan

```php
php artisan make:rule Uppercase
```

The file of the rule is in `App→Rules`, the files has 2 methods as following

```php
public function passes($attribute, $value){ // validation method
		return $strtoupper($value) === $value;
}

public function message(){
	return 'Validation Error Message';
}
```

Then to use it in the validate method

```php
public function store(Request $request)
{
	$request->validate([
		'name'=> new Uppercase,
	]);
}
```

## Form Request

For more complex validation scenarios, you may wish to create a "form request". Form requests are custom request classes that encapsulate their own validation and authorization logic.

Creating the Request

```php
php artisan make:request CreateValidationRequest
```

The file is in `App→Http→Requests`, it has two methods

```php
public function authorize(){ // To authorize it to use this request
	return true;
}

public function rules(){ // valiation rules
	return [
		'name'=> 'required|unique:cars',
		'founded'=> 'required|integer|min:0|max:2021'
	];
}
```

To use the request inside the controller

```php
use App\Http\Requests\CreateValidationRequest; // import the class

public function store(CreateValidationRequest $request){ // using our class instead of the original request class

	$request->validated(); // it will contiue if it passes

}
```