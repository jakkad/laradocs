# Relationships

Relationships in Laravel represent the relation between one DB table to another, so for example a blog post in the posts table has a writer in the users table and few comments in the comment table, Laravel provides using Eloquent an easy way to create those relationships and access data between tables.

Eloquent Relationships are defined as methods inside the model classes, which means that all the chaining and querying capabilities can be applied.

## Pre-requisite

Before adding the relationship we need to make sure to add the foreign key column to our table during creating the migration.

```php
// Without defining the relation inside DB

$table->unsignedInteger('user_id');

// With defining the relation inside DB
$table->unsignedInteger('manufactur_id');
$table->foreign('manufactur_id')->references('id')->on('manufacturs')->onDelete('cascade');
```

## One to One

A one-to-one relationship is a very basic type of database relationship. For example, a **`User`** model might be associated with one **`Phone`** model. To define this relationship, we will place a **`phone`** method on the **`User`** model. The **`phone`** method should call the **`hasOne`** method and return its result. The **`hasOne`** method is available to your model via the model's **`Illuminate\Database\Eloquent\Model`** base class:

```php
<?php
 
namespace App\Models;
 
use Illuminate\Database\Eloquent\Model;
 
class User extends Model
{
    /**
     * Get the phone associated with the user.
     */
    public function phone()
    {
        return $this->hasOne(Phone::class);
    }
}
```

The first argument passed to the **`hasOne`** method is the name of the related model class. Once the relationship is defined, we may retrieve the related record using Eloquent's dynamic properties. Dynamic properties allow you to access relationship methods as if they were properties defined on the model:

```php
$phone = User::find(1)->phone;
```

Eloquent determines the foreign key of the relationship based on the parent model name. In this case, the **`Phone`** model is automatically assumed to have a **`user_id`** foreign key (this field should be already created inside the phone table).

### One to One (Inverse)

To define a relationship on the **`Phone`** model that will let us access the user that owns the phone. We can define the inverse of a **`hasOne`** relationship using the **`belongsTo`** method:

```php
class Phone extends Model
{
    /**
     * Get the user that owns the phone.
     */
    public function user()
    {
        return $this->belongsTo(User::class);
    }
}
```

When invoking the **`user`** method, Eloquent will attempt to find a **`User`** model that has an **`id`** which matches the **`user_id`** column on the **`Phone`** model.

## One to Many

A one-to-many relationship is used to define relationships where a single model is the parent to one or more child models. For example, a blog post may have an infinite number of comments. Like all other Eloquent relationships, one-to-many relationships are defined by defining a method on your Eloquent model:

```php
class Post extends Model
{
    /**
     * Get the comments for the blog post.
     */
    public function comments()
    {
        return $this->hasMany(Comment::class);
    }
}
```

Remember, Eloquent will automatically determine the proper foreign key column for the **`Comment`** model. By convention, Eloquent will take the "snake case" name of the parent model and suffix it with **`_id`**. So, in this example, Eloquent will assume the foreign key column on the **`Comment`** model is **`post_id`**.

