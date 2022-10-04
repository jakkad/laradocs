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