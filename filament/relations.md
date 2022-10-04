Filament provides methods to access and store data through relations, but before that we have to define those relations inside Model classes (as well as all needed fields in DB)

# Relation Types

## BelongsTo

### Select field

Filament includes the ability automatically loads options from a `BelongsTo` relationship:

```php
Select::make('author_id')
    ->relationship('author', 'name')
```

You may customize the database query that retrieves options using the third parameter of the `relationship()` method:

```php
Select::make('authorId')
    ->relationship('author', 'name', fn (Builder $query) => $query->withTrashed())
```

If you'd like to customize the label of each option, maybe to be more descriptive, or to concatenate a first and last name, you should use a virtual column in your database migration:

```php
$table->string('full_name')->virtualAs('concat(first_name, \' \', last_name)');
```

```php
Select::make('authorId')
    ->relationship('author', 'full_name')
```

> This Can be applied on Select, MultiSelect, Checkbox List, or Repeater
>

### Layout Component

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

## HasOne

Similar to the Layout component above we can publish the parent’s fields in a fieldset. [Read more](https://filamentphp.com/docs/2.x/admin/resources/getting-started#hasone)

## HasMany

### Relation manager

"Relation managers" in Filament allow admins to list, create, associate, edit, dissociate and delete related records without leaving the resource's Edit page.

The related records are listed in a table, which has buttons to open a modal for each action.

For more information on relation managers, see the **[full documentation](https://filamentphp.com/docs/2.x/admin/resources/relation-managers)**.

### Repeater

If we want to create child records from within the Parent’s form, we could use a **[repeater](https://filamentphp.com/docs/2.x/forms/fields#populating-automatically-from-a-relationship)**:

```php
Repeater::make('qualifications')
    ->relationship()
    ->schema([
        // ...
    ])
```

## HasManyThrough

It’s supported through the **Relation Manager**

## BelongsToMany

### Multi-select field

Filament can automatically load `MultiSelect` options from a `BelongsToMany` relationship:

```php
use Filament\Forms\Components\MultiSelect;

MultiSelect::make('technologies')
    ->relationship('technologies', 'name')
```

More information about `BelongsToManyMultiSelect` is available in the **[Form docs](https://filamentphp.com/docs/2.x/forms/fields#populating-automatically-from-a-belongstomany-relationship)**.

### Checkbox list field

Filament can automatically load `CheckboxList` options from a `BelongsToMany` relationship:

```php
use Filament\Forms\Components\CheckboxList;

CheckboxList::make('technologies')
    ->relationship('technologies', 'name')
```

More information about `CheckboxList` is available in the **[Form docs](https://filamentphp.com/docs/2.x/forms/fields#populating-automatically-from-a-relationship-1)**.

It’s supported also through the **Relation Manager**

# Relation Manager

"Relation managers" in Filament allow administrators to list, create, attach, associate, edit, detach, dissociate and delete related records without leaving the resource's Edit page. Resource classes contain a static `getRelations()` method that is used to register relation managers for your resource.

[Read more on how to use it](https://filamentphp.com/docs/2.x/admin/resources/relation-managers)