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

## Columns Options

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

### Reordering Records

To allow the user to reorder records using drag and drop in your table, you can use the `reorderable()` method:

```php
return $table
    // ...
    ->reorderable('sort');

```

When making the table reorderable, a new button will be available on the table to toggle reordering. Pagination will be disabled in reorder mode to allow you to move records between pages. The `reorderable()` method passes in the name of a column to store the record order in.

### Polling Content

You may poll table content so that it refreshes at a set interval, using the `poll()` method:

```php
  return $table
      // ...
      ->poll('10s');
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

## Filters

Filters allow you to scope the Eloquent query as a way to reduce the number of records in a table.

```php
public static function table(Table $table): Table
{
    return $table
        ->filters([
            // ...
        ]);
}
```

Filters may be created using the static `make()` method, passing its name. The name of the filter should be unique. You should then pass a callback to `query()` which applies your filter's scope:

```php
Filter::make('is_featured')
    ->query(fn (Builder $query): Builder => $query->where('is_featured', true))
```

### Filter Options

Similar to all other components in Filament, we can modify a number of label’s options

```php
Filter::make('is_featured')->label('Featured') // change label
->translateLabel() // Translated label
->toggle() // switch the filter control from checkbox to toggle
->default() // set a filter to be enabled by default
```

### Select / MultiSelect filters

By default, filters have two states: enabled and disabled. When the filter is enabled, it is applied to the query. When it is disabled it is not. This is controlled through a checkbox. However, some filters may require extra data input to narrow down the results further. You may use a custom filter form to collect this data.

```php
SelectFilter::make('status') // or MultiSelectFilter
    ->options([
        'draft' => 'Draft',
        'reviewing' => 'Reviewing',
        'published' => 'Published',
    ])
/* To customize the attirbute */
    ->attribute('status_id')
```

### Relationship Select Filters

Select filters are also able to automatically populate themselves based on a `BelongsTo` relationship. For example, if your table has a `author` relationship with a `name` column, you may use `relationship()` to filter the records belonging to an author:

```php
SelectFilter::make('author')->relationship('author', 'name') // or MultiSelect

/* We can customize the relationship query */
->relationship('author', 'name', fn (Builder $query) => $query->withTrashed())
```

### Ternary Filters

Ternary filters allow you to quickly create a filter which has three states - usually true, false and blank. To filter a column named `is_admin` to be `true` or `false`, you may use the ternary filter:

```php
TernaryFilter::make('is_admin')
```

Another common pattern is to use a nullable column. For example, when filtering verified and unverified users using the `email_verified_at` column, unverified users have a null timestamp in this column. To apply that logic, you may use the `nullable()` method:

```php
TernaryFilter::make('email_verified_at')
    ->nullable()
/* Customize attirbute */
->attribute('status_id')
/* Customize Query */
->placeholder('Without trashed records')
    ->trueLabel('With trashed records')
    ->falseLabel('Only trashed records')
    ->queries(
        true: fn (Builder $query) => $query->withTrashed(),
        false: fn (Builder $query) => $query->onlyTrashed(),
        blank: fn (Builder $query) => $query->withoutTrashed(),
    )
```

### Custom Filter Forms

You may use components from the **[Form Builder](https://filamentphp.com/docs/forms/fields)** to create custom filter forms. The data from the custom filter form is available in the `$data` array of the `query()` callback:

```php
Filter::make('created_at')
    ->form([
        Forms\Components\DatePicker::make('created_from'),
        Forms\Components\DatePicker::make('created_until'),
    ])
    ->query(function (Builder $query, array $data): Builder {
        return $query
            ->when(
                $data['created_from'],
                fn (Builder $query, $date): Builder => $query->whereDate('created_at', '>=', $date),
            )
            ->when(
                $data['created_until'],
                fn (Builder $query, $date): Builder => $query->whereDate('created_at', '<=', $date),
            );
    })

/* Set default Value */
Filter::make('created_at')
    ->form([
        Forms\Components\DatePicker::make('created_from'),
        Forms\Components\DatePicker::make('created_until')->default(now()),
    ])
