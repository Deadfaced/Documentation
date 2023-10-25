# CREATE CONTROLLER, MODEL, FACTORY, SEEDER AND MIGRATIONS

## INDEX

- [ROUTES](#ROUTES)
- [MIGRATIONS](#MIGRATIONS)
- [SEEDERS](#SEEDERS)
- [FACTORIES](#FACTORIES)
- [MODELS](#MODELS)
- [CONTROLLERS](#CONTROLLERS)
- [N-N](#N-N)

---


**WARNING**

if we know that we'll be needing a User model we can either delete:
- user model (`/app/User.php`);
- user factory (`/database/factories/UserFactory.php`);
- user seeder (`/database/seeds/UserSeeder.php`);
- user migrations:
  - (`/database/migrations/...create_users_table.php`);
  - (`/database/migrations/...create_password_resets_table.php`);
  - (`/database/migrations/...create_failed_jobs_table.php`);
  
or we can reuse them

---
**CREATE EVERYTHING AT ONCE**

_create all_
```shell
php artisan make:model "NameModel" -a
```

>nameModel should be a singular noun written in title case


**OR INDIVIDUALLY**


_create controller_
```shell
php artisan make:controller "NameController"
```

>nameController should be a singular noun written in title case and include the word "Controller"


_create model_
```shell
php artisan make:model "NameModel"
```

>nameModel should be a singular noun written in title case and include the word "Model"


_migration_ (create tables)
```shell
php artisan make:migration "nameMigration" --create="nameTable"
```

>`nameMigration` example: create_table_"nameTable" (in which nameTable is plural and in lowercase)

>`--create="nameTable"` example: --create="nameTable" (in which nameTable is plural and in lowercase)



_create seeder_ (insert data into tables)
```shell
php artisan make:seeder "NameSeeder"
```

>nameSeeder should be a singular noun written in title case and include the word "Seeder"


_create factory_ (create mass data)
```shell
php artisan make:factory "NameFactory"
```

>nameFactory should be a singular noun written in title case and include the word "Factory"




---




# ROUTES

### CREATE ROUTE
`./routes/web.php`
```php
Route::get('/"nameRoute"', "nameController@nameMethod");
```

`nameRoute`: points to the model

`nameController`: points to the controller

`nameMethod`: points to the method inside the controller

Example 1:
```php
Route::get('/user', "UserController@index");
```

Example 2:
```php
Route::get('/user/{id}', "UserController@show");
```

---
# MIGRATIONS

Example:

File: `./database/migrations/2020_12_09_000000_create_users_table.php`
```php
public function up()
    {
        Schema::create('users', function (Blueprint $table) {
            $table->id();
            $table->foreignId('country_id')->constrained();
            $table->string('first_name');
            $table->string('last_name');
            $table->date('birth_date');
            $table->timestamps();
            $table->softDeletes();
        });
    }
```
A few things to note:
- `public function up()` is the function that will be executed when the migration is run
- `Schema::create('users', function (Blueprint $table)` creates a table named `users`
- the keyword `constrained()` is used to create a foreign key; if left empty it will create a foreign key named `country_id` that references the `id` column of the `countries` table
- the keyword `constrained()` can also be filled to point to the table and column that we want to reference; in this case it could be something like `constrained('countries', 'code')` which would create a foreign key named `country_id` that references the `code` column of the `countries` table
- `softDeletes()` creates a column named `deleted_at` which will be used to soft delete rows from the table

---
# SEEDERS

**IMPORTANT**
in the file `./database/seeds/DatabaseSeeder.php` it is necessary to call each seeder that we want to run

```php
public function run()
    {
        $this->call(CountrySeeder::class);
        $this->call(UserSeeder::class);
        $this->call(BicycleSeeder::class);
    }
```


_insert data manually_

File: `./database/seeds/CountrySeeder.php`
```php
public function run()
    {
        \DB::table('countries')->insert([
            'name' => 'Portugal',
        ]);
        \DB::table('countries')->insert([
            'name' => 'Spain',
        ]);
        \DB::table('countries')->insert([
            'name' => 'France',
        ]);
        \DB::table('countries')->insert([
            'name' => 'Japan',
        ]);
    }
```



---
# FACTORIES
_insert fictional data into each field_

Ex:
`./database/factories/UserFactory.php`
```php
$factory->define(User::class, function (Faker $faker) {
    return [
        'first_name' => $faker->name(),
        'last_name' => $faker->name(),
        'country_id' => $faker->numberBetween(1, 4),
    ];
});
```

_if we want to create for example 100 fictional users also add the following code in `./database/seeds/UserSeeder.php`:_

```php
factory(\App\User::class, 100)->create();
```



### After creating users if we want to creat _x_ number of bicycles and associate 2 to each user

File: `./database/seeds/BicycleSeeder.php`
```php
public function run()
    {
        for($i = 1; $i <= 100; $i++){
            factory(\App\Bicycle::class, 2)->create([
                'user_id' => $i,
            ]);
        }
    }
```	

_for each `$i` iteration creates 2 bicycles and associates to the same user_id_


---
# MODELS
_create relationships between tables, add fillable property to fields, etc._

Ex:
```shell
class Country extends Model
{
    public function users()
    {
        return $this->hasMany(\App\User::class);
    }
}
```
Keywords:
- hasOne
- hasMany
- belongsTo
- belongsToMany

_(class) Country hasMany (function) users_

**WARNING**
To create a N:N relationship follow [this link](#N:N)

---
# CONTROLLERS
Processes the requests from the view (sent through the routes) and interacts with the model if necessary. Returns a response (for example to the view or to a JSON).


- _Example 1 (returning a view):_

File: `./app/Http/Controllers/UserController.php`

```php
public function index()
    {
        $users = \App\User::all();
        return response()->view('user', [
            'users' => $users,
        ]);
    }
```

_Another way to represent is to return the view directly instead of returning the response_
```php
public function index()
    {
        $users = \App\User::all();
        return view('user', [
            'users' => $users,
        ]);
    }
```


- _Example 2 (returning a JSON):_

File: `./app/Http/Controllers/UserController.php`

```php
public function index()
    {
        return response()->json(User::all(), 200);
    }
```

_can also be used inside a try-catch_
```php
public function index()
    {
        try{
            return response()->json(User::all(), 200);
        }
        catch(\Exception $e){
            return response()->json(['error' => $e], 500);
        }
    }
```




---




# N-N

In order to create a N:N relationship (users-roles for example) we need to do a few things:
- create a `belongsToMany` relationship in both tables:

File: `./app/User.php`
```php
public function roles()
    {
        return $this->belongsToMany(\App\Role::class);
    }
```

File: `./app/Role.php`
```php
public function users()
    {
        return $this->belongsToMany(\App\User::class);
    }
```

- create a pivot table (which will be used to store the relationships between the two tables) and its migration:
`php artisan make:migration create_role_user_table --create="role_user"`

**WARNING**: the name of the pivot table should be the two table names (singular) in alphabetical order separated by an underscore

- change the migration for the pivot table so that it will be composed of the two foreign keys (one for each table) and any other fields that we want to add (the pivot table itself doesn't need to have an id column):
```php
public function up()
    {
        Schema::create('role_user', function (Blueprint $table) {
            $table->foreignId('user_id')->constrained();
            $table->foreignId('role_id')->constrained();
        });
    }
```



- use methods such as `attach()`, `detach()`, `sync()` and `syncWithoutDetaching()` to add or remove relationships between the two tables:
  - `attach()` adds a relationship between the two tables
  - `detach()` removes a relationship between the two tables
  - `sync()` removes all relationships between the two tables and adds the ones that we specify
  - `syncWithoutDetaching()` adds the relationships that we specify without removing the ones that already exist

_Example of a `UserController@index`:_
```php
public function index(){
    $user = User::all()->load("roles");
    return response()->json($user);
}
```

_Example of a `UserController@store`:_
```php
public function store(Request $request)
    {
        $this->validate($request,[
            'first_name' => 'required',
            'last_name' => 'required',
        ])
        $user = new User();
        $user->first_name = $request->input('first_name');
        $user->last_name = $request->input('last_name');
        $user->save();
        $user->roles()->sync($request->input('roles', []));

        return response()->json($user, 201);
    }
```

- In this example we are first creating an instance of the `User` model, asking for the fields `first_name` and `last_name` and saving it to the database (using the method `save()`).
- Then we are using the `sync()` method to add the relationships between the user and the roles that we specify in the request.
- The `sync()` method takes an array as an argument so we are using the `input()` method to get the roles from the request.
- The second argument of the `input()` method is the default value that will be used if the roles are not specified in the request.
- In this case we are using an empty array as the default value because if the roles are not specified in the request we don't want to add any roles to the user.

---
[TOP](#INDEX)

[BACK](./)
