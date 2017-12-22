# Adldap2 - Laravel

![Built for Laravel](https://img.shields.io/badge/Built_for-Laravel-green.svg?style=flat-square)
[![Build Status](https://img.shields.io/travis/Adldap2/Adldap2-Laravel.svg?style=flat-square)](https://travis-ci.org/Adldap2/Adldap2-Laravel)
[![Scrutinizer Code Quality](https://img.shields.io/scrutinizer/g/Adldap2/Adldap2-laravel/master.svg?style=flat-square)](https://scrutinizer-ci.com/g/Adldap2/Adldap2-laravel/?branch=master)
[![Total Downloads](https://img.shields.io/packagist/dt/adldap2/adldap2-laravel.svg?style=flat-square)](https://packagist.org/packages/adldap2/adldap2-laravel)
[![Latest Stable Version](https://img.shields.io/packagist/v/adldap2/adldap2-laravel.svg?style=flat-square)](https://packagist.org/packages/adldap2/adldap2-laravel)
[![License](https://img.shields.io/packagist/l/adldap2/adldap2-laravel.svg?style=flat-square)](https://packagist.org/packages/adldap2/adldap2-laravel)

Adldap2 - Laravel allows easy configuration, access, management and authentication to LDAP connections utilizing the root
[Adldap2 Repository](http://www.github.com/Adldap2/Adldap2).

## Requirements

To use Adldap2-Laravel, your application and server must meet the following requirements:

- Laravel 5.*
- PHP 7.0 or greater
- PHP LDAP extension enabled
- An LDAP Server

## Index

* [Installation](#installation)
* [Usage](#usage)
* Auth Driver
  * [Installation & Basic Setup](docs/auth.md#installation)
  * [Quick Start - From Scratch](docs/quick-start.md)
  * [Upgrading](docs/auth.md#upgrading-from-3-to-4)
  * [Features](docs/auth.md#features)
    * [Providers](docs/auth.md#providers)
    * [Scopes](docs/auth.md#scopes)
    * [Rules](docs/auth.md#rules)
    * [Events](docs/auth.md#events)
    * [Synchronizing Attributes](docs/auth.md#syncing-attributes)
    * [Model Binding](docs/auth.md#model-binding)
    * [Login Fallback](docs/auth.md#fallback)
    * [Single Sign On (SSO) Middleware](docs/auth.md#middleware)
    * [Password Synchronization](docs/auth.md#password-synchronization)
    * [Importing Users](docs/importing.md)

## Installation

Run the following command in the root of your project:

```bash
composer require adldap2/adldap2-laravel
```

> **Note**: If you are using laravel 5.5 or higher you can skip the service provider
> and facade registration and continue with publishing the configuration file.

Once finished, insert the service provider in your `config/app.php` file:

```php
Adldap\Laravel\AdldapServiceProvider::class,
```

Then insert the facade:

```php
'Adldap' => Adldap\Laravel\Facades\Adldap::class
```

Publish the configuration file by running:

```bash
php artisan vendor:publish --tag="adldap"
```

Now you're all set!

## Usage

First, configure your LDAP connection in the `config/adldap.php` file.

Then, you can perform all methods on your Adldap connection through its facade like so:

```php
use Adldap\Laravel\Facades\Adldap;

// Finding a user.
$user = Adldap::search()->users()->find('john doe');

// Searching for a user.
$search = Adldap::search()->where('cn', '=', 'John Doe')->get();

// Running an operation under a different connection:
$users = Adldap::getProvider('other-connection')->search()->users()->get();

// Creating a user.
$user = Adldap::make()->user([
    'cn' => 'John Doe',
]);

$user->save();
```

Or you can inject the Adldap interface into your controllers, which gives
you access to all of your LDAP connections and resources.

```php
use Adldap\AdldapInterface;

class UserController extends Controller
{
    /**
     * @var Adldap
     */
    protected $ldap;
    
    /**
     * Constructor.
     *
     * @param AdldapInterface $adldap
     */
    public function __construct(AdldapInterface $ldap)
    {
        $this->ldap = $ldap;
    }
    
    /**
     * Displays the all LDAP users.
     *
     * @return \Illuminate\View\View
     */
    public function index()
    {
        $users = $this->ldap->search()->users()->get();
        
        return view('users.index', compact('users'));
    }
}
```

To see more usage in detail, please visit the [Adldap2 Repository](http://github.com/Adldap2/Adldap2).
