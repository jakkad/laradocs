# Database

Using DB in Laravel is straightforward and can be done by adding the settings to `.ENV` file which refers to the variables in `Config→database` file.

Laravel comes with a tool called Eloquent which is an ORM and allows you to write queries in the object oriented paradigm.

## Migration

Migration is a way to control database tables (creation, deletion) through Laravel and it’s stored under `database→migration`.

### Creating a Migration

A new migration can be created using artisan by one of the two ways:

**Create a migration independently** 

```bash
php artisan make:migration create_posts_table
```

**Create a migration while creating a model**

```bash
php artisan make:model Post -m
```

### Building the Table

Each migration has 2 functions

- Up: which is used to create the table from the migration
- Down: which does the opposite, drops the table.

**Creating the table fields:** we add the fields inside the schema and we can use multiple types based on the field's type

- Increments: Auto increased id `$table→increments('id')`
- string
- mediumText
- timestamps

### Migration commands

Doing the Migration

```php
php artisan migrate
```

Other migration commands

```php
php artisan migrate:install // similar to migrate but will keep track of which migration has been used
php artisan migrate:reset // will call the down function in the migration files
php artisan migrate:refresh // will rollback every change
```

## Query Builder

Laravel's database query builder provides a convenient, fluent interface to creating and running database queries.

Usage: to use it we need to use `"Illuminate\Support\Facades\DB".`

### **Manual Query**

```php
$posts = DB::select('select * from posts WHERE id = ?', [value]); // Query

OR

$posts = DB::select('select * from posts WHERE id = :id', ['id' => 7]); // Query

dd($posts); // Die Dumb 
```

### CRUD Methods

**Insert**

```php
$posts = DB::table('posts')
				->insert([
					'title'=> 'Post', 'body' => 'Body'
				]);
```

**Update**

```php
$posts = DB::table('posts')
				->where('id', '=', $id)
				->update([
					'title'=> 'Post', 'body' => 'Body'
				]);
```

**Delete**

```php
$posts = DB::table('posts')
				->where('id', '=', $id)
				->delete();
```

### **Query Builder:**

```php
$id = 7;

$posts = DB::table('posts')
				->where('id','<', $id) // middle param is optional if it's equal
				->get(); // get all
```

### **Get only one column**

```php
$posts = DB::table('posts')
				->select('body')
				->get() 
```

### **Where methods**

```php
$posts = DB::table('posts')
				->where('created_at', '>', now()->subDay())
				->orWhere('title', 'Prof.') // Similar to Else if
				->get() 

$posts = DB::table('posts')
				->whereBetween('id', [7,9]) // the column's value is between two values
				->get() 

$posts = DB::table('posts')
				->whereNotNull('title') // Values aren't NULL
				->get() 

$posts = DB::table('posts')
				->select('title') 
				->distinct() // select only distinct values
				->get() 
```

### **Sorting Methods**

```php
$posts = DB::table('posts')
				->orderBy('title', 'asc')
				->get() 

$posts = DB::table('posts')
				->latest()
				->get() 

$posts = DB::table('posts')
				->oldest()
				->get() 

$posts = DB::table('posts')
				->inRandomOrder()
				->get() 
```

### **Other Returning Methods**

```php
$posts = DB::table('posts')
				->orderBy('title', 'asc')
				->first() // Only first row

$posts = DB::table('posts')
				->find($id) // search for specifi value  

$posts = DB::table('posts')
				->where('id', $id)
				->count() // count of rows

$posts = DB::table('posts')
				->min('id') // minimum value

$posts = DB::table('posts')
				->max('id') // maximum value
```
## Seeding