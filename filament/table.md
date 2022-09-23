# Table Builder

## Table Structure

Resource classes contain a static `table()` method that is used to build the table on the **[List page](https://filamentphp.com/docs/2.x/admin/resources/listing-records)**:

```php
public static function table(Table $table): Table
{
    return $table
        ->columns([
            Tables\Columns\TextColumn::make('name'),
            Tables\Columns\TextColumn::make('email'),
            // ...
        ])
        ->filters([
            Tables\Filters\Filter::make('verified')
                ->query(fn (Builder $query): Builder => $query->whereNotNull('email_verified_at')),
            // ...
        ])
        ->actions([
            Tables\Actions\EditAction::make(),
        ])
        ->bulkActions([
            Tables\Actions\DeleteBulkAction::make(),
        ]);
}
```

## Columns

Columns may be created using the static `make()` method, passing its name. The name of the column should correspond to a column or accessor on your model. You may use "dot syntax" to access columns within relationships.

```php
TextColumn::make('title')
```

### Setting a Label

By default, the label of the column, which is displayed in the header of the table, is generated from the name of the column. You may customize this using the `label()` method:

```php
TextColumn::make('title')->label('Post title')
/* Automatically load translated label */
->translateLabel()
```

### Sorting

Columns may be sortable, by clicking on the column label. To make a column sortable, you must use the `sortable()` method:

```php
TextColumn::make('title')->sortable()
```

custom sort is available [here](https://filamentphp.com/docs/2.x/tables/columns/getting-started#sorting)

### Searching

Columns may be searchable, by using the text input in the top right of the table. To make a column searchable, you must use the `searchable()` method:

```php
TextColumn::make('title')->searchable()
/* Create individual search */
->searchable(isIndividual: true)
/* Stop global search */
->searchable(isIndividual: true, isGlobal: false)

```

### Cell Actions and URLs

When a cell is clicked, you may run an "action", or open a URL.

**Running Actions**

To run an action, you may use the `action()` method, passing a callback or the name of a Livewire method to run. Each method accepts a `$record` parameter which you may use to customize the behaviour of the action:

```php
TextColumn::make('title')
    ->action(function (Post $record): void {
        $this->dispatchBrowserEvent('open-post-edit-modal', [
            'post' => $record->getKey(),
        ]);
    })
```

**Action Modals**

You may open **[action modals](https://filamentphp.com/docs/2.x/tables/columns/actions#modals)** by passing in an `Action` object to the `action()` method:

```php
TextColumn::make('title')
    ->action(
        Action::make('select')
            ->requiresConfirmation()
            ->action(function (Post $record): void {
                $this->dispatchBrowserEvent('select-post', [
                    'post' => $record->getKey(),
                ]);
            }),
    )
```

Action objects passed into the `action()` method must have a unique name to distinguish it from other actions within the table.

**Opening URLs**

To open a URL, you may use the `url()` method, passing a callback or static URL to open. Callbacks accept a `$record` parameter which you may use to customize the URL:

```php
TextColumn::make('title')
    ->url(fn (Post $record): string => route('posts.edit', ['post' => $record]))
/* Can be opened in a New Tab */
		->openUrlInNewTab()
```

### Setting a default value

To set a default value for fields with a `null` state, you may use the `default()` method:

```php
TextColumn::make('title')->default('Untitled')
```

### Hiding Columns

To hide a column conditionally, you may use the `hidden()` and `visible()` methods, whichever you prefer:

```php
TextColumn::make('role')->hidden(! auth()->user()->isAdmin())
/* Toggle Column Visibility */
->toggleable()
/* Hide By Default */
->toggleable(isToggledHiddenByDefault: true)
```

### Tooltips

You may specify a tooltip to display when you hover over a cell:

```php
TextColumn::make('title')
    ->tooltip('Title')
/* Use closure to access current table record */
    ->tooltip(fn (Model $record): string => "By {$record->author->name}")
```

### Custom Attributes

The HTML of columns can be customized, by passing an array of `extraAttributes()`:

```php
TextColumn::make('slug')->extraAttributes(['class' => 'bg-gray-200'])
```

### Global Settings

If you wish to change the default behaviour of all columns globally, then you can call the static `configureUsing()` method inside a service provider's `boot()` method, to which you pass a Closure to modify the columns using. For example, if you wish to make all columns **`[sortable()](https://filamentphp.com/docs/2.x/tables/columns/getting-started#sorting)`** and **`[toggleable()](https://filamentphp.com/docs/2.x/tables/columns/getting-started#toggling-column-visibility)`**, you can do it like so:

```php
Column::configureUsing(function (Column $column): void {
    $column
        ->toggleable()
        ->sortable();
});
```

Additionally, you can call this code on specific column types as well:

```php
BooleanColumn::configureUsing(function (BooleanColumn $column): void {
    $column
        ->toggleable()
        ->sortable();
});
```

Of course, you are still able to overwrite this on each column individually:

```php
BooleanColumn::make('is_admin')->toggleable(false)
```

## Column Types

### Text Column

Text columns display simple text from your database:

```php
TextColumn::make('title')
/* Display description */
->description(fn (Post $record): string => $record->description)
/* Change Description to above */
->description(fn (Post $record): string => $record->description, position: 'above')
/* Date Formatting */
->dateTime()
/* we can use since as well to show difference between value and now */
->since()
/* Currency Format */
>money('eur')
/* Limit Text length */
->limit(50)
/* Wrap content if it's too long */
->wrap()
/* HTML Format */
->html()
/* Cutsom Format */
->formatStateUsing(fn (string $state): string => __("statuses.{$state}"))
```

Also we have option to customize style

```php
->color('primary')
->icon('heroicon-s-mail')
->iconPosition('after') // `before` or `after`
->size('sm') // Text Size, we can use lg
->weight('medium') // font weight, we can use bold
```

### Boolean Column

Boolean columns display a check or cross icon based on the contents of the database column, either true or false:

```php
BooleanColumn::make('is_featured')
/* Customize Icons */
		->trueIcon('heroicon-o-badge-check')
    ->falseIcon('heroicon-o-x-circle')
/* Customize Colors */
		->trueColor('primary')
    ->falseColor('warning')

```

### Images Column

Images can be easily displayed within your table, The column in the database must contain the path to the image, relative to the root directory of its storage disk.

```php
ImageColumn::make('header_image')
->disk('s3') // choose disk
->visibility('private') // Visibility
->rounded() // Rounded images
->width(200) // define width
->height(50) // height
->size(40) // size
->extraImgAttributes(['title' => 'Company logo']) // custom attributes
```

### Icon Column

Icon columns render a Blade icon component representing their contents:

```php
IconColumn::make('is_featured')
    ->options([
        'heroicon-o-x-circle',
        'heroicon-o-pencil' => 'draft',
        'heroicon-o-clock' => 'reviewing',
        'heroicon-o-check-circle' => 'published',
    ])
```

You may also pass a callback to activate an option, accepting the cell's `$state`:

```php
IconColumn::make('is_featured')
    ->options([
        'heroicon-o-x-circle',
        'heroicon-o-pencil' => fn ($state): bool => $state === 'draft',
        'heroicon-o-clock' => fn ($state): bool => $state === 'reviewing',
        'heroicon-o-check-circle' => fn ($state): bool => $state === 'published',
    ])
```

Icon columns may also have a set of icon colors, using the same syntax. They may be either `primary`, `secondary`, `success`, `warning` or `danger`:

```php
->colors([
        'secondary',
        'danger' => 'draft',
        'warning' => 'reviewing',
        'success' => 'published',
    ])
```

### Badge Column

Badge columns render a colored badge with the cell's contents:

```php
BadgeColumn::make('status')
    ->enum([
        'draft' => 'Draft',
        'reviewing' => 'Reviewing',
        'published' => 'Published',
    ])
```

Badges can take same state and color options as icon column, but we have the option to dynamically calculate state

```php
BadgeColumn::make('status')
    ->icon(static function ($state): string {
        if ($state === 'published') {
            return 'success';
        }
 
        return 'secondary';
    })
```

Also it accepts icons, and icons accept same options  (state, color, dynamic state)

```php
BadgeColumn::make('status')
    ->icons([
        'heroicon-o-x',
        'heroicon-o-document' => 'draft',
        'heroicon-o-refresh' => 'reviewing',
        'heroicon-o-truck' => 'published',
    ])
/* Icon Position */
->iconPosition('after') // `before` or `after`
```

### Tags Column

Tags columns render a list of tags from an array:

```php
TagsColumn::make('tags')
```

Tags need to be casted when using it in forms or we can store it with separator (more explained in forms).

### Color Column

The color column allows you to show the color preview from a CSS color definition, typically entered using the color picker field, in one of the supported formats (HEX, HSL, RGB, RGBA).

```php
ColorColumn::make('color')
/* Allow copying */
		->copyable()
    ->copyMessage('Color code copied')
    ->copyMessageDuration(1500)
```

### Select Column

The select column allows you to render a select field inside the table, which can be used to update that database record without needing to open a new page or a modal.

You must pass options to the column:

```php
SelectColumn::make('status')
    ->options([
        'draft' => 'Draft',
        'reviewing' => 'Reviewing',
        'published' => 'Published',
    ])

/* Validation */
    ->rules(['required'])
/* disable placeholder selection */
		->disablePlaceholderSelection()
```

### Toggle Column

The toggle column allows you to render a toggle button inside the table, which can be used to update that database record without needing to open a new page or a modal:

```php
ToggleColumn::make('is_admin')
```

### Text Input Column

The text input column allows you to render a text input inside the table, which can be used to update that database record without needing to open a new page or a modal:

```php
TextInputColumn::make('name')
/* Validation */
->rules(['required', 'max:255'])
/* HTML Input Type */
->type('color')
```

### Checkbox Column

The checkbox column allows you to render a checkbox inside the table, which can be used to update that database record without needing to open a new page or a modal:

```php
CheckboxColumn::make('is_admin')
```

### Custom Relationships

You may use "dot syntax" to access columns within relationships. The name of the relationship comes first, followed by a period, followed by the name of the column to display:

```php
TextColumn::make('author.name')
/* Number of related records */
->counts('users')
/* Check if relationship exist */
->exists('users')
/* Relationship Aggregating: avg(), max(), min(), sum() */
->avg('users', 'age')
```