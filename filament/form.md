# Form Builder

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

## Fields Option

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

## Change Field Globally

If you wish to change the default behavior of a field globally, then you can call the static `configureUsing()` method inside a service provider's `boot()` method, to which you pass a Closure to modify the component using. For example, if you wish to make all checkboxes **`[inline(false)](https://filamentphp.com/docs/2.x/forms/fields#checkbox)`**, you can do it like so:

```php
Checkbox::configureUsing(function (Checkbox $checkbox): void {
    $checkbox->inline(false);
});
```

Of course, you are still able to overwrite this on each field individually:

```php
Checkbox::make('is_admin')->inline()
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