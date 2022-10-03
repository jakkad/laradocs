Filament is a collection of tools/packages for rapid admin panel development. It consists of 4 main packages (Form, Table, Notification, and admin) while the last is the full solution that has the other packages pre-installed and configured.

This documentation will only focus on the admin panel package and all the needed docs related to it while using the sub-packages. More details about using the sub-packages and any customization using livewire can be found in the [official documentation](https://filamentphp.com/).

# Installation

## Requirements

Filament has a few requirements to run:

- PHP 8.0+
- Laravel v8.0+
- Livewire v2.0+

## Installation

To get started with the admin panel, you can install it using the command:

```bash
composer require filament/filament:"^2.0"
```

## Create User

After making sure we have did an initial Laravel migration we can create a user

```bash
php artisan make:filament-user
```

## ****Publishing configuration****

If you wish, you may publish the configuration of the package using:

```bash
php artisan vendor:publish --tag=filament-config
```

# Resource

Resources are the breakstones in filament, each resource is built on top of an eloquent model as a class and contains the main function to manage CRUD for this model.

## Create Resource

To create a resource for the `App\Models\Customer` model:

```bash
php artisan make:filament-resource Customer
```

This will create several files in the `app/Filament/Resources` directory:

```
.
+-- CustomerResource.php
+-- CustomerResource
|   +-- Pages
|   |   +-- CreateCustomer.php
|   |   +-- EditCustomer.php
|   |   +-- ListCustomers.php

```

The new resource class lives in `CustomerResource.php`.

> We should create the model (table) separately, then we can create the resource for it.
>

The classes in the `Pages` directory represent the individual pages and can be used to customized each page respectively.

### ****Automatically generating forms and tables****

We can auto generate forms and index tables based on our table’s fields with the help of  `doctrine/dbal` package which is required to use this functionality:

```bash
composer require doctrine/dbal
```

When creating your resource, you may now use `--generate`:

```bash
php artisan make:filament-resource Customer --generate
```

### **Generating a View page**

By default, only List, Create and Edit pages are generated for your resource. If you'd also like a **[View page](https://filamentphp.com/docs/2.x/admin/resources/viewing-records)**, use the `--view` flag:

```bash
php artisan make:filament-resource Customer --view
```

**Adding a View page to an existing resource**

```php
php artisan make:filament-page ViewUser --resource=UserResource --type=ViewRecord
```

You must register this new page in your resource's `getPages()` method:

```php
public static function getPages(): array
{
    return [
        'index' => Pages\ListUsers::route('/'),
        'create' => Pages\CreateUser::route('/create'),
        'view' => Pages\ViewUser::route('/{record}'),
        'edit' => Pages\EditUser::route('/{record}/edit'),
    ];
}
```

### ****Simple (modal) resources****

Sometimes, your models are simple enough that you only want to manage records on one page, using modals to create, edit and delete records. To generate a simple resource with modals:

```bash
php artisan make:filament-resource Customer --simple
```

Your resource will have a "Manage" page, which is a List page with modals added.

> Simple resource will have no `getRelations()` method
>

### ****Handling soft deletes****

If you'd like to add functionality to restore, force delete and filter trashed records in your resource, use the `--soft-deletes` flag when generating the resource:

```bash
php artisan make:filament-resource Customer --soft-deletes
```

In order to use soft deletes we have to include that in the migration first, then use the trait in the model

```php
// In migration
$table->softDeletes();

// In Model
class Customer extends Model
{
    use SoftDeletes;
}
```

## Structure

Each resource has four sections:

- Form: used to build the create/edit form and all the fields in it (details in Form Builder)
- Table: used to build the table inside the index page (details in Table Builder)
- Relation: used by relation manager to declare the relations with other resources (details in Relations)
- Pages: state all the registered pages for this resource (details in Pages)