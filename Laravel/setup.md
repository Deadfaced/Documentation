## Setup

Setting up a Laravel project

# INSTALL AND CREAT LARAVEL PROJECT

_install files needed for a Laravel project_
```shell
composer global require laravel/installer
```

_create a new empty project_
```shell
composer create-project --prefer-dist laravel/laravel:^7.0 "projectName"
```


_**IMPORTANT**_
_enter the newly created project_
```shell
cd "projectName"
```



_runs the php server in the localhost_
```shell
php artisan serve
```

_**WARNING**_: after this command the cmd will be blocked! you need to open a new cmd inside the same folder or through the IDE terminal (VSCode/PHPStorm)


_adds UI to the project's dependencies_
```shell
composer require laravel/ui:^2.4
```





_used to scaffold the basic authentication views and routes_
```shell
php artisan ui vue --auth
```




_install npm dependencies_
```shell
npm install (or "npm i")
```



_setup npm packages settings_
```shell
npm run dev
```



_**OPEN XAMPP**_
```shell
- start apache and mysql
- "admin" in mysql
```


_**OPEN phpmyadmin**_
```shell
- go to "Databases" tab
- create database (all lower case)
```



_**BACK to IDE**_
_change database name_
```shell
- open "newProject\.env" file
- look for "DB_DATABASE=laravel" (in between lines 9-14) and change "laravel" to the database name you created in phpmyadmin
```



_**RESTART SERVER**_
- to restart server you need to go to the cmd where the server is running and do **CTRL+C** to shutdown and then write ```php artisan serve``` to activate again



---


# *IMPORTANT*
- After project is finished
  - delete "vendor" and "node_modules" folders
- Run the project again
  - CMD: ```composer install``` and ```npm install```

---

[TOP](#setup)

[BACK](/)