Once the relationship method has been defined, we can access the [collection](https://laravel.com/docs/9.x/eloquent-collections) of related comments by accessing the **`comments`** property. Remember, since Eloquent provides "dynamic relationship properties", we can access relationship methods as if they were defined as properties on the model:

```php
use App\Models\Post;
 
$comments = Post::find(1)->comments;
 
foreach ($comments as $comment) {
    //
}
```

Since all relationships also serve as query builders, you may add further constraints to the relationship query by calling the **`comments`** method and continuing to chain conditions onto the query:

```php
$comment = Post::find(1)->comments()
                    ->where('title', 'foo')
                    ->first();
```

### One to Many (Inverse) / Belongs To

let's define a relationship to allow a comment to access its parent post. To define the inverse of a **`hasMany`** relationship, define a relationship method on the child model which calls the **`belongsTo`** method:

```php
class Comment extends Model
{
    /**
     * Get the post that owns the comment.
     */
    public function post()
    {
        return $this->belongsTo(Post::class);
    }
}
```

Once the relationship has been defined, we can retrieve a comment's parent post by accessing the **`post`** "dynamic relationship property":

```php
use App\Models\Comment;
 
$comment = Comment::find(1);
 
return $comment->post->title;
```

In the example above, Eloquent will attempt to find a **`Post`** model that has an **`id`** which matches the **`post_id`** column on the **`Comment`** model.

## One of Many

Sometimes a model may have many related models, yet you want to easily retrieve the "latest" or "oldest" related model of the relationship. For example, a **`User`** model may be related to many **`Order`** models, but you want to define a convenient way to interact with the most recent order the user has placed. You may accomplish this using the **`hasOne`** relationship type combined with the **`ofMany`** methods:

```php
/**
 * Get the user's most recent order.
 */
public function latestOrder()
{
    return $this->hasOne(Order::class)->latestOfMany();
}
```

Likewise, you may define a method to retrieve the "oldest", or first, related model of a relationship:

```php
/**
 * Get the user's oldest order.
 */
public function oldestOrder()
{
    return $this->hasOne(Order::class)->oldestOfMany();
}
```

By default, the **`latestOfMany`** and **`oldestOfMany`** methods will retrieve the latest or oldest related model based on the model's primary key, which must be sortable. However, sometimes you may wish to retrieve a single model from a larger relationship using a different sorting criteria.

For example, using the **`ofMany`** method, you may retrieve the user's most expensive order. The **`ofMany`** method accepts the sortable column as its first argument and which aggregate function (**`min`** or **`max`**) to apply when querying for the related model:

```php
/**
 * Get the user's largest order.
 */
public function largestOrder()
{
    return $this->hasOne(Order::class)->ofMany('price', 'max');
}
```

## Has One Through

The "has-one-through" relationship defines a one-to-one relationship with another model. However, this relationship indicates that the declaring model can be matched with one instance of another model by proceeding *through* a third model.

For example, in a vehicle repair shop application, each **`Mechanic`** model may be associated with one **`Car`** model, and each **`Car`** model may be associated with one **`Owner`** model. While the mechanic and the owner have no direct relationship within the database, the mechanic can access the owner *through* the **`Car`** model. Let's look at the tables necessary to define this relationship:

```php
mechanics
    id - integer
    name - string
 
cars
    id - integer
    model - string
    mechanic_id - integer
 
owners
    id - integer
    name - string
    car_id - integer
```

Now that we have examined the table structure for the relationship, let's define the relationship on the **`Mechanic`** model:

```php
class Mechanic extends Model
{
    /**
     * Get the car's owner.
     */
    public function carOwner()
    {
        return $this->hasOneThrough(Owner::class, Car::class);
    }
}
```

The first argument passed to the **`hasOneThrough`** method is the name of the final model we wish to access, while the second argument is the name of the intermediate model.

## Has Many Through

The "has-many-through" relationship provides a convenient way to access distant relations via an intermediate relation. For example, let's assume we are building a deployment platform like [Laravel Vapor](https://vapor.laravel.com/). A **`Project`** model might access many **`Deployment`** models through an intermediate **`Environment`** model. Using this example, you could easily gather all deployments for a given project. Let's look at the tables required to define this relationship:

```php
projects
    id - integer
    name - string
 
environments
    id - integer
    project_id - integer
    name - string
 
deployments
    id - integer
    environment_id - integer
    commit_hash - string
```

Now that we have examined the table structure for the relationship, let's define the relationship on the **`Project`** model:

```php
class Project extends Model
{
    /**
     * Get all of the deployments for the project.
     */
    public function deployments()
    {
        return $this->hasManyThrough(Deployment::class, Environment::class);
    }
}
```

The first argument passed to the **`hasManyThrough`** method is the name of the final model we wish to access, while the second argument is the name of the intermediate model.

Though the **`Deployment`** model's table does not contain a **`project_id`** column, the **`hasManyThrough`** relation provides access to a project's deployments via **`$project->deployments`**. To retrieve these models, Eloquent inspects the **`project_id`** column on the intermediate **`Environment`** model's table. After finding the relevant environment IDs, they are used to query the **`Deployment`** model's table.

## Many To Many

Many-to-many relations are slightly more complicated than **`hasOne`** and **`hasMany`** relationships. An example of a many-to-many relationship is a user that has many roles and those roles are also shared by other users in the application. For example, a user may be assigned the role of "Author" and "Editor"; however, those roles may also be assigned to other users as well. So, a user has many roles and a role has many users.

To define this relationship, three database tables are needed: **`users`**, **`roles`**, and **`role_user`**. The **`role_user`** table is derived from the alphabetical order of the related model names and contains **`user_id`** and **`role_id`** columns. This table is used as an intermediate table linking the users and roles.

Remember, since a role can belong to many users, we cannot simply place a **`user_id`** column on the **`roles`** table. This would mean that a role could only belong to a single user. In order to provide support for roles being assigned to multiple users, the **`role_user`** table is needed. We can summarize the relationship's table structure like so:

```php
users
    id - integer
    name - string
 
roles
    id - integer
    name - string
 
role_user
    user_id - integer
    role_id - integer
```

Many-to-many relationships are defined by writing a method that returns the result of the **`belongsToMany`** method. The **`belongsToMany`** method is provided by the **`Illuminate\Database\Eloquent\Model`** base class that is used by all of your application's Eloquent models. For example, let's define a **`roles`** method on our **`User`** model. The first argument passed to this method is the name of the related model class:

```php
class User extends Model
{
    /**
     * The roles that belong to the user.
     */
    public function roles()
    {
        return $this->belongsToMany(Role::class);
    }
}
```

Once the relationship is defined, you may access the user's roles using the **`roles`** dynamic relationship property:

```php
use App\Models\User;
 
$user = User::find(1);
 
foreach ($user->roles as $role) {
    //
}
```

Since all relationships also serve as query builders, you may add further constraints to the relationship query by calling the **`roles`** method and continuing to chain conditions onto the query:

```php
$roles = User::find(1)->roles()->orderBy('name')->get();
```

### Inverse of Many to Many

To define the "inverse" of a many-to-many relationship, you should define a method on the related model which also returns the result of the **`belongsToMany`** method. To complete our user / role example, let's define the **`users`** method on the **`Role`** model:

```php
class Role extends Model
{
    /**
     * The users that belong to the role.
     */
    public function users()
    {
        return $this->belongsToMany(User::class);
    }
}
```

As you can see, the relationship is defined exactly the same as its **`User`** model counterpart with the exception of referencing the **`App\Models\User`** model. Since we're reusing the **`belongsToMany`** method, all of the usual table and key customization options are available when defining the "inverse" of many-to-many relationships.