### Create user via tinker

```
php artisan tinker
```

```
$user = new App\User();
$user->password = Hash::make('mypassword');
$user->email = 'emal@domain';
$user->name = 'user';
$user->save();
```
