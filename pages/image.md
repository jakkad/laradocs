# Image Upload

Add the upload button in the view

```php
<form action="/cars" method="POST" enctype="multipart/form-data"> // we need to add the enctype
<input type="file" name="image">
```

Migration to add only one column to an existing table

```php
public function up()
{
	Schema::table('cars', function(Blueprint $table) {
		$table->string('image_path');
	});
}

public function down()
{
	Schema::table('cars', function(Blueprint $table) {
		$table->dropColumn('image_path');
	});
}
```

Validation and store in the controller

```php
'image' => 'required|mimes:jpg,png,jpeg|max:5048' // validation Rules

// Methods we can use on $request

$test = $request->file('image')->guessExtension(); // return extension
$test = $request->file('image')->getMimeType(); // return image/extension
$test = $request->file('image')->getClientOriginalName(); // return original name
$test = $request->file('image')->guessClientExtension(); // return extension
$test = $request->file('image')->guessExtension(); // return extension without the dot
$test = $request->file('image')->getSize(); // return Size
$test = $request->file('image')->getError(); // return 0 = true or 1 = false based on the file is accepted or not in the validation rule
$test = $request->file('image')->isValid(); // Similar to above

// Store Methods
$newImageName = time(). '-'. $request->name. '.'. $request->name->extension(); // Change the name
$request->image->move(public_path('images'), $newImageName); // store in public->images

// Add it to the table
$car = Car::create([
		..
	'image_path' => $newImageName 
]);
```

Show the image in the view

```php
<img src="{{ asset('images/' . $car->image_path) }}" />
```