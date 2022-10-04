# Getting Started

## Creating a page

To create a new page, you can use:

```php
php artisan make:filament-page Settings
```

This command will create two files - a page class in the `/Pages` directory of the Filament directory, and a view in the `/pages` directory of the Filament views directory.

Page classes are all full-page **[Livewire](https://laravel-livewire.com/)** components with a few extra utilities you can use with the admin panel.

## Conditionally hide pages in navigation

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

## Customization

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

# Actions

## Getting Started

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

# Widgets

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

## Customizing the widgets in Grid

You may change how many grid columns are used to display widgets.

You may override the `getHeaderWidgetsColumns()` or `getFooterWidgetsColumns()` methods to return a number of grid columns to use:

```php
protected function getHeaderWidgetsColumns(): int | array
{
    return 3;
}
```

## Responsive widgets grid

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