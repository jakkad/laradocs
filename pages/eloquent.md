# Eloquent

<aside>
💡 Laravel includes Eloquent, an object-relational mapper (ORM) that makes it enjoyable to interact with your database. When using Eloquent, each database table has a corresponding "Model" that is used to interact with that table. In addition to retrieving records from the database table, Eloquent models allow you to insert, update, and delete records from the table as well.

</aside>

## Model Functions

We can link the model to a different table

```php
protected $table = name;
```

Change the Primary key or disable it

```php
protected $primaryKey = 'column' // change it

protected $primaryKey = false // disable it

```

Disable Timestamps

```php
protected $timestamps = false;
```

Hide or show specific columns

```php
protected $hidden = ['password'];

protected $visible = ['name'];
```

Allow inserting data to certain coulmns

```bash
protected $fillable = ['column1', 'column2',...]
```

## Retrieve Data

First to use Eloquent in the Controller we need to call the model

```php
use App\Models\Car;
```

Select all rows

```php
$cars = Car::all();
```

Get one row

```php
$cars = Car::where('name','=',$name)->get();
```

Chunk Results

```php
$flights = Flight::chunk(200, function ($flights) {
    foreach ($flights as $flight) {
        //
    }
});
```

Return results only if found

```php
$cars = Car::where('name','=',$name)->firstOrFail(); // only one row

$cars = Car::where('name','=',$name)->findOrFail();
```

Other Returning Methods

```php
$cars = Car::where('name','=',$name)->count(); // Only count

$cars = Car::all()->count(); // count of all rows
```

## Inserting Data

In the view the form should have the following settings

```php
<form action='/cars' method="POST">
@csrf
..
</form>
```

To store the data:

```php
// Method 1

public function store(Request $request){

	$car = new Car;
	$car->name = $request->input('name');
	$car->founded = $request->input('founded');
	$car->save();

	return redirect('/cars');
}

// Method 2

public function store(Request $request){
	$car = Car::create([
		'name' => $request->input('name'),
		'founded' => $request->input('founded');
	]);

}

// but we need to define the fillable columns in the model

protected $fillable = ['name', 'founded'];
```

## Updating Data

The edit button should have the following link

```php
<a href="cars/{{ $car->id }}/edit">Edit</a>
```

In the View

We pass the data from the controller to the view+ we setup the form

```php
<form action='/cars/{{ $car->id}}' method="POST">
@csrf
@method('PUT')

<input name="name" value="{{ $car->name }}">
</form>
```

To update the data

```php
public function update(Request $request, $id){
	$car = Car::where('id', $id)
				->update([
					'name' => $request->input('name'),
					'founded' => $request->input('founded');
					]);
}
```

## Delete Data

The delete button and in the same page we will have the delete form

```php
<form action='/cars/{{ $car->id}}' method="POST">
@csrf
@method('Delete')

<button type="submit">Delete</button>
</form>
```

To delete

```php
// Method 1
public function destroy($id){
	$car = Car::find('id', $id)->first();
	$car->delete();
}

// Method 2 to pass the model
public function destroy(Car $car){
	$car->delete();
}
```

## Eloquent Serialization

When we start working with APIs we'll need to convert arrays into jSON, the default type of the returned data if it's more than one row is `COLLECTION` which is very similar to array

### Return array

In the controller

```php
$cars = Car::all()->toArray();
```

Then to use it in the view

```php
$foreach ($cars as $car)
	{{ $car['name'] }}
$endforeach
```

### Return jSON

In the controller we need to convert it to json then object and in the view can be used like a collection

```php
$cars = Car::all()->toJson();

$cars = json_decode($cars);
```