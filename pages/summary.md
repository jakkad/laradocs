# Summary

- First we create the project
    
    ```php
    laravel new Title
    ```
    
- We require any prerequisite library, then we install it with npm
    
    ```php
    compoer reuire laravel-frontend-presets/tailwinddcss --dev
    
    npm install && npm run dev
    ```
    
- We create the model + Migration
    
    ```php
    php artsian make:model Car -m
    ```
    
- We create our Controller with the use of resource flag to create all CRUD Function automatically
    
    ```php
    php artisan make:controller CarsController --reource
    ```
    
- We create our route in the routes file using also resource
    
    ```php
    Route::reosource('/cars', CarsController::class);
    ```
    
- We create our view files then we can call them in the Controllers' functions.
- We create the Layout and views needed for each CRUD Function
- Create the Migration and Migrate the DB
-