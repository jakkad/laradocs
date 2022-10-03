# Customizing Main Functions

## Listing Records

### Creating records in Modals

If your resource is simple, you may wish to create records in a modal rather than on the **[Create page](https://filamentphp.com/docs/2.x/admin/resources/creating-records)**. [Read more](https://filamentphp.com/docs/2.x/admin/resources/listing-records#creating-records-in-modals)

### Editing records in Modals

If your resource is simple, you may wish to edit records a modal rather than on the **[Edit page](https://filamentphp.com/docs/2.x/admin/resources/editing-records)**. [Read more](https://filamentphp.com/docs/2.x/admin/resources/listing-records#editing-records-in-modals)

### View records in Modals

If your resource is simple, you may wish to view records in modals rather than on the **[View page](https://filamentphp.com/docs/2.x/admin/resources/viewing-records)**. [Read more](https://filamentphp.com/docs/2.x/admin/resources/listing-records#viewing-records-in-modals)

### Authorization

For authorization, Filament will observe any **[model policies](https://laravel.com/docs/authorization#creating-policies)** that are registered in your app.

Users may access the List page if the `viewAny()` method of the model policy returns `true`.

They also have the ability to bulk-delete records if the `deleteAny()` method of the policy returns `true`. Filament uses the `deleteAny()` method because iterating through multiple records and checking the `delete()` policy is not very performant.

### Customizing Eloquent Query

Although you can **[customise the Eloquent query for the entire resource](https://filamentphp.com/docs/2.x/admin/resources/getting-started#customising-the-eloquent-query)**, you may also make specific modifications for the List page table. [Read more](https://filamentphp.com/docs/2.x/admin/resources/listing-records#customising-the-eloquent-query)

### Custom View

For further customization opportunities, you can override the static `$view` property on the page class to a custom view in your app:

```php
protected static string $view = 'filament.resources.users.pages.list-users';
```

## Creating / Editing Records

More details about all points [here](https://filamentphp.com/docs/2.x/admin/resources/creating-records) and [here](https://filamentphp.com/docs/2.x/admin/resources/editing-records)

### Customizing data before Saving

### Customizing the creation process

### Customizing form redirects

### Customizing the save notifications

### Lifecycle hooks

### Wizards

### Custom View

## Viewing Records

More details about all points [here](https://filamentphp.com/docs/2.x/admin/resources/viewing-records)

### Creating a resource with a View page

### Adding a View page to an existing resource

### Customizing data before filling the form

### Authorization

### Custom view