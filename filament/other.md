# Global Search

## Titles

To enable global search on your model, you must **[set a title attribute](https://filamentphp.com/docs/2.x/admin/resources/global-search#setting-a-title-attribute)** for your resource:

```php
protected static ?string $recordTitleAttribute = 'title';
```

This attribute is used to retrieve the search result title for that record.

You may customise the title further by overriding `getGlobalSearchResultTitle()` method

```php
public static function getGlobalSearchResultTitle(Model $record): string
{
    return $record->name;
}
```

## Multi Column Search

If you would like to search across multiple columns of your resource, you may override the `getGloballySearchableAttributes()` method. "Dot-syntax" allows you to search inside of relationships:

```php
public static function getGloballySearchableAttributes(): array
{
    return ['title', 'slug', 'author.name', 'category.name'];
}
```

## Details

Search results can display "details" below their title, which gives the user more information about the record. To enable this feature, you must override the `getGlobalSearchResultDetails()` method:

```php
public static function getGlobalSearchResultDetails(Model $record): array
{
    return [
        'Author' => $record->author->name,
        'Category' => $record->category->name,
    ];
}
```

In this example, the category and author of the record will be displayed below its title in the search result. However, the `category` and `author` relationships will be lazy-loaded, which will result in poor results performance. To **[eager-load](https://laravel.com/docs/eloquent-relationships#eager-loading)** these relationships, we must override the `getGlobalSearchEloquentQuery()` method:

```php
protected static function getGlobalSearchEloquentQuery(): Builder
{
    return parent::getGlobalSearchEloquentQuery()->with(['author', 'category']);
}
```

## URL

Global search results will link to the **[Edit page](https://filamentphp.com/docs/2.x/admin/resources/editing-records)** of your resource, or the **[View page](https://filamentphp.com/docs/2.x/admin/resources/viewing-page)** if the user does not have **[edit permissions](https://filamentphp.com/docs/2.x/admin/resources/editing-records#authorization)**. To customize this, you may override the `getGlobalSearchResultUrl()` method and return a route of your choice:

```php
public static function getGlobalSearchResultUrl(Model $record): string
{
    return UserResource::getUrl('edit', ['record' => $record]);
}
```

## Actions

Global search support actions that render a button or link which may open a URL or emit a Livewire event. Actions will render as link by default, but you may configure it to render a button using the `button()` method.

Actions can be defined as follows:

```php
public static function getGlobalSearchResultActions(Model $record): array
{
    return [
        Action::make('edit')
            ->iconButton()
            ->icon('heroicon-s-pencil')
            ->url(static::getUrl('edit', ['record' => $record])),
    ];
}
```

# Widgets

## Getting Started

Filament allows you to display widgets inside pages, below the header and above the footer.

You can use an existing **[dashboard widget](https://filamentphp.com/docs/2.x/admin/dashboard)**, or create one specifically for the resource.

## Creating a resource Widget

To get started building a resource widget:

```php
php artisan make:filament-widget CustomerOverview --resource=CustomerResource
```

This command will create two files - a widget class in the `app/Filament/Resources/CustomerResource/Widgets` directory, and a view in the `resources/views/filament/resources/customer-resource/widgets` directory.

You must register the new widget in your resource's `getWidgets()` method:

```php
public static function getWidgets(): array
{
    return [
        Widgets\CustomerOverview::class,
    ];
}
```

If you'd like to learn how to build and customize widgets, check out the **[Dashboard](https://filamentphp.com/docs/2.x/admin/dashboard)** documentation section.

## Displaying a widget on a resource page

To display a widget on a resource page, use the `getHeaderWidgets()` or `getFooterWidgets()` methods for that page:

```php
class ListCustomers extends ListRecords
{
    public static string $resource = CustomerResource::class;
 
    protected function getHeaderWidgets(): array
    {
        return [
            CustomerResource\Widgets\CustomerOverview::class,
        ];
    }
}
```

`getHeaderWidgets()` returns an array of widgets to display above the page content, whereas `getFooterWidgets()` are displayed below.

If you'd like to customize the number of grid columns used to arrange widgets, check out the **[Pages documentation](https://filamentphp.com/docs/2.x/admin/pages/widgets#customizing-the-widgets-grid)**.

## Accessing the current record

If you're using a widget on an **[Edit](https://filamentphp.com/docs/2.x/admin/resources/editing-records)** or **[View](https://filamentphp.com/docs/2.x/admin/resources/viewing-records)** page, you may access the current record by defining a `$record` property on the widget class:

```php
public ?Model $record = null;
```

# Custom Pages for Resources

Filament allows you to create completely custom pages for resources. To create a new page, you can use:

```php
php artisan make:filament-page SortUsers --resource=UserResource --type=custom
```

This command will create two files - a page class in the `/Pages` directory of your resource directory, and a view in the `/pages` directory of the resource views directory.

You must register custom pages to a route in the static `getPages()` method of your resource:

```php
public static function getPages(): array
{
    return [
        // ...
        'sort' => Pages\SortUsers::route('/sort'),
    ];
}
```

Any **[parameters](https://laravel.com/docs/routing#route-parameters)** defined in the route's path will be available to the page class, in an identical way to **[Livewire](https://laravel-livewire.com/docs/rendering-components#route-params)**.

To generate a URL for a resource route, you may call the static `getUrl()` method on the resource class:

```php
UserResource::getUrl('sort');
```

# Resource Security

Filament will expose all model attributes to JavaScript, except if they are `$hidden` on your model. This is Livewire's behaviour for model binding. We preserve this functionality to facilitate the dynamic addition and removal of form fields after they are initially loaded, while preserving the data they may need.

> While attributes may be visible in JavaScript, only those with a form field are actually editable by the user. This is not an issue with mass assignment.
>

To remove certain attributes from JavaScript on the Edit and View pages, you may override **[the `mutateFormDataBeforeFill()` method](https://filamentphp.com/docs/2.x/admin/resources/editing-records#customizing-data-before-filling-the-form)**:

```php
protected function mutateFormDataBeforeFill(array $data): array
{
    unset($data['is_admin']);

    return $data;
}
```

In this example, we remove the `is_admin` attribute from JavaScript, as it's not being used by the form.