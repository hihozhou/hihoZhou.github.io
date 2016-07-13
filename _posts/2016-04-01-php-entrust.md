---
layout: post
title: entrust —— 权限管理
date: 2016-04-01
categories: blog
tags: [php,php entrust,权限管理]
description: php entrust

---



#Installation

In order to install Laravel 5 Entrust, just add

```bash
"zizaco/entrust": "dev-laravel-5"
```

to your composer.json. Then run `composer install` or `composer update` .

Then in your config/app.php add

```php
Zizaco\Entrust\EntrustServiceProvider::class
```

in the providers array and

```php
Entrust' => Zizaco\Entrust\EntrustFacade::class
```

to the aliases array.

If you are going to use Middleware (requires Laravel 5.1 or later) you also need to add

```php
    'role' => \Zizaco\Entrust\Middleware\EntrustRole::class,
    'permission' => \Zizaco\Entrust\Middleware\EntrustPermission::class,
    'ability' => \Zizaco\Entrust\Middleware\EntrustAbility::class,
```

to routeMiddleware array in app/Http/Kernel.php.

#Configuration

Set the property values in the config/auth.php. These values will be used by entrust to refer to the correct user table and model.  
在`config/auth.php`中配置属性值.这些值将被entrust来引用正确的用户表和模型。


You can also publish the configuration for this package to further customize table names and model namespaces.    
你也可以创建一个配置来进一步自定义表名和模型命名空间  
Just use php artisan vendor:publish and a entrust.php file will be created in your app/config directory.  
只要用`php artisan vendor:publish`命令在`app/config`目录下创建一个entrust.php的文件

```bash
$ php artisan vendor:publish
Copied File [/vendor/zizaco/entrust/src/config/config.php] To [/config/entrust.php]
Publishing complete for tag []!
```

#User relation to roles

Now generate the Entrust migration:  
开始创建entrust数据库表:

```bash
php artisan entrust:migration
```

It will generate the \<timestamp\>_entrust_setup_tables.php migration. You may now run it with the artisan migrate command:  
它会创建\<timestamp\>_entrust_setup_tables.php迁移文件.你现在可以运行artisan migrate命令：

```bash
php artisan migrate
```

迁移后，四个新表将出现：

- roles — stores role records储存角色记录表
- permissions — stores permission records储存权限记录表
- role_user — stores many-to-many relations between roles and users储存用户表和角色表之间多对多的关系表
- permission_role — stores many-to-many relations between roles and permissions储存角色和权限之间多对多的关系表

#Models(模型)

##Role(角色)
Create a Role model inside app/models/Role.php using the following example:  
使用下面例子在`app/models/Role.php`创建角色模型:

```php
<?php namespace App;

use Zizaco\Entrust\EntrustRole;

class Role extends EntrustRole
{
}
```

The Role model has three main attributes:  
角色模型有三个主要属性:

- name — Unique name for the Role, used for looking up role information in the application layer. For example: "admin", "owner", "employee".  
- name(名称) — 唯一的角色名称,用于寻找应用层的角色信息.例如: "admin"(管理员), "owner"(业主), "employee"(员工).  
- display_name — Human readable name for the Role. Not necessarily unique and optional. For example: "User Administrator", "Project Owner", "Widget Co. Employee".  
- display_name(显示的名称) — 具有可读性的角色名称.任意并且不是一定是唯一的.例如: "User Administrator"(用户管理员), "Project Owner"(物业拥有者), "Widget Co. Employee"(部件有限公司员工).
- description — A more detailed explanation of what the Role does. Also optional.
- description(详细描述) — 角色的更详细说明文档. 可选填写.

Both display_name and description are optional; their fields are nullable in the database.  
显示的名称和详细描述是可选填写的;它们的字段在数据库中是可为空的.


##Permission(权限)

Create a Permission model inside app/models/Permission.php using the following example:  
使用下面例子在`app/models/Permission.php`创建权限模型:  

```php
<?php namespace App;

use Zizaco\Entrust\EntrustPermission;

class Permission extends EntrustPermission
{
}
```

The Permission model has the same three attributes as the Role:  
权限模型像角色模型一样有三个属性:  

- name — Unique name for the permission, used for looking up permission information in the application layer. For example: "create-post", "edit-user", "post-payment", "mailing-list-subscribe".
- name — 唯一的权限名称,用于寻找应用层的权限信息.例如:"create-post"(创建文章),"edit-user"(用户编辑),"post-payment"(请求支付),"mailing-list-subscribe"(订阅邮件列表).
- display_name — Human readable name for the permission. Not necessarily unique and optional. For example "Create Posts", "Edit Users", "Post Payments", "Subscribe to mailing list".
- display_name — 具有可读性的权限名称.任意并且不是一定是唯一的.例如:"Create Posts"(创建文章), "Edit Users"(用户编辑), "Post Payments"(请求支付), "Subscribe to mailing list"(订阅邮件列表).
- description — A more detailed explanation of the Permission.
- description — 权限的更详细说明

