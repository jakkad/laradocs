# Routing

## Basic Routing

We can handle the route directly using a function

```php
Route::get('/users', function() {
	return view('welcome'); // return a view
	return 'Here is a test text'; // return a text
	return ['cat','dog','fish']; // return an array
	return response()->json([
					'name' => 'Jake',
					'Lname' => 'Akkad'
		]); // return a json object
	return redirect('/'); // redirect
});
```

## Route to Controller

The more common way to handle a route is by accessing a function in a controller

```php
Route::get('/user', [UserController::class, 'index']);
```

## Naming Routes

Laravel allows us to name routes for easier access to them from the view

```php
Route::get('/products/{id}', [ProductsController::class, 'show'])->name('products');
```

and then we can use it anywhere like in a view

```html
<a href="{{ route('products') }}">Products </a>
```

## Route Parameters

We can pass parameters through routes and it can be accessed in the controller as a parameter of the function

```php
// in the route
Route::get('/products/{id}', [ProductsController::class, 'show']);

// in the controller's function
public function show($id){...}
```

## Route Model Binding

When we pass a model ID as a parameter to query the DB looking for it, Laravel provides us an easier alternative by passing the model itself

```php
use App\Http\Controllers\UserController;
use App\Models\User;
 
// Route definition...
Route::get('/users/{user}', [UserController::class, 'show']);
 
// Controller method definition...
public function show(User $user)
{
    return view('user.profile', ['user' => $user]);
}
```

## Parameter Conditions

We can add regular expression to the route to accept specific type of data for the parameter

```php
Route::get('/products/{id}', [ProductsController::class, 'show'])->where('id', '[0-9]+'); // Integer
Route::get('/products/{name}', [ProductsController::class, 'show'])->where('name', '[a-zA-Z]+'); // String
Route::get('/products/{name}/id', [ProductsController::class, 'show'])->where([
	'name' =>'[a-zA-Z]+',
	'id' => '[0-9]+']); // Multiple
```

## Type of Routes