```

### Active Indicators

When a filter is active, an indicator is displayed above the table content to signal that the table query has been scoped.

By default, the label of the filter is used as the indicator. You can override this:

```php
TernaryFilter::make('is_admin')
    ->label('Administrators only?')
    ->indicator('Administrators')
```

### Custom Indicators

Not all indicators are simple, so you may need to use `indicateUsing()` to customize which indicators should be shown at any time.

For example, if you have a custom date filter, you may create a custom indicator that formats the selected date:

```php
Filter::make('created_at')
    ->form([DatePicker::make('date')])
    // ...
    ->indicateUsing(function (array $data): ?string {
        if (! $data['date']) {
            return null;
        }
 
        return 'Created at ' . Carbon::parse($data['date'])->toFormattedDateString();
    })
```

You may even render multiple indicators at once, by returning an array. If you have different fields associated with different indicators, you should use the field's name as the array key, to ensure that the correct field is reset when the filter is removed:

```php
Filter::make('created_at')
    ->form([
        DatePicker::make('from'),
        DatePicker::make('until'),
    ])
    // ...
    ->indicateUsing(function (array $data): array {
        $indicators = [];
 
        if ($data['from'] ?? null) {
            $indicators['from'] = 'Created from ' . Carbon::parse($data['from'])->toFormattedDateString();
        }
 
        if ($data['until'] ?? null) {
            $indicators['until'] = 'Created until ' . Carbon::parse($data['until'])->toFormattedDateString();
        }
 
        return $indicators;
    })
```

### Appearance

By default, filters are displayed in a thin popover on the right side of the table, in 1 column.

To change the number of columns that filters may occupy, you may use the `getTableFiltersFormColumns()` method:

```php
protected function getTableFiltersFormColumns(): int
{
    return 3;
}
```

Adding more columns to the filter form will automatically widen the popover. To customize the popover width, you may use the `getTableFiltersFormWidth()` method, and specify a width from `xs` to `7xl`:

```php
protected function getTableFiltersFormWidth(): string
{
    return '4xl';
}
```

### ****Displaying filters above or below the table content****

To render the filters above the table content instead of in a popover, you may use:

```php
protected function getTableFiltersLayout(): ?string
{
    return Layout::AboveContent;
}
```

To render the filters below the table content instead of in a popover, you may use:

```php
protected function getTableFiltersLayout(): ?string
{
    return Layout::BelowContent;
}
```

### Persist Filters in Session

To persist the table filters in the user's session, use the `shouldPersistTableFiltersInSession()` method:

```php
protected function shouldPersistTableFiltersInSession(): bool
{
    return true;
}
```

## Actions

Single action buttons are rendered at the end of each table row.

Actions may be created using the static `make()` method, passing its name. The name of the action should be unique. You can then pass a callback to `action()` which executes the task, or a callback to `url()` which generates a link URL:

```php
Action::make('edit')
    ->url(fn (Post $record): string => route('posts.edit', $record))
    ->openUrlInNewTab()
```

Bulk action buttons are visible when the user selects at least one record.

Bulk actions may be created using the static `make()` method, passing its name. The name of the action should be unique. You should then pass a callback to `action()` which executes the task:

```php
BulkAction::make('delete')
    ->action(fn (Collection $records) => $records->each->delete())
```

### Action Options

Actions have multiple options to modify as follows:

```php
->label('Edit post') // change label
->translateLabel() // Translated label
->color('danger') // color
->icon('heroicon-o-trash') // icon
```

### ****Confirmation modals****

You may require confirmation before an action is run using the `requiresConfirmation()` method. This is useful for particularly destructive actions, such as those that delete records.

```php
->requiresConfirmation()
```

### Custom Forms

You may also render a form in this modal to collect extra information from the user before the action runs. Details [Here](https://filamentphp.com/docs/2.x/tables/actions#custom-forms)

### Authorization

You may conditionally show or hide actions and bulk actions for certain users using either the `visible()` or `hidden()` methods, passing a closure:

```php
Action::make('edit')
    ->url(fn (Post $record): string => route('posts.edit', $record))
    ->visible(fn (Post $record): bool => auth()->user()->can('update', $record))
