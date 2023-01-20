### Create user via tinker

```
php artisan tinker
```

```
$user = new App\User();
$user->password = Hash::make('mypassword');
$user->email = 'email@domain';
$user->name = 'user';
$user->save();
```
