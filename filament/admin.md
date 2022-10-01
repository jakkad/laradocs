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

## Relation Manager

Relation managers" in Filament allow administrators to list, create, attach, associate, edit, detach, dissociate and delete related records without leaving the resource's Edit page. Resource classes contain a static `getRelations()` method that is used to register relation managers for your resource.

To create a relation manager, you can use the `make:filament-relation-manager` command:

```php
php artisan make:filament-relation-manager CategoryResource posts title // <Resource name> <relation name> <relation attribute>
```

This will create a `CategoryResource/RelationManagers/PostsRelationManager.php` file. This contains a class where you are able to define a **[form](https://filamentphp.com/docs/2.x/admin/resources/getting-started#forms)** and **[table](https://filamentphp.com/docs/2.x/admin/resources/getting-started#tables)** for your relation manager:

```php
public static function form(Form $form): Form
{
    return $form
        ->schema([
            Forms\Components\TextInput::make('title')->required(),
            Forms\Components\MarkdownEditor::make('content'),
            // ...
        ]);
}
 
public static function table(Table $table): Table
{
    return $table
        ->columns([
            Tables\Columns\TextColumn::make('title'),
            // ...
        ]);
```

You must register the new relation manager in your resource's `getRelations()` method:

```php
public static function getRelations(): array
{
    return [
        RelationManagers\PostsRelationManager::class,
    ];
}
```

For relationships with unconventional naming conventions, you may wish to include the `$inverseRelationship` property on the relation manager:

```php
protected static ?string $inverseRelationship = 'section'; // Since the inverse related model is `Category`, this is normally `category`, not `section`.
```

Once a table and form have been defined for the relation manager, visit the **[Edit](https://filamentphp.com/docs/2.x/admin/resources/editing-records)** or **[View](https://filamentphp.com/docs/2.x/admin/resources/viewing-records)** page of your resource to see it in action.

### Enabling Soft Deletes

By default, you will not be able to interact with deleted records in the relation manager. If you'd like to add functionality to restore, force delete and filter trashed records in your relation manager, use the `--soft-deletes` flag when generating the relation manager:

```php
php artisan make:filament-relation-manager CategoryResource posts title --soft-deletes
```

### Listing Records

Related records will be listed in a table. The entire relation manager is based around this table, which contains actions to **[create](https://filamentphp.com/docs/2.x/admin/resources/relation-managers#creating-records)**, **[edit](https://filamentphp.com/docs/2.x/admin/resources/relation-managers#editing-records)**, **[attach / detach](https://filamentphp.com/docs/2.x/admin/resources/relation-managers#attaching-and-detaching-records)**, **[associate / dissociate](https://filamentphp.com/docs/2.x/admin/resources/relation-managers#associating-and-dissociating-records)**, and delete records.

As per the documentation on **[listing records](https://filamentphp.com/docs/2.x/admin/resources/listing-records)**, you may use all the same utilities for customisation on the relation manager:

- **[Columns](https://filamentphp.com/docs/2.x/admin/resources/listing-records#columns)**
- **[Filters](https://filamentphp.com/docs/2.x/admin/resources/listing-records#filters)**
- **[Actions](https://filamentphp.com/docs/2.x/admin/resources/listing-records#actions)**
- **[Bulk actions](https://filamentphp.com/docs/2.x/admin/resources/listing-records#bulk-actions)**

Additionally, you may use any other feature of the **[table builder](https://filamentphp.com/docs/2.x/tables)**.

**Listing with pivot attributes**

For `BelongsToMany` and `MorphToMany` relationships, you may also add pivot table attributes. For example, if you have a `TeamsRelationManager` for your `UserResource`, and you want to add the `role` pivot attribute to the table, you can use:

```php
public static function table(Table $table): Table
{
    return $table
        ->columns([
            Tables\Columns\TextColumn::make('name'),
            Tables\Columns\TextColumn::make('role'),
        ]);
}
```

Please ensure that any pivot attributes are listed in the `withPivot()` method of the relationship *and* inverse relationship.

### Creating/Editing records

Creating and editing records can be customized in various ways and filament provides us function to do that like (customizing before filling, customizing before saving, customizing the saving process, customizing notifications and many lifecycle hooks).

**Creating/Editing with pivot attributes**

For `BelongsToMany` and `MorphToMany` relationships, you may also edit pivot table attributes. For example, if you have a `TeamsRelationManager` for your `UserResource`, and you want to add the `role` pivot attribute to the edit form, you can use:

```php
public static function form(Form $form): Form
{
    return $form
        ->schema([
            Forms\Components\TextInput::make('name')->required(),
            Forms\Components\TextInput::make('role')->required(),
            // ...
        ]);
}
```

Please ensure that any pivot attributes are listed in the `withPivot()` method of the relationship *and* inverse relationship.

### Attaching and detaching records

Filament is able to attach and detach records for `BelongsToMany` and `MorphToMany` relationships.

When generating your relation manager, you may pass the `--attach` flag to also add `AttachAction`, `DetachAction` and `DetachBulkAction` to the table:

```php
php artisan make:filament-relation-manager CategoryResource posts title --attach
```

Alternatively, if you've already generated your resource, you can just add the actions to the `$table` arrays:

```php
public static function table(Table $table): Table
{
    return $table
        ->columns([
            // ...
        ])
        ->headerActions([
            // ...
            Tables\Actions\AttachAction::make(),
        ])
        ->actions([
            // ...
            Tables\Actions\DetachAction::make(),
        ])
        ->bulkActions([
            // ...
            Tables\Actions\DetachBulkAction::make(),
        ]);
}
```

**Preloading the attachment modal select options**

By default, as you search for a record to attach, options will load from the database via AJAX. If you wish to preload these options when the form is first loaded instead, you can use the `preloadRecordSelect()` method of `AttachAction`:
AttachAction::make()->preloadRecordSelect()

```php
AttachAction::make()->preloadRecordSelect()
```

**Attaching with pivot attributes**

When you attach record with the `Attach` button, you may wish to define a custom form to add pivot attributes to the relationship:

```php
AttachAction::make()
    ->form(fn (AttachAction $action): array => [
        $action->getRecordSelect(),
        Forms\Components\TextInput::make('role')->required(),
    ])
```

In this example, `$action->getRecordSelect()` outputs the select field to pick the record to attach. The `role` text input is then saved to the pivot table's `role` column.

Please ensure that any pivot attributes are listed in the `withPivot()` method of the relationship *and* inverse relationship.

**Handling duplicates**

By default, you will not be allowed to attach a record more than once. This is because you must also set up a primary `id` column on the pivot table for this feature to work.

Please ensure that the `id` attribute is listed in the `withPivot()` method of the relationship *and* inverse relationship.

Finally, add the `$allowsDuplicates` property to the relation manager:

```
protected bool $allowsDuplicates = true;

```

### Associating and diassociating records

Filament is able to associate and dissociate records for `HasMany`, `HasManyThrough` and `MorphMany` relationships.

When generating your relation manager, you may pass the `--associate` flag to also add `AssociateAction`, `DissociateAction` and `DissociateBulkAction` to the table:

```php
php artisan make:filament-relation-manager CategoryResource posts title --associate
```

Alternatively, if you've already generated your resource, you can just add the actions to the `$table` arrays:

```php
public static function table(Table $table): Table
{
    return $table
        ->columns([
            // ...
        ])
        ->headerActions([
            // ...
            Tables\Actions\AssociateAction::make(),
        ])
        ->actions([
            // ...
            Tables\Actions\DissociateAction::make(),
        ])
        ->bulkActions([
            // ...
            Tables\Actions\DissociateBulkAction::make(),
        ]);
}
```

**Preloading the associate modal select options**

By default, as you search for a record to associate, options will load from the database via AJAX. If you wish to preload these options when the form is first loaded instead, you can use the `preloadRecordSelect()` method of `AssociateAction`:

```php
AssociateAction::make()->preloadRecordSelect()
```

### Viewing Records

When generating your relation manager, you may pass the `--view` flag to also add a `ViewAction` to the table:

```php
php artisan make:filament-relation-manager CategoryResource posts title --view
```

Alternatively, if you've already generated your resource, you can just add the `ViewAction` to the `$table->actions()` array:

```php
public static function table(Table $table): Table
{
    return $table
        ->columns([
            // ...
        ])
        ->actions([
            Tables\Actions\ViewAction::make(),
            // ...
        ]);
}
```

### Grouping Relation Managers

You may choose to group relation managers together into one tab. To do this, you may wrap multiple managers in a `RelationGroup` object, with a label:

```php
public static function getRelations(): array
{
    return [
        // ...
        RelationGroup::make('Contacts', [
            RelationManagers\IndividualsRelationManager::class,
            RelationManagers\OrganizationsRelationManager::class,
        ]),
        // ...
    ];
}
```

### Conditional Visibility

By default, relation managers will be visible if the `viewAny()` method for the related model policy returns `true`.

You may use the `canViewForRecord()` method to determine if the relation manager should be visible for a specific owner record:

```php
public static function canViewForRecord(Model $ownerRecord): bool
{
    return $ownerRecord->status === Status::Draft;
}
```

### Moving the resource form to tabs

On the Edit or View page class, override the `hasCombinedRelationManagerTabsWithForm()` method:

```php
public function hasCombinedRelationManagerTabsWithForm(): bool
{
    return true;
}
```

# Resource Features

## Global Search

### Titles

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

### Multi Column Search

If you would like to search across multiple columns of your resource, you may override the `getGloballySearchableAttributes()` method. "Dot-syntax" allows you to search inside of relationships:

```php
public static function getGloballySearchableAttributes(): array
{
    return ['title', 'slug', 'author.name', 'category.name'];
}
```

### Details

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

### URL

Global search results will link to the **[Edit page](https://filamentphp.com/docs/2.x/admin/resources/editing-records)** of your resource, or the **[View page](https://filamentphp.com/docs/2.x/admin/resources/viewing-page)** if the user does not have **[edit permissions](https://filamentphp.com/docs/2.x/admin/resources/editing-records#authorization)**. To customize this, you may override the `getGlobalSearchResultUrl()` method and return a route of your choice:

```php
public static function getGlobalSearchResultUrl(Model $record): string
{
    return UserResource::getUrl('edit', ['record' => $record]);
}
```

### Actions

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

## Widgets

### Getting Started

Filament allows you to display widgets inside pages, below the header and above the footer.

You can use an existing **[dashboard widget](https://filamentphp.com/docs/2.x/admin/dashboard)**, or create one specifically for the resource.

### Creating a resource Widget

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

### Displaying a widget on a resource page

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

### Accessing the current record

If you're using a widget on an **[Edit](https://filamentphp.com/docs/2.x/admin/resources/editing-records)** or **[View](https://filamentphp.com/docs/2.x/admin/resources/viewing-records)** page, you may access the current record by defining a `$record` property on the widget class:

```php
public ?Model $record = null;
```

## Custom Pages for Resources

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

## Resource Security

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

# Pages

## Getting Started

### Creating a page

To create a new page, you can use:

```php
php artisan make:filament-page Settings
```

This command will create two files - a page class in the `/Pages` directory of the Filament directory, and a view in the `/pages` directory of the Filament views directory.

Page classes are all full-page **[Livewire](https://laravel-livewire.com/)** components with a few extra utilities you can use with the admin panel.

### conditionally hide pages in navigation

You can prevent pages from appearing in the menu by overriding the `shouldRegisterNavigation()` method in your Page class. This is useful if you want to control which users can see the page in the sidebar.

```php
protected static function shouldRegisterNavigation(): bool
{
    return auth()->user()->canManageSettings();
}
```

Please be aware that all users will still be able to visit this page through its direct URL, so to fully limit access you must also also check in the `mount()` method of the page:

```php
public function mount(): void
{
    abort_unless(auth()->user()->canManageSettings(), 403);
}
```

### customization

Filament will automatically generate a title, navigation label and URL (slug) for your page based on its name. You may override it using static properties of your page class:

```php
protected static ?string $title = 'Custom Page Title';
 
protected static ?string $navigationLabel = 'Custom Navigation Label';
 
protected static ?string $slug = 'custom-url-slug';
```

You may also specify a custom header and footer view for any page. You may return them from the `getHeader()` and `getFooter()` methods:

```php
protected function getHeader(): View
{
    return view('filament.settings.custom-header');
}
 
protected function getFooter(): View
{
    return view('filament.settings.custom-footer');
}
```

## Actions

### Getting Started

"Actions" are buttons that are displayed next to the page's heading, and allow the user to run a Livewire method on the page or visit a URL.

To define actions for a page, use the `getActions()` method:

```php
protected function getActions(): array
{
    return [
        Action::make('settings')->action('openSettingsModal'),
				/* Change Label */
				->label('Settings')
				/* Open URL */
				->url(route('settings')),
				/* Change Appearance */
				->color('secondary')
				->icon('heroicon-s-cog')
				->size('lg'), // `sm`, `md` or `lg`
    ];
}
 
public function openSettingsModal(): void
{
    $this->dispatchBrowserEvent('open-settings-modal');
}
```

Actions in pages share all the same features of normal actions inside the table builder:

- Modal
- Grouping
- Keybindings

## Widgets

Filament allows you to display **[widgets](https://filamentphp.com/docs/2.x/admin/dashboard)** inside pages, below the header and above the footer.

To add a widget to a page, use the `getHeaderWidgets()` or `getFooterWidgets()` methods:

```php
protected function getHeaderWidgets(): array
{
    return [
        StatsOverviewWidget::class
    ];
}
```

`getHeaderWidgets()` returns an array of widgets to display above the page content, whereas `getFooterWidgets()` are displayed below.

If you'd like to learn how to build and customize widgets, check out the **[Dashboard](https://filamentphp.com/docs/2.x/admin/dashboard)** documentation section.

### Customizing the widgets in Grid

You may change how many grid columns are used to display widgets.

You may override the `getHeaderWidgetsColumns()` or `getFooterWidgetsColumns()` methods to return a number of grid columns to use:

```php
protected function getHeaderWidgetsColumns(): int | array
{
    return 3;
}
```

### Responsive widgets grid

You may wish to change the number of widget grid columns based on the responsive **[breakpoint](https://tailwindcss.com/docs/responsive-design#overview)** of the browser. You can do this using an array that contains the number of columns that should be used at each breakpoint:

```php
protected function getHeaderWidgetsColumns(): int | array
{
    return [
        'md' => 4,
        'xl' => 5,
    ];
}
```

# Navigation

## Getting Started

By default, Filament will register navigation items for each of your **[resources](https://filamentphp.com/docs/2.x/admin/resources)** and **[custom pages](https://filamentphp.com/docs/2.x/admin/pages)**. These classes contain static properties and methods that you can override, to configure that navigation item and its order:

```php
protected static ?string $navigationIcon = 'heroicon-o-document-text';
 
protected static ?string $navigationLabel = 'Custom Navigation Label';
 
protected static ?int $navigationSort = 3;
```

The `$navigationIcon` supports the name of any Blade component, and passes a set of formatting classes to it. By default, the **[Blade Heroicons](https://github.com/blade-ui-kit/blade-heroicons)** package is installed, so you may use the name of any **[Heroicon](https://heroicons.com/)** out of the box. However, you may create your own custom icon components or install an alternative library if you wish.

## Navigation Item Badges

To add a badge next to the navigation item, you can use the `getNavigationBadge()` method and return the content of the badge:

```php
protected static function getNavigationBadge(): ?string
{
    return static::getModel()::count();
}
```

If a badge value is returned by `getNavigationBadge()`, it will display using the primary Tailwind color by default. To style the badge contextually, return either `danger`, `warning`, `success` or `secondary` from the `getNavigationBadgeColor()` method:

```php
protected static function getNavigationBadgeColor(): ?string
{
    return static::getModel()::count() > 10 ? 'warning' : 'primary';
}
```

## Grouping Navigation Items

You may group navigation items by specifying a `$navigationGroup` property on a **[resource](https://filamentphp.com/docs/2.x/admin/resources)** and **[custom page](https://filamentphp.com/docs/2.x/admin/pages)**:

```php
protected static ?string $navigationGroup = 'Settings';
```

All items in the same navigation group will be displayed together under the same group label, "Settings" in this case. Ungrouped items will remain at the top of the sidebar.

### Customizing Navigation Groups

You may customize navigation groups by calling `Filament::registerNavigationGroups()` from the `boot()` method of any service provider, and passing `NavigationGroup` objects in order:

```php
class AppServiceProvider extends ServiceProvider
{
    public function boot(): void
    {
        Filament::serving(function () {
            Filament::registerNavigationGroups([
                NavigationGroup::make()
                     ->label('Shop')
                     ->icon('heroicon-s-shopping-cart'),
                NavigationGroup::make()
                    ->label('Blog')
                    ->icon('heroicon-s-pencil'),
                NavigationGroup::make()
                    ->label('Settings')
                    ->icon('heroicon-s-cog')
                    ->collapsed(),
            ]);
        });
    }
}
```

In this example, we pass in a custom `icon()` for the groups, and make one `collapsed()` by default.

**Ordering navigation groups**

By using `registerNavigationGroups()`, you are defining a new order for the navigation groups in the sidebar. If you just want to reorder the groups and not define an entire `NavigationGroup` object, you may just pass the labels of the groups in the new order:

```php
Filament::registerNavigationGroups([
    'Shop',
    'Blog',
    'Settings',
])
```

## Registering Custom Navigation Items

You may register custom navigation items by calling `Filament::registerNavigationItems()` from the `boot()` method of any service provider:

```php
Filament::serving(function () {
    Filament::registerNavigationItems([
        NavigationItem::make('Analytics')
            ->url('https://filament.pirsch.io', shouldOpenInNewTab: true)
            ->icon('heroicon-o-presentation-chart-line')
            ->group('Reports')
            ->sort(3),
    ]);
});
```

## Disabling Resource or Page Navigation Items

To prevent resources or pages from showing up in navigation, you may use:

```php
protected static bool $shouldRegisterNavigation = false;
```

## Advanced Navigation Customization

Details about more customization [here](https://filamentphp.com/docs/2.x/admin/navigation#advanced-navigation-customization)

## Customizing the User Menu

Details about that [here](https://filamentphp.com/docs/2.x/admin/navigation#customizing-the-user-menu)

# Dashboard

# Appearance

# Users

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