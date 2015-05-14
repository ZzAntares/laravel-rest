# LaravelRest

## Installation

Install the package via composer:

    composer require osedea/laravel-rest:~0.1

## Usage

First, add the eloquence service provider to your config/app.php file:

    'Osedea\LaravelRest\LaravelRestServiceProvider'

Then, publish the config file to your application:

    `php artisan vendor:publish --provider="Osedea\LaravelRest\LaravelRestServiceProvider"`

You should now be good to go without having to change anything! Access `/api/v1/users` to see a list of users!

## Config Options

### Application Namespace

You need to specify the application namespace used in your project. The default is App, but if you changed it, you need to change it in the config:

```
#!php
<?php

return [
    'app_namespace' => 'SomethingElse'
];
```

### Mapping

To add a resource to the default controller, you just have to add a line in the `/config/api.php` file:

```
#!php
<?php

return [
    'mapping' => [
        'users' => 'User',
    ]
];
```

### API Prefix

If you want to use a different prefix, you can do so by specifying the prefix key:

```
#!php
<?php

return [
    'prefix' => 'api/v2'
];
```

If you don't specify the key prefix, it will default to `api`.

## Commands and the default controller

Lucy comes with a default REST controller to handle resources.
This allows automatic actions on resources listing, showing, deleting and lising relations.

### Actions

| Method   | Url                           | Command         | Description              | Options                            |
| -------- | ----------------------------- | --------------- | ------------------------ | ---------------------------------- |
| `GET`    | `/{resource}`                 | `Index`         | List resources           | page, perPage, sort, fields, embed |
| `GET`    | `/{resource}/{id}`            | `Show`          | Show one resource        | fields, embed                      |
| `POST`   | `/{resource}`                 | `Store`         | Create a resource        |                                    |
| `PUT`    | `/{resource}/{id}`            | `Update`        | Update a resource        |                                    |
| `DELETE` | `/{resource}/{id}`            | `Destroy`       | Remove a resource        |                                    |
| `GET`    | `/{resource}/{id}/{relation}` | `RelationIndex` | List a resource relation | page, perPage, sort, fields, embed |

### Options

#### Pagination

All lists are paginated by default.
You can set a number of items per page for each Model by overriding a variable:

```
#!php
protected $perPage = 10;
```

You can also define a maximum for this value because it can be overridden in a request:

```
#!php
protected $perPageMax = 50;
```

And then in a request: `/users?page=2&perPage=3`

For a full documentation on pagination, refer to the [API Guideline](https://bitbucket.org/osedea/coding-guidelines/src/master/API%20guidelines.md)

#### Sorting

Sorting can be done on multiple columns by separating them with commas:

`/users?sort=name,email`

You can choose the order (desc or asc) by putting a `-` in front of a column name:

`/users?sort=-id`

#### Fields

If you don't need all fields when querying a list or a resource, you can specify which fields you need:

`/users?fields=id,name`

#### Nesting

**Use this only when you REALLY need to.**

If you absolutely need nested relations, you specify them like so:

`/users?embed=groups`

### Commands

#### Defaults

Lucy has default commands for several actions:

 * `GET /{resource}`: `Lucy\Commands\DefaultCommand\IndexCommand`
 * `GET /{resource}/{id}`: `Lucy\Commands\DefaultCommand\ShowCommand`
 * `DELETE /{resource}/{id}`: `Lucy\Commands\DefaultCommand\DeleteCommand`
 * `GET /{resource}/{id}/{relation}`: `Lucy\Commands\DefaultCommand\RelationIndexCommand`

With these defaults, the mapping is enough for them to work. You can override them by creating a command using the
correct namespace:

Example, to override the `POST /users/{id}` default command, create one called `Lucy\Commands\UserCommand\DestroyCommand`.

#### Create and Update

Those actions are very specific to a resource so there is no default.

You will have to create them for all resources listed in the mapping file. The schema is this:

`Lucy\Commands\{mapping[resource]}Command\{action}Command`

For example, to add the create command of the `users` resource, the command is:

`Lucy\Commands\UserCommand\StoreCommand`

#### A command class

A command class needs to extend `Lucy\Commands\Command` and implement `Illuminate\Contracts\Bus\SelfHandling`.
