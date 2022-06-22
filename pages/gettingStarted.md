# Getting Started

## What is Laravel?

**Laravel** is an open-source PHP framework that uses the MVC Architecture.

**Documentation**: Laravel has a very detailed documentation for each version [Check Here.](https://laravel.com/docs/8.x)

## Pre-Installation

Before installing Laravel we need to have PHP, MYSQL installed on the machine. After that we have to install **Composer**: which is a PHP Package Manager that we’ll use to install Laravel and other packages.

## Installation

We have 2 ways to install Laravel

**Via Composer:** 

```bash
composer create-project laravel/laravel {app-name}
```

**Laravel Installer:** we define Laravel Installer as a global Composer dependency, then we create the app:

```bash
composer global require laravel/installer

laravel new {app-name}
```

## Run The Project

Starting a project can be done through CMD using artisan

```bash
PHP artisan serve <--port={port-number}> // port is optional
```

note: port number is optional but sometimes we need it as the default port 8000 is used by another application.

## .ENV file

A file to let us setup the environment variables in the server, e.g: the app metadata, DB settings,..

## Composer Json & Lock Files

Composer Json: is a manifest with all the information needed for composer from the application info to the required libraries.

- Required: Each library we need in the project.
- Required-dev: only used in development and not required to run our app.

All libraries can be reinstalled by

```bash
composer intall
```

## Packagist

A repository website for all packages in composer and how to install it. [Here](https://packagist.org/)