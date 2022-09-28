# Getting Started

Filament admin panel is a packages that offers a full working admin dashboard and uses all the other filament packages (form, table, notifications). We can also use any of those packages separately by following the installation rules in the documentation.

This documentation focus only on the admin panel and it ignores anything related to using the individual packages independently as well as any customization using Livewire.

## Installation

### Requirements

Filament has a few requirements to run:

- PHP 8.0+
- Laravel v8.0+
- Livewire v2.0+

This package is compatible with other Filament v2.x products. The **[form builder](https://filamentphp.com/docs/forms)**, **[table builder](https://filamentphp.com/docs/tables)** and **[notifications](https://filamentphp.com/docs/notifications)** come pre-installed with the package, and no other installation steps are required to use them within the admin panel.

### Installation

To get started with the admin panel, you can install it using the command:

```bash
composer require filament/filament:"^2.0"
```

### Create User

After making sure we have did an initial Laravel migration we can create a user

```bash
php artisan make:filament-user
```

### ****Publishing configuration****

If you wish, you may publish the configuration of the package using:

```bash
php artisan vendor:publish --tag=filament-config
```

## Basics

Resources are static classes that are used to build CRUD interfaces for your Eloquent models. They describe how administrators should be able to interact with data from your app - using tables and forms.

### Create Resource

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

Your new resource class lives in `CustomerResource.php`.

> We should create the model (table) separately, then we can create the resource for it.
>

The classes in the `Pages` directory are used to customize the pages in the admin panel that interact with your resource.

### Resource Structure

Each resource has two main sections:

- Form: which is the method used to build the form on the create and Edit Pages (More in Forms Builder)
- Table: which is the method used to generate the tables on the index page (More in Table Builder)

## Resource Options

### ****Simple (modal) resources****

Sometimes, your models are simple enough that you only want to manage records on one page, using modals to create, edit and delete records. To generate a simple resource with modals:

```bash
php artisan make:filament-resource Customer --simple
```

Your resource will have a "Manage" page, which is a List page with modals added.

> Simple resource will have no `getRelations()` method
>

Filament offers options to [create, edit, view data in modals](https://filamentphp.com/docs/2.x/admin/resources/listing-records#creating-records-in-modals)

### ****Automatically generating forms and tables****

We can auto generate forms and index tables based on our table’s fields with the help of  `doctrine/dbal` package which is required to use this functionality:

```bash
composer require doctrine/dbal
```

When creating your resource, you may now use `--generate`:

```bash
php artisan make:filament-resource Customer --generate
```

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

### ****Record titles****

A `$recordTitleAttribute` may be set for your resource, which is the name of the column on your model that can be used to identify it from others.

For example, this could be a blog post's `title` or a customer's `name`:

```php
protected static ?string $recordTitleAttribute = 'name';
```

This is required for features like **[global search](https://filamentphp.com/docs/2.x/admin/resources/global-search)** to work.

### Model Labels

Each resource has a "model label" which is automatically generated from the model name. For example, an `App\Models\Customer` model will have a `customer` label, Also Filament provides a plural version of the label and both can be customized :

```php
protected static ?string $modelLabel = 'cliente';

protected static ?string $pluralModelLabel = 'clientes'; 
```

### Deleting Pages

If you'd like to delete a page from your resource, you can just delete the page file from the `Pages` directory of your resource, and its entry in the `getPages()` method.

For example, you may have a resource with records that may not be created by anyone. Delete the `Create` page file, and then remove it from `getPages()`:

```php
public static function getPages(): array
{
    return [
        'index' => Pages\ListCustomers::route('/'),
        'edit' => Pages\EditCustomer::route('/{record}/edit'),
    ];
}
```

# Relations

Filament provides methods to access and store data through relations, but before that we have to define those relations inside Model classes (as well as all needed fields in DB)

## Relation Types

### BelongsTo

**Select field**

Filament includes the ability automatically loads options from a `BelongsTo` relationship:

```php
Select::make('author_id')
    ->relationship('author', 'name')
```

**Layout Component**

We can publish the Parent’s fields in a fieldset inside the edit form of the child table, this can be useful in the edit form mainly

```php
Fieldset::make('Author')
    ->relationship('author')
    ->schema([
        TextInput::make('name')->required(),
    ])
```

> In order to use that we need to make sure we don’t use DB relationship as well as making the relation field (author_id) nullable.
>

### HasOne

Similar to the Layout component above we can publish the parent’s fields in a fieldset

### HasMany

**Relation manager**

"Relation managers" in Filament allow admins to list, create, associate, edit, dissociate and delete related records without leaving the resource's Edit page.

The related records are listed in a table, which has buttons to open a modal for each action.

For more information on relation managers, see the **[full documentation](https://filamentphp.com/docs/2.x/admin/resources/relation-managers)**.

**Repeater**

If we want to create child records from within the Parent’s form, we could use a **[repeater](https://filamentphp.com/docs/2.x/forms/fields#populating-automatically-from-a-relationship)**:

```php
Repeater::make('qualifications')
    ->relationship()
    ->schema([
        // ...
    ])
```

### HasManyThrough

It’s supported through the **Relation Manager**

### BelongsToMany

**Multi-select field**

Filament can automatically load `MultiSelect` options from a `BelongsToMany` relationship:

```php
use Filament\Forms\Components\MultiSelect;

MultiSelect::make('technologies')
    ->relationship('technologies', 'name')
```

More information about `BelongsToManyMultiSelect` is available in the **[Form docs](https://filamentphp.com/docs/2.x/forms/fields#populating-automatically-from-a-belongstomany-relationship)**.

**Checkbox list field**

Filament can automatically load `CheckboxList` options from a `BelongsToMany` relationship:

```php
use Filament\Forms\Components\CheckboxList;

CheckboxList::make('technologies')
    ->relationship('technologies', 'name')
```

More information about `CheckboxList` is available in the **[Form docs](https://filamentphp.com/docs/2.x/forms/fields#populating-automatically-from-a-relationship-1)**.

It’s supported also through the **Relation Manager**

# Navigation

Filament will automatically generate a navigation menu item for your resource using the **[plural label](https://filamentphp.com/docs/2.x/admin/resources/getting-started#plural-label)**. It can be customized:

```php
protected static ?string $navigationLabel = 'Mis Clientes';
```

## Icons

By default Filament use Laravel HeroIcon, but we can import and use other libraries. To customize the icon:

```php
protected static ?string $navigationIcon = 'heroicon-o-user-group';
```

## Sorting Navigation Items

The `$navigationSort` property allows you to specify the order in which navigation items are listed:

```php
protected static ?int $navigationSort = 2;
```

## Grouping Navigation Items

You may group navigation items by specifying a `$navigationGroup` property:

```php
protected static ?string $navigationGroup = 'Shop';
```

# Configurations

## Deploy to Production

By default, all `App\Models\User`s can access Filament locally. To allow them to access Filament in production, you must take a few extra steps to ensure that only the correct users have access to the admin panel. Details **[Users page](https://filamentphp.com/docs/2.x/admin/users#authorizing-access-to-the-admin-panel)**.

## Publishing Translations

If you wish to translate the package, you may publish the language files using:

```bash
php artisan vendor:publish --tag=filament-translations
```

Since this package depends on other Filament packages, you may wish to translate those as well:

```bash
php artisan vendor:publish --tag=filament-forms-translations
php artisan vendor:publish --tag=filament-tables-translations
php artisan vendor:publish --tag=filament-support-translations
```

## Upgrading

To upgrade the package to the latest version, you must run:

```bash
composer update
php artisan filament:upgrade
```

We recommend adding the `filament:upgrade` command to your `composer.json`'s `post-update-cmd` to run it automatically:

```jsx
"post-update-cmd": [
    // ...
    "@php artisan filament:upgrade"
],
```