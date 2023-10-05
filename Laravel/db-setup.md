# CREATE CONTROLLER, MODEL, FACTORY, SEEDER AND MIGRATIONS

## INDEX

- [ROUTES](#ROUTES)
- [MIGRATIONS](#MIGRATIONS)
- [SEEDERS](#SEEDERS)
- [FACTORIES](#FACTORIES)
- [MODELS](#MODELS)
- [CONTROLLERS](#CONTROLLERS)
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
php artisan make:model "nameModel" -a
```

>nameModel should be a singular noun written in title case


**OR INDIVIDUALLY**


_create controller_
```shell
php artisan make:controller "nameController"
```

>nameController should be a singular noun written in title case and include the word "Controller"


_create model_
```shell
php artisan make:model "nameModel"
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
php artisan make:seeder "nameSeeder"
```

>nameSeeder should be a singular noun written in title case and include the word "Seeder"


_create factory_ (create mass data)
```shell
php artisan make:factory "nameFactory"
```

>nameFactory should be a singular noun written in title case and include the word "Factory"




-------------------------------------------------------------------------------------------------------




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

---
# SEEDERS

**IMPORTANTE**
em `./database/seeds/DatabaseSeeder.php` é necessário chamar cada seeder que se quer correr
```php	
public function run()
    {
        $this->call(CountrySeeder::class);
        $this->call(UserSeeder::class);
        $this->call(BicycleSeeder::class);
    }
```


_inserir dados nas tabelas manualmente_

ex: `./database/seeds/CountrySeeder.php`
```php
public function run()
    {
        \DB::table('countries')->insert([
            'name' => 'Portugal',
        ]);
        \DB::table('countries')->insert([
            'name' => 'Espanha',
        ]);
        \DB::table('countries')->insert([
            'name' => 'França',
        ]);
        \DB::table('countries')->insert([
            'name' => 'Polónia',
        ]);
    }
```



---
# FACTORIES
inserir um certo número de dados nas tabelas automaticamente

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
_se quiser inserir por exemplo 100 users acrescentar também_

```php
factory(\App\User::class, 100)->create();
```
em `./database/seeds/UserSeeder.php`


### Caso queira criar _x_ número de bicicletas e associar 2 a cada user 
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

_por cada iteração de `$i` cria 2 bicicletas e associa ao mesmo user_id_

**ATENÇÃO**

_é necessário já haver (neste caso) bicycles criadas, quer através de seeder quer através de factory, para que se possa associar as bicicletas aos users_

---
# MODELS
fazer relações entre tabelas

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

---
# CONTROLLERS
Processa os requests da view (enviados através das routes) e interage com o model se necessário. Retorna uma response para a view.

Ex: `./app/Http/Controllers/UserController.php`
```php
public function index()
    {
        $users = \App\User::all();
        return view('user', [
            'users' => $users,
        ]);
    }
```

Outra maneira de representar é retornando a reponse em vez de retornar diretamente a view
```php
public function index()
    {
        $users = \App\User::all();
        return response()->view('user', [
            'users' => $users,
        ]);
    }
```



---