In general, it may be helpful to think of the last two attributes in the form of a sentence: "The permission display_name allows a user to description."  
总的来说，

##User
Next, use the EntrustUserTrait trait in your existing User model. For example:  
然后,在你的User模型中使用EntrustUserTrait的trait.例如:

```php
<?php

use Zizaco\Entrust\Traits\EntrustUserTrait;

class User extends Eloquent
{
    use EntrustUserTrait; // add this trait to your user model

    ...
}
```

This will enable the relation with Role and add the following methods roles(), hasRole($name), can($permission), and ability($roles, $permissions, $options) within your User model.  


Don't forget to dump composer autoload

```bash
composer dump-autoload
```

And you are ready to go.你已经准备好开发了

##Soft Deleting

The default migration takes advantage of onDelete('cascade') clauses within the pivot tables to remove relations when a parent record is deleted. If for some reason you cannot use cascading deletes in your database, the EntrustRole and EntrustPermission classes, and the HasRole trait include event listeners to manually delete records in relevant pivot tables. In the interest of not accidentally deleting data, the event listeners will not delete pivot data if the model uses soft deleting. However, due to limitations in Laravel's event listeners, there is no way to distinguish between a call to delete() versus a call to forceDelete(). For this reason, before you force delete a model, you must manually delete any of the relationship data (unless your pivot tables uses cascading deletes). For example:

```php
$role = Role::findOrFail(1); // Pull back a given role

// Regular Delete
$role->delete(); // This will work no matter what

// Force Delete
$role->users()->sync([]); // Delete relationship data
$role->perms()->sync([]); // Delete relationship data

$role->forceDelete(); // Now force delete will work regardless of whether the pivot table has cascading delete
```

#Usage(用法)

##概念
让我们从创建下面的角色和权限开始：

```php
$owner = new Role();
$owner->name         = 'owner';
$owner->display_name = 'Project Owner'; // optional
$owner->description  = 'User is the owner of a given project'; // optional
$owner->save();

$admin = new Role();
$admin->name         = 'admin';
$admin->display_name = 'User Administrator'; // optional
$admin->description  = 'User is allowed to manage and edit other users'; // optional
$admin->save();
```

Next, with both roles created let's assign them to the users. Thanks to the HasRole trait this is as easy as:
然后让我们分配刚创建的两个角色给用户.感谢HasRole trait让这很容易:

```php
$user = User::where('username', '=', 'michele')->first();

// role attach alias
$user->attachRole($admin); // parameter can be an Role object, array, or id

// or eloquent's original technique
$user->roles()->attach($admin->id); // id only
```

Now we just need to add permissions to those Roles:  
现在我们只需要向这些角色添加权限：  

```php
$createPost = new Permission();
$createPost->name         = 'create-post';
$createPost->display_name = 'Create Posts'; // optional
// Allow a user to...
$createPost->description  = 'create new blog posts'; // optional
$createPost->save();

$editUser = new Permission();
$editUser->name         = 'edit-user';
$editUser->display_name = 'Edit Users'; // optional
// Allow a user to...
$editUser->description  = 'edit existing users'; // optional
$editUser->save();

$admin->attachPermission($createPost);
// equivalent to $admin->perms()->sync(array($createPost->id));

$owner->attachPermissions(array($createPost, $editUser));
// equivalent to $owner->perms()->sync(array($createPost->id, $editUser->id));
```

##Checking for Roles & Permissions(验证角色和权限)
Now we can check for roles and permissions simply by doing:  
现在我们可以简单地检查角色和权限：  

```php
$user->hasRole('owner');   // false
$user->hasRole('admin');   // true
$user->can('edit-user');   // false
$user->can('create-post'); // true
```

Both hasRole() and can() can receive an array of roles & permissions to check:  
`hasRole()`和`can()`方法都可以验证传入的角色和权限数组: 

```php
$user->hasRole(['owner', 'admin']);       // true
$user->can(['edit-user', 'create-post']); // true
```

By default, if any of the roles or permissions are present for a user then the method will return true. Passing true as a second parameter instructs the method to require all of the items:  
默认情况下,如果用户存在角色权限或权限数组中的其中一个,方法就会返回true.通过在方法的第二个参数中传入true来要求判断是否有所有选项:

```php
$user->hasRole(['owner', 'admin']);             // true
$user->hasRole(['owner', 'admin'], true);       // false, user does not have admin role
$user->can(['edit-user', 'create-post']);       // true
$user->can(['edit-user', 'create-post'], true); // false, user does not have edit-user permission
```

You can have as many `Roles` as you want for each `User` and vice versa.  
同一个`用户`可以有很多`角色`,反过来也一样,多个`用户`可以拥有同一个`角色`.

