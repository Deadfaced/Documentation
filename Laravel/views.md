# VIEWS
Represens the web page where the user can see and interact with the data.

## Creating a Master Page
We first need to create a master page that will be the base for all the other pages and we will be using a bootstrap template for this.

First of all we need to create a folder called `master` (for example) inside `resources/views` and inside this folder we will create the files:
- `header.blade.php`
- `main.blade.php`
- `footer.blade.php`

Examples of these files:
- [header](./Views/MasterPage/header.md)
- [main](./Views/MasterPage/main.md)
- [footer](./Views/MasterPage/footer.md)

---

## Creating a Page
After the layout is created we can create a page that will extend the master page. This page should be created inside the `resources/views` folder or in another folder inside `resources/views`.

Whenever we create a new page we need to add the following code:
```php
@extends('master.main')


@section('content')

@endsection
```

The `@extends` directive tells the page to extend the `main` page inside the `master` folder.

The `@section` directive tells the page to add the content inside the `content` section of the `main` page.


_Example of a page that displays all users:_
```php
@extends('master.main')

@section('content')


<table class="table table-dark">
    <thead>
      <tr>
        <th scope="col">ID</th>
        <th scope="col">Country ID</th>
        <th scope="col">First Name</th>
        <th scope="col">Last Name</th>
        <th scope="col">Birth Date</th>
      </tr>
    </thead>

    <tbody>

        @foreach ($users as $user)
            <tr>
                <td>{{ $user->id }}</td>
                <td>{{ $user->country->id }}</td>
                <td>{{ $user->first_name }}</td>
                <td>{{ $user->last_name }}</td>
                <td>{{ $user->birth_date }}</td>
            </tr>
        @endforeach
    </tbody>
  </table>

@endsection
```

We create a bootstrap table and we use a `foreach` loop to display all the users. Inside each `<td>` we use the `{{ }}` to display php code in which we access each user's attributes.



---
[TOP](#views)

[BACK](./)