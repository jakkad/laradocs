## Changing Brand Logo

By default, Filament will use your app's name as a brand logo in the admin panel.

You may create a `resources/views/vendor/filament/components/brand.blade.php` file to provide a custom logo:

```php
<img src="{{ asset('/images/logo.svg') }}" alt="Logo" class="h-10">
```

## Dark Mode

By default, Filament only includes a light theme. However, you may allow the user to switch to dark mode if they wish, using the `dark_mode` setting of the **[configuration file](https://filamentphp.com/docs/2.x/admin/installation#publishing-configuration)**:

```php
'dark_mode' => true,
```

When dark mode is enabled, the admin panel will automatically obey your system's dark / light mode preference. You may switch to dark / light mode permanently through the button in the user dropdown menu.
[More details here](https://filamentphp.com/docs/2.x/admin/appearance#dark-mode)

## Collapsible Sidebar

By default, the sidebar is only collapsible on mobile. You may make it collapsible on desktop as well.

You must **[publish the configuration](https://filamentphp.com/docs/2.x/admin/installation#publishing-configuration)** in order to access this feature.

In `config/filament.php`, set the `layouts.sidebar.is_collapsible_on_desktop` to `true`:

```php
'layout' => [
    'sidebar' => [
        'is_collapsible_on_desktop' => true,
    ],
],
```

## Non-sticky Topbar

By default, the topbar sticks to the top of the page.

You may make the topbar scroll out of view instead by adding the following styles to your **[theme](https://filamentphp.com/docs/2.x/admin/appearance#building-theme)** or by **[registering a new stylesheet](https://filamentphp.com/docs/2.x/admin/appearance#including-frontend-assets)**:

```php
.filament-main-topbar {
    position: relative;
}
```

> [More details on all other topics here](https://filamentphp.com/docs/2.x/admin/appearance)
>

## Building Themes

## Changing the Maximum Content Width

## Including Frontend Assets

## Custom Meta Tags

## Notification Postition

## Render Hooks