The Entrust class has shortcuts to both can() and hasRole() for the currently logged in user:  
Entrust类为登录用户提供快捷的can()和hasRole():

```php
Entrust::hasRole('role-name');
Entrust::can('permission-name');

// is identical to
// 和以下代码相同

Auth::user()->hasRole('role-name');
Auth::user()->can('permission-name);
```

You can also use placeholders (wildcards) to check any matching permission by doing:  
你也可以使用占位符（通配符）通过检查任何匹配的权限：  

```php
// match any admin permission
$user->can("admin.*"); // true

// match any permission about users
$user->can("*_users"); // true
```

##User ability(用户能力)
More advanced checking can be done using the awesome ability function. It takes in three parameters (roles, permissions, options):  
通过使用高级的方法可以实现更先进的验证.它需要三个参数(roles, permissions, options)：

- roles is a set of roles to check.
- roles指的是验证的角色列表.
- permissions is a set of permissions to check.
- permissions指的是验证的权限列表

Either of the roles or permissions variable can be a comma separated string or array:  
传入的角色列表或权限列表参数可以用逗号分割的字符串或者是数组中的其中一种:  

```php
$user->ability(array('admin', 'owner'), array('create-post', 'edit-user'));

// or

$user->ability('admin,owner', 'create-post,edit-user');
```

This will check whether the user has any of the provided roles and permissions. In this case it will return true since the user is an `admin` and has the `create-post` permission.  
这将检查用户是否具有传入的角色列表和权限列表的其中一个。当用用户是`admin`角色和有`create-post`权限,方法将会返回true.

The third parameter is an options array:  
第三个参数是选项数组:  

```php
$options = array(
    'validate_all' => true | false (Default: false),
    'return_type'  => boolean | array | both (Default: boolean)
);

```

- validate_all is a boolean flag to set whether to check all the values for true, or to return true if at least one role or permission is matched.
- return_type specifies whether to return a boolean, array of checked values, or both in an array.


Here is an example output:  
这里有输出列子:  

```php
$options = array(
    'validate_all' => true,
    'return_type' => 'both'
);

list($validate, $allValidations) = $user->ability(
    array('admin', 'owner'),
    array('create-post', 'edit-user'),
    $options
);

var_dump($validate);
// bool(false)

var_dump($allValidations);
// array(4) {
//     ['role'] => bool(true)
//     ['role_2'] => bool(false)
//     ['create-post'] => bool(true)
//     ['edit-user'] => bool(false)
// }
```

The `Entrust` class has a shortcut to `ability()` for the currently logged in user:

```php
Entrust::ability('admin,owner', 'create-post,edit-user');

// is identical to

Auth::user()->ability('admin,owner', 'create-post,edit-user');
```

##Blade templates(Blade模板)
Three directives are available for use within your Blade templates. What you give as the directive arguments will be directly passed to the corresponding `Entrust` function.
在Blade模板中,你有可以三个指令.你给的指令参数将被直接传递到相应的`Entrust`方法。

```php
@role('admin')
    <p>This is visible to users with the admin role. Gets translated to 
    \Entrust::role('admin')</p>
@endrole

@permission('manage-admins')
    <p>This is visible to users with the given permissions. Gets translated to 
    \Entrust::can('manage-admins'). The @can directive is already taken by core 
    laravel authorization package, hence the @permission directive instead.</p>
@endpermission

@ability('admin,owner', 'create-post,edit-user')
    <p>This is visible to users with the given abilities. Gets translated to 
    \Entrust::ability('admin,owner', 'create-post,edit-user')</p>
@endability
```

##Middleware(中间件)
You can use a middleware to filter routes and route groups by permission or role  
你可以使用中间件来过滤路由和路由组的权限或角色

```php
Route::group(['prefix' => 'admin', 'middleware' => ['role:admin']], function() {
    Route::get('/', 'AdminController@welcome');
    Route::get('/manage', ['middleware' => ['permission:manage-admins'], 'uses' => 'AdminController@manageAdmins']);
});
```

It is possible to use pipe symbol as OR operator:

```php
'middleware' => ['role:admin|root']
```

To emulate AND functionality just use multiple instances of middleware

```php
'middleware' => ['permission:owner', 'permission:writer']
```

For more complex situations use ability middleware which accepts 3 parameters: roles, permissions, validate_all

```php
'middleware' => ['ability:admin|owner,create-post|edit-user,true']
```

##Short syntax route filter(短语法规则路由过滤器)

To filter a route by permission or role you can call the following in your `app/Http/routes.php`:



—End—



##迭代


* 2016年04月01日 13:00:00 初稿
* 2016年04月02日 22:39:12 翻译原稿
* 2016年07月13日 23:28:00 修改排版



## 原文
- [《Pimple - A simple PHP Dependency Injection Container》](http://pimple.sensiolabs.org/)
