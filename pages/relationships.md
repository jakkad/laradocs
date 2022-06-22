# Relationships

## One to Many

First type of table relations, one row of table 1 has one or more rows from table 2

Migration for the table 2 should have a foreign key column which is equal to the primary key of the table 1

```php
$table->unsignedInteger('car_id');
$table->foreign('car_id')->references('id')->on('cars')->onDelete('cascade'); //
```

Note: the onDelete function defines the behavior on the table 2 rows if the connected row in table 1 is deleted, the other value is `'set null'`

Model of table 1

```php
public function carModels(){
	return $this->hasMany(carModel::class);
}
```

Model of table 2

```php
public function car(){
	return $this->belongsTo(Car::class);
}
```

Show data 

```php
{{ $car->name }} // table 1 data

@forelse ($car->carModels as $model) // table 2 data
	{{ $model['model_name'] }}

@empty

@endforelse

```

## HasMany

This helps to pull the relationship of a relationship (Table2 row has multiple rows in Table3)

Migration of Table3

```php
$table->unsignedInteger('model_id');
$table->foreign('model_id')->refernces('id')->on('models')-onDelete('cascade'); //
```

Model of Table1

```php
public function engines()
{
	return $this->hasManyThrough(
									Engine::class,
									CarModel::class,
									'car_id', // forign key 1
									'model_id'); // foriegn key 2
}
```

Show data

```php
@forelse ($car->carModels as $model) // table 2 data
	{{ $model->model_name }}
	@foreach ($car->engines as $engine)
		@if ($model->id == $engine->model_id)
			{{ $engine->engine_name }}
		@endif
	@endforeach

@empty

@endforelse
```

## HasOne

Similar to hasMany but in this case Table2 has only one row in Table3

Migration is the same

Model of Table1

```php
public function productionDate()
{
	return $this->hasOneThrough(
									CarProductionDate::class,
									CarModel::class,
									'car_id', // forign key 1
									'model_id'); // foriegn key 2
}
```

Show Data

```php
$car->produtionDate->created_at
```

## Many To Many

This relationship is between two tables where a row from Table1 can have on or more rows in Table2 and vice versa, to create this we need to have a Pivot Table (Table1name_Table2name)

Pivot table needs a table and a migration

```php
$table->integer('car_id')->unsigned();
$table->integer('product_id')->unsigned();
$table->foreign('car_id')->refernces('id')->on('cars')->onDelete('cascade');
$table->foreign('product_id')->refernces('id')->on('products')->onDelete('cascade');
```

Table1 Model (Similar in Table2)

```php
public function products(){
	return $this->belongsToMany(Products::class);
}
```

Show Data

```php
$forelse ($car->products as $product)
	{{ $product->name }}
@empty

@endforelse
```