```

### Prebuilt Actions

Filament offers the main actions (View, Edit, Delete) as well as a **Replicate Action**

This package includes an action to replicate table records. You may use it like so:

```php
ReplicateAction::make()
/* To exclude specific attributes from being replicated */
->excludeAttributes(['slug'])
/* Inboke Closure before or after save */
->beforeReplicaSaved(function (Model $replica): void {
        // ...
    })
->afterReplicaSaved(function (Model $replica): void {
        // ...
    })
/* Show popup for the user to fill in some fields manually */
->form([
        TextInput::make('title')->required(),
    ])
```

### Grouping

You may use an `ActionGroup` object to group multiple table actions together in a dropdown:

```php
  Tables\Actions\ActionGroup::make([
		Tables\Actions\ViewAction::make(),
		Tables\Actions\EditAction::make(),
		Tables\Actions\DeleteAction::make(),
]),

```

### Position

By default, the row actions in your table are rendered in the final cell. You may change the position by overriding the `getTableActionsPosition()` method:

```php
protected function getTableActionsPosition(): ?string
{
    return Position::BeforeCells;
}
```

### Alignment

Row actions are aligned to the right in their cell by default. To change the alignment, update the configuration value inside of the package config:

```php
'actions' => [
    'cell' => [
        'alignment' => 'right', // `right`, `left` or `center`
    ],
]
```

### Tooltips

You may specify a tooltip to display when you hover over an action:

```php
->tooltip('Edit this blog post')
/* or use closure to access current table record */
->tooltip(fn (Model $record): string => "Edit {$record->title}")
```

## Layout

Table layout always suffer on mobile from the responsive issue, so Filament provides two out of the box features to help

- Allow the user to scroll horizontally to see more table content
- Hide non-important columns on smaller devices

    ```php
    TextColumn::make('slug')->visibleFrom('md')
    ```


### Allowing Columns to Stack on Mobile

Filament provides split component to allow stacking on mobile

```php
Split::make([
    ImageColumn::make('avatar'),
    TextColumn::make('name'),
    TextColumn::make('email'),
])
/* Select the breakpoint */
->from('md')
/* We can stop specific column from growing to fill up the space */
ImageColumn::make('avatar')->grow(false),
```

Inside a split, you may stack multiple columns on top of each other vertically. This allows you to display more data inside fewer columns on desktop:

```php
Split::make([
    ImageColumn::make('avatar'),
    TextColumn::make('name'),
    Stack::make([
        TextColumn::make('email'),
        TextColumn::make('phone'),
    ]),
])
```

We can manipulate the stacked columns (show, hide them) on specific breakpoint or change alignment

```php
Stack::make([

    ])->alignment('right')->visibleFrom('md'),,
```

### Collapsible Content

When you're using a column layout like split or stack, then you can also add collapsible content. This is very useful for when you don't want to display all data in the table at once, but still want it to be accessible to the user if they need to access it, without navigating away.

Split and stack components can be made `collapsible()`, but there is also a dedicated `Panel` component that provides a pre-styled background color and border radius, to separate the collapsible content from the rest:

```php
Panel::make([
        Stack::make([
            TextColumn::make('email'),
            TextColumn::make('phone'),
        ]),
    ])->collapsible(),
```

We need to add the animation manually details [here](https://filamentphp.com/docs/2.x/tables/layout#adding-a-collapse-animation)

### Arranging Records into a grid

Sometimes, you may find that your data fits into a grid format better than a list. Filament can handle that too!

```php
return $table
        ->contentGrid([
            'md' => 2,
            'xl' => 3,
        ]);
```

### Custom HTML

You may add custom HTML to your table using a `View` component. It can even be `collapsible()`

Details [Here](https://filamentphp.com/docs/2.x/tables/layout#custom-html)