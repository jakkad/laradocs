# Authentication & Authorization

Laravel provides a built in Authentication & Authorization functionality.

## Create the UI

While we can create it manually we can use taliwind or bootstrap to automatically generate all the needed views for us

```php
php artisan ui tailwindcss --auth
```

Then we need to run npm 

## Model & Migration

Automatically created for us when we create a new project

User model class extends a class called `Authenticatable`  which allows access to multiple function related to authentication.

## Route

We only need to use 1 route instead of define them manually

```php
Auth::routes(); 

// We may need to clear and optimize route list
php artsian route:clear
php artisan optimize
```

## Controller

Laravel will generate a new folder that has 6 controller to manage all needed functions 

- Register
    
    It has a property `$redirectTo` which defines the page the user will be redirected to after registration and we can change it by clicking on it and change the url from the `RouteServiceProvider→Home`, also it has a validator and create methods.
    
- Login
- Reset password
- Forgot Password
- Confirm Password
- Verification

## View / UI

With the help of tailwind library we used, Laravel will add code to our master App view to handle the user + Login/Logout buttons

## Use Authorization

We can use authorization function to show content only for registered or allowed users.

- Check if the user logged in
    
    ```php
    @if ( Auth::user() )
    		..
    @endif
    ```
    
- Store Current user in the DB
    
    ```php
    // In create Method
    'user_id' => auth()->user()->id
    ```
    
- Allow only set user in the DB to see something
    
    ```php
    @if (isset(Auth::user()->id) && Auth::user()->id == $car->user_id)
    	..
    @endif
    ```
    
- Forbid User to access pages from the controller
    
    ```php
    // In the Controller
    
    public function __construct(){
    	$this->middleeware('auth', ['except' => ['index', 'show']]);
    }
    ```