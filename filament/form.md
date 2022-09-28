# Basics

Resource classes contain a static `form()` method that is used to build the forms on the Create and Edit pages.

## Form Structure

The `schema()` method is used to define the structure of your form. It is an array of **[fields](https://filamentphp.com/docs/2.x/forms/fields)**, in the order they should appear in your form. Filament offers many types of fields (in the Form Section) as well as the ability to create custom form fields.

Also Filament offers layout components to group fields and also we can create custom layout components.

```php
public static function form(Form $form): Form
{
    return $form
        ->schema([
            // ...
        ]);
}
```

Fields may be created using the static `make()` method, passing its name. Which is usually equivalent to a table column in DB.

```php
TextInput::make('name')
```

# Fields

## Fields Options

Filament provides number of option to alter fields inside the form

### Setting a label

By default, the label of the field will be automatically determined based on its name. To override the field's label, you may use the `label()` method. Customizing the label in this way is useful if you wish to use a **[translation string for localization](https://laravel.com/docs/localization#retrieving-translation-strings)**:

```php
TextInput::make('name')->label(__('fields.name'))
```

Optionally, you can have the label automatically translated by using the `translateLabel()` method:

```php
TextInput::make('name')->translateLabel() // Equivalent to `label(__('Name'))`
```

### Setting an ID

In the same way as labels, field IDs are also automatically determined based on their names. To override a field ID, use the `id()` method:

```php
TextInput::make('name')->id('name-field')
```

### Validation attributes

When fields fail validation, their label is used in the error message. To customize the label used in field error messages, use the `validationAttribute()` method:

```php
TextInput::make('name')->validationAttribute('full name')
```

### Setting a default value

Fields may have a default value. This will be filled if the **[form's `fill()` method](https://filamentphp.com/docs/2.x/forms/getting-started#default-data)** is called without any arguments. To define a default value, use the `default()` method:

```php
TextInput::make('name')->default('John')
```

### Helper messages and hints

Sometimes, you may wish to provide extra information for the user of the form. For this purpose, you may use helper messages and hints.

Help messages are displayed below the field. The `helperText()` method supports Markdown formatting:

```php
TextInput::make('name')->helperText('Your full name here, including any middle names.')
```

Hints can be used to display text adjacent to its label:

```php
TextInput::make('password')->hint('[Forgotten your password?](forgotten-password)')
```

Hints may also have an icon rendered next to them:

```php
RichEditor::make('content')
    ->hint('Translatable')
    ->hintIcon('heroicon-s-translate')
```

### Custom attributes

The HTML attributes of the field's wrapper can be customized by passing an array of `extraAttributes()`:

```php
TextInput::make('name')->extraAttributes(['title' => 'Text input'])
```

To add additional HTML attributes to the input itself, use `extraInputAttributes()`:

```php
TextInput::make('points')
    ->numeric()
    ->extraInputAttributes(['step' => '10'])
```

### Disabling

You may disable a field to prevent it from being edited:

```php
TextInput::make('name')->disabled()
```

Optionally, you may pass a boolean value to control if the field should be disabled or not:

```php
Toggle::make('is_admin')->disabled(! auth()->user()->isAdmin())
```

Please note that disabling a field does not prevent it from being saved, and a skillful user could manipulate the HTML of the page and alter its value.

To prevent a field from being saved, use the `dehydrated(false)` method:

```php
Toggle::make('is_admin')->dehydrated(false)
```

Alternatively, you may only want to save a field conditionally, maybe if the user is an admin:

```php
Toggle::make('is_admin')
    ->disabled(! auth()->user()->isAdmin())
    ->dehydrated(auth()->user()->isAdmin())
```

If you're using the **[admin panel](https://filamentphp.com/docs/admin)** and only want to save disabled fields on the **[Create page of a resource](https://filamentphp.com/docs/admin/resources)**:

```php
TextInput::make('slug')
    ->disabled()
    ->dehydrated(fn (Page $livewire) => $livewire instanceof CreateRecord)
```

### Autofocusing

Most fields will be autofocusable. Typically, you should aim for the first significant field in your form to be autofocused for the best user experience.

```php
TextInput::make('name')->autofocus()
```

### Setting a placeholder

Many fields will also include a placeholder value for when it has no value. You may customize this using the `placeholder()` method:

```php
TextInput::make('name')->placeholder('John Doe')
```

## Field Types

### Text Input

The text input allows you to interact with a string, with all the other available option:

```php
TextInput::make('name')
/* Define type */
		->email()
    ->numeric()
    ->password()
    ->tel()
    ->url()
/* We can use type instead */
		->type('color')
/* Use Prefix and suffix */
		->prefix('https://')
    ->suffix('.com')
/*Prefix and suffix Icon */
		->prefixIcon('heroicon-o-external-link')
    ->suffixIcon('heroicon-o-external-link')
/* Min, Max or specific Length */
		->minLength(2)
    ->maxLength(255)
		->length(8)
/* Value Range for numeric fields */
		->minValue(1)
    ->maxValue(100)
/* Set or Disable Auto complete */
		->autocomplete('new-password')
		->disableAutocomplete()
/* Add data List for Auto Complete */
		->datalist([
		        'BWM',
		        'Ford'
		    ])
```

Filament also provides a very detailed masking options that can be checked [here](https://filamentphp.com/docs/2.x/forms/fields#input-masking)

### Select

The select component allows you to select from a list of predefined options:

```php
Select::make('status')
    ->options([
        'draft' => 'Draft',
        'reviewing' => 'Reviewing',
        'published' => 'Published',
    ])
/* we can fetch all data from a model */
		->options(User::all()->pluck('name', 'id'))
/* We can enable search */
		->searchable()
/* Choose Default Value */
		->default('draft')
/* Disable default */
    ->disablePlaceholderSelection()

```

### Multi-select

The multi-select component allows you to select multiple values from a list of predefined options:

```php
MultiSelect::make('technologies')
    ->options([
        'tailwind' => 'Tailwind CSS',
        'alpine' => 'Alpine.js',
        'laravel' => 'Laravel',
        'livewire' => 'Laravel Livewire',
    ])
```

These options are returned in JSON format. If you're saving them using Eloquent, you should be sure to add an `array` cast inside the model

```php
protected $casts = [
        'technologies' => 'array',
    ];
```

### Checkbox

The checkbox component allows you to interact a boolean value.

```php
Checkbox::make('is_admin')
```

If you're saving the boolean value using Eloquent, you should be sure to add a `boolean` **cast** to the model property:

```php
protected $casts = [
        'is_admin' => 'boolean',
    ];
```

### Toggle

Similar to Checkbox but can take more customization

```php
Toggle::make('is_admin')
/* Icon for both on and off state */
		->onIcon('heroicon-s-lightning-bolt')
    ->offIcon('heroicon-s-user')
/* Color for both on and Off State */
		->onColor('success')
    ->offColor('danger')
```

### Checkbox list

The checkbox list component allows you to select multiple values from a list of predefined options:

```php
CheckboxList::make('technologies')
    ->options([
        'tailwind' => 'Tailwind CSS',
        'alpine' => 'Alpine.js',
        'laravel' => 'Laravel',
        'livewire' => 'Laravel Livewire',
    ])
/* Break into columns */
		->columns(2)
```

These options are returned in JSON format. If you're saving them using Eloquent, you should be sure to add an `array` cast inside the model

```php
protected $casts = [
        'technologies' => 'array',
    ];
```

### Radio

The radio input provides a radio button group for selecting a single value from a list of predefined options:

```php
Radio::make('status')
    ->options([
        'draft' => 'Draft',
        'scheduled' => 'Scheduled',
        'published' => 'Published'
    ])
/* Add description to each option */
		->descriptions([
        'draft' => 'Is not visible.',
        'scheduled' => 'Will be visible.',
        'published' => 'Is visible.'
    ])
```

If you want a simple boolean radio button group, with "Yes" and "No" options, you can use the `boolean()` method:

```php
Radio::make('feedback')
    ->label('Do you like this post?')
    ->boolean()
/* To make it inline */
		->inline()
```

### Date-time picker

The date-time picker provides an interactive interface for selecting a date and a time.

```php
DateTimePicker::make('published_at')
DatePicker::make('date_of_birth')
TimePicker::make('alarm_at')

/* Restric Minimun and Maximum date */
		->minDate(now()->subYears(150))
    ->maxDate(now())
/* customize format */
		->format('d/m/Y')
		->displayFormat('d/m/Y') // Only for front-end
/* Disable Seconds on Time Picker */
		->withoutSeconds()
/* Timezone */
		->timezone('America/New_York')
```

### File Upload

The file upload field is based on **[Filepond](https://pqina.nl/filepond)**. By default, files will be uploaded publicly to your default storage disk.

```php
FileUpload::make('attachment')
/* Change Disk and Directory */
		->disk('s3')
    ->directory('form-attachments')
    ->visibility('private')
/* Keep Original Names */
		->preserveFilenames()
/* Restric Types or choose the predefined image type */
		->acceptedFileTypes(['application/pdf'])
		->image()
/* Select min and Max size */
		->minSize(512)
    ->maxSize(1024)
/* Resize and Crop images before upload */
		->imageCropAspectRatio('16:9')
    ->imageResizeTargetWidth('1920')
    ->imageResizeTargetHeight('1080')
/* Change Uploader Style */
		->imagePreviewHeight('250')
    ->loadingIndicatorPosition('left')
    ->panelAspectRatio('2:1')
    ->panelLayout('integrated')
    ->removeUploadedFileButtonPosition('right')
    ->uploadButtonPosition('left')
    ->uploadProgressIndicatorPosition('left')

```

You may also upload multiple files. This stores URLs in JSON, but we have to add it to $casts as an array

```php
FileUpload::make('attachments')->multiple()
/* Min and Max number of files */
		->minFiles(2)
    ->maxFiles(5)
/* Enable Re-ordering */
		->enableReordering()
/* Add a button to Open each file in a new tab */
		->enableOpen()
/* Enable Download */
		->enableDownload()
```

### Rich Editor

The rich editor allows you to edit and preview HTML content, as well as upload images.

```php
RichEditor::make('content')
```

We can customize button and enable/disable them. Also we have the ability to choose the storage of the uploaded media.

### Markdown Editor

The markdown editor allows you to edit and preview markdown content, as well as upload images.

```php
MarkdownEditor::make('content')
```

Very similar to Rich editor and has all the same features and configruations.

### Hidden

The hidden component allows you to create a hidden field in your form that holds a value.

```php
Hidden::make('token')
```

### Tags Input

The tags input component allows you to interact with a list of tags.

By default, tags are stored in JSON:

```php
TagsInput::make('tags')
/* Allow suggestions */
		->suggestions([
		        'tailwindcss',
		        'alpinejs',
		    ])
```

We need to either cast it as an array or store as a string using a separator like

```php
TagsInput::make('tags')->separator(',')
```

### Textarea

The textarea allows you to interact with a multi-line string:

```php
Textarea::make('description')
/* Change Size and Length */
		->rows(10)
    ->cols(20)
		->minLength(50)
    ->maxLength(500)
```

### Key-Value

The key-value field allows you to interact with one-dimensional JSON object:

```php
KeyValue::make('meta')
/* Add labels */
		->keyLabel('Property name')
    ->valueLabel('Property value')
/* disable Actions */
		->disableAddingRows()
    ->disableDeletingRows()
    ->disableEditingKeys()
/* Allow re-order */
		->reorderable()
/* Placeholder */
		->keyPlaceholder('Property name')
    ->valuePlaceholder('Property value')
```

### Color Picker

The color picker component allows you to pick a color in a range of formats.

By default, the component uses HEX format:

```php
ColorPicker::make('color')
```

## Advanced Fields

### Repeater

The repeater component allows you to output a JSON array of repeated form components.

```php
Repeater::make('members')
    ->schema([
        TextInput::make('name')->required(),
        Select::make('role')
            ->options([
                'member' => 'Member',
                'administrator' => 'Administrator',
                'owner' => 'Owner',
            ])
            ->required(),
    ])
    ->columns(2)
```

We recommend that you store repeater data with a `JSON` column in your database. Additionally, if you're using Eloquent, make sure that column has an `array` cast.

**Options**

Repeater has multiple options to modify

```php
Repeater::make('members')
    ->schema([
        // ...
    ])
/* Default number of items, min and max */
    ->defaultItems(3)
		->minItems(1)
    ->maxItems(10)
/* Button label */
		->createItemButtonLabel('Add member')
/* Disable action buttons */
		->disableItemCreation()
    ->disableItemDeletion()
    ->disableItemMovement()
```

**Collapsible**

The repeater may be `collapsible()` to optionally hide content in long forms:

```php
Repeater::make('qualifications')
    ->schema([
        // ...
    ])
    ->collapsible()
/* Collapsed by default */
		->collapsed()
```

**Clone Items**

You may allow repeater items to be duplicated using the `cloneable()` method:

```php
->cloneable()
```

**Grid Layout**

You may organize repeater items into columns by using the `grid()` method:

```php
->grid(2) // This method accepts the same options as the columns() method of the grid.
```

**Access Parent Values**

All form components are able to **[use `$get()` and `$set()`](https://filamentphp.com/docs/2.x/forms/advanced)** to access another field's value. However, you might experience unexpected behaviour when using this inside the repeater's schema.

This is because `$get()` and `$set()`, by default, are scoped to the current repeater item. This means that you are able to interact with another field inside that repeater item easily without knowing which repeater item the current form component belongs to.

The consequence of this, is that you may be confused when you are unable to interact with a field outside the repeater. We use `../` syntax to solve this problem - `$get('../../parent_field_name')`.

Consider your form has this data structure:

```json
[
    'client_id' => 1,

    'repeater' => [
        'item1' => [
            'service_id' => 2,
        ],
    ],
]

```

You are trying to retrieve the value of `client_id` from inside the repeater item.

`$get()` is relative to the current repeater item, so `$get('client_id')` is looking for `$get('repeater.item1.client_id')`.

You can use `../` to go up a level in the data structure, so `$get('../client_id')` is $get('repeater.client_id') and `$get('../../client_id')` is `$get('client_id')`.

### Builder

Similar to a **[repeater](https://filamentphp.com/docs/2.x/forms/fields#repeater)**, the builder component allows you to output a JSON array of repeated form components. Unlike the repeater, which only defines one form schema to repeat, the builder allows you to define different schema "blocks", which you can repeat in any order. This makes it useful for building more advanced array structures.

The primary use of the builder component is to build web page content using predefined blocks. The example below defines multiple blocks for different elements in the page content. On the frontend of your website, you could loop through each block in the JSON and format it how you wish.

```php
Builder::make('content')
    ->blocks([
        Builder\Block::make('heading')
            ->schema([
                TextInput::make('content')
                    ->label('Heading')
                    ->required(),
                Select::make('level')
                    ->options([
                        'h1' => 'Heading 1',
                        'h2' => 'Heading 2',
                        'h3' => 'Heading 3',
                        'h4' => 'Heading 4',
                        'h5' => 'Heading 5',
                        'h6' => 'Heading 6',
                    ])
                    ->required(),
            ]),
        Builder\Block::make('paragraph')
            ->schema([
                MarkdownEditor::make('content')
                    ->label('Paragraph')
                    ->required(),
            ]),
        Builder\Block::make('image')
            ->schema([
                FileUpload::make('url')
                    ->label('Image')
                    ->image()
                    ->required(),
                TextInput::make('alt')
                    ->label('Alt text')
                    ->required(),
            ]),
    ])
```

We recommend that you store builder data with a JSON column in your database. Additionally, if you're using Eloquent, make sure that column has an array cast.

As evident in the above example, blocks can be defined within the blocks() method of the component. Blocks are Builder\Block objects, and require a unique name, and a component schema:

```php
Builder::make('content')
    ->blocks([
        Builder\Block::make('heading')
            ->schema([
                TextInput::make('content')->required(),
                // ...
            ]),
        // ...
    ])
```

**Options**

Builder has multiple options to manipulate

```php
Builder\Block::make('heading')
/* Add translatable label */
		->label(__('blocks.heading'))
/* Add icon */
		->icon('heroicon-o-bookmark')
/* Min and Max Items */
    ->minItems(1)
    ->maxItems(10)
```

# Layout

## Layout Options

Layout component classes can be found in the `Filament\Form\Components` namespace.

They reside within the schema of your form, alongside any fields

### Columns

You may create multiple columns within each layout component using the `columns()` method:

```php
Card::make()->columns(2)
/* Alternatively For Grid choose columns inside the make */
Grid::make(3)
/* Select columns at breakpoints */
Grid::make([
    'default' => 1,
    'sm' => 3,
    'xl' => 6,
    '2xl' => 8,
])

```

You may specify the number of columns that any component may span in the parent grid:

```php
Grid::make(3)
    ->schema([
        TextInput::make('name')
            ->columnSpan(2),// or we can choose full
        // ...
    ])
```

### Setting an ID

You may define an ID for the component using the `id()` method:

```php
Card::make()->id('main-card')
```

### Custom Attributes

The HTML of components can be customized even further, by passing an array of `extraAttributes()`:

```php
Card::make()->extraAttributes(['class' => 'bg-gray-50'])
```

## Layout Types

### Grid

Generally, form fields are stacked on top of each other in one column. To change this, you may use a grid component:

```php
Grid::make()
    ->schema([
        // ...
    ])
```

### Fieldset

You may want to group fields into a Fieldset. Each fieldset has a label, a border, and a two-column grid by default:

```php
Fieldset::make('Label')
    ->schema([
        // ...
    ])
```

### Tabs

Some forms can be long and complex. You may want to use tabs to reduce the number of components that are visible at once:

```php
Tabs::make('Heading')
    ->tabs([
        Tabs\Tab::make('Label 1')
            ->schema([
                // ...
            ]),
        Tabs\Tab::make('Label 2')
            ->schema([
                // ...
            ]),
        Tabs\Tab::make('Label 3')
            ->schema([
                // ...
            ]),
    ])
/* Select Default tab */
	->activeTab(2)
/* Set Icon and Badge for the tab */
Tabs\Tab::make('Notifications')
            ->icon('heroicon-o-bell') 
            ->badge('39') 
            ->schema([
                // ...
            ]),

```

### Wizard

Similar to **[tabs](https://filamentphp.com/docs/2.x/forms/layout#tabs)**, you may want to use a multistep form wizard to reduce the number of components that are visible at once. These are especially useful if your form has a definite chronological order, in which you want each step to be validated as the user progresses.

```php
Wizard::make([
    Wizard\Step::make('Order')
        ->schema([
            // ...
        ]),
    Wizard\Step::make('Delivery')
        ->schema([
            // ...
        ]),
    Wizard\Step::make('Billing')
        ->schema([
            // ...
        ]),
])
/* Add icon to a step */
	->icon('heroicon-o-shopping-bag')
/* Choose the starting step */
	->startOnStep(2)
/* Make a step skippable */
	->skippable()
```

### Section

You may want to separate your fields into sections, each with a heading and description. To do this, you can use a section component:

```php
Section::make('Heading')
    ->description('Description')
    ->schema([
        // ...
    ])
/* Collapsiable */
		->collapsible()
		->collapsed()
/* Compact Style */
		->compact()
```

### Placeholder

Placeholders can be used to render text-only "fields" within your forms. Each placeholder has `content()`, which cannot be changed by the user.

```php
Placeholder::make('Label')
    ->content('Content, displayed underneath the label')
/* We can render custom HTML */
Placeholder::make('Documentation')
    ->content(new HtmlString('<a href="https://filamentphp.com/docs">filamentphp.com</a>'))
```

### Card

The card component may be used to render the form components inside a card:

```php
Card::make()
    ->schema([
        // ...
    ])
```

### View

Aside from **[building custom layout components](https://filamentphp.com/docs/2.x/forms/layout#building-custom-layout-components)**, you may create "view" components which allow you to create custom layouts without extra PHP classes.

```php
View::make('filament.forms.components.wizard')
```

Inside your view, you may render the component's schema() using the $getChildComponentContainer() closure:

```php
<div>
    {{ $getChildComponentContainer() }}
</div>
```

# Advanced

## Change Field or Layout Globally

If you wish to change the default behavior of a field or Layout globally, then you can call the static `configureUsing()` method inside a service provider's `boot()` method, to which you pass a Closure to modify the component using. For example, if you wish to make all checkboxes **`[inline(false)](https://filamentphp.com/docs/2.x/forms/fields#checkbox)`**, you can do it like so:

```php
Checkbox::configureUsing(function (Checkbox $checkbox): void {
    $checkbox->inline(false);
});
```

Of course, you are still able to overwrite this on each field individually:

```php
Checkbox::make('is_admin')->inline()
```

# Validation

Validation rules may be added to any **[field](https://filamentphp.com/docs/2.x/forms/fields)**.

Filament includes several **[dedicated validation methods](https://filamentphp.com/docs/2.x/forms/validation#available-rules)**, but you can also use any **[other Laravel validation rules](https://filamentphp.com/docs/2.x/forms/validation#other-rules)**, including **[custom validation rules](https://filamentphp.com/docs/2.x/forms/validation#custom-rules)**

## Available Rules

Full List of All validation Rules Here [https://filamentphp.com/docs/2.x/forms/validation#available-rules](https://filamentphp.com/docs/2.x/forms/validation#available-rules)

## Other Rules

You may add other validation rules to any field using the `rules()` method:

```php
TextInput::make('slug')->rules(['alpha_dash'])
```

A full list of validation rules may be found in the **[Laravel documentation](https://laravel.com/docs/validation#available-validation-rules)**.

## Custom Rules

You may use any custom validation rules as you would do in **[Laravel](https://laravel.com/docs/validation#custom-validation-rules)**:

```php
TextInput::make('slug')->rules([new Uppercase()])
```

You may also use **[closure rules](https://laravel.com/docs/validation#using-closures)**:

```php
TextInput::make('slug')->rules([
    function () {
        return function (string $attribute, $value, Closure $fail) {
            if ($value === 'foo') {
                $fail("The {$attribute} is invalid.");
            }
        };
    },
])
```