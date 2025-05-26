# Laravel Drag and Drop menu
[![Latest Stable Version](https://poser.pugx.org/nguyendachuy/laravel-menu/v)](//packagist.org/packages/nguyendachuy/laravel-menu) [![Total Downloads](https://poser.pugx.org/nguyendachuy/laravel-menu/downloads)](//packagist.org/packages/nguyendachuy/laravel-menu) [![Latest Unstable Version](https://poser.pugx.org/nguyendachuy/laravel-menu/v/unstable)](//packagist.org/packages/nguyendachuy/laravel-menu) [![License](https://poser.pugx.org/nguyendachuy/laravel-menu/license)](//packagist.org/packages/nguyendachuy/laravel-menu)


![Laravel drag and drop menu](https://raw.githubusercontent.com/nguyendachuy/laravel-menu/master/screenshot.png)

## Important Notice: Major Upgrade Available

We're excited to announce a major upgrade of Laravel Drag and Drop Menu Builder available on the `upgrade-version` branch. This version adds significant new features and improvements, especially comprehensive multilingual support.

### New Features and Improvements

- **Complete Multilingual Support**: User interface and system messages translated into multiple languages (currently English and Vietnamese)
- **Migration from Bootstrap to Tailwind CSS**: Completely redesigned interface with Tailwind CSS, providing a more modern and flexible experience
- **Mega Menu Support**: Create mega menus with custom content
- **Advanced JavaScript Integration**: Translation system integrated into JavaScript, allowing messages to be displayed in the user's language
- **Improved UX/UI**: Modern interface with intuitive drag-and-drop feedback
- **Performance Optimization**: Improved performance with caching system and query optimization
- **Robust Error Handling**: Comprehensive validation and error recovery system
- **Critical Bug Fixes**: Fixed issues including Menu Name and Menu Class not updating when clicking Update Menu

### How to Use the `upgrade-version` Branch

#### Option 1: Direct Git Checkout

```bash
# Clone repository (if you don't have it)
git clone https://github.com/nguyendachuy/laravel-menu.git

# Switch to upgrade-version branch
git checkout upgrade-version

# Install dependencies
composer install
```

#### Option 2: Using Composer (Recommended for Projects)

Update your `composer.json` file to use the upgrade-version branch:

```json
{
    "require": {
        "nguyendachuy/laravel-menu": "dev-upgrade-version"
    }
}
```

Then run:

```bash
composer update nguyendachuy/laravel-menu
```

### Database Updates

The new version adds columns for the Mega Menu feature. Run migrations to update your database structure:

```bash
php artisan migrate
```

The migration will add the following columns to the `menu_items` table:
- `is_mega_menu` (boolean): Determines if an item is a mega menu
- `mega_menu_content` (text): Stores the HTML content of the mega menu

### Updating Resources

The new version includes many changes to config, views, and public files. Run the following command to update:

```bash
# Publish all resources
php artisan vendor:publish --provider="NguyenHuy\Menu\Providers\MenuServiceProvider" --force

# Or publish specific resource types
php artisan vendor:publish --tag=laravel-menu-config --force
php artisan vendor:publish --tag=laravel-menu-views --force
php artisan vendor:publish --tag=laravel-menu-assets --force
php artisan vendor:publish --tag=laravel-menu-translations --force
```

**Important Note**: Using `--force` will overwrite any customizations you've made. If you've customized files, back them up before running these commands.

### Important Note

The `upgrade-version` branch has not yet been merged into `master` to avoid affecting current users. We encourage you to test this branch in a development environment before applying it to production projects.

Feedback and contributions are welcome to help us improve this package before the official release.

Please refer to the README.md in the `upgrade-version` branch for complete details on new features and usage instructions.

### Installation

1. Run

```php
composer require nguyendachuy/laravel-menu
```
2. Run publish

```php
php artisan vendor:publish --provider="NguyenHuy\Menu\Providers\MenuServiceProvider"
```

3. Configure (optional) in **_config/menu.php_** :

- **_CUSTOM MIDDLEWARE:_** You can add you own middleware
- **_TABLE PREFIX:_** By default this package will create 2 new tables named "menus" and "menu_items" but you can still add your own table prefix avoiding conflict with existing table
- **_TABLE NAMES_** If you want use specific name of tables you have to modify that and the migrations
- **_Custom routes_** If you want to edit the route path you can edit the field
- **_Role Access_** If you want to enable roles (permissions) on menu items
- **_CACHE ENABLED:_** Set this to `true` if you want to enable caching for menu items. Default is `false`.
- **_CACHE KEY PREFIX:_** The prefix to use for cache keys. Default is `'menu'`.
- **_CACHE TTL:_** The time-to-live (in minutes) for cached menu items. Default is `60`.

4. Run migrate

```php
php artisan migrate
```

DONE

### Menu Builder Usage Example - displays the builder

On your view blade file

```php
@extends('app')

@section('contents')
    {!! Menu::render() !!}
@endsection

//YOU MUST HAVE JQUERY LOADED BEFORE menu scripts
@push('scripts')
    {!! Menu::scripts() !!}
@endpush
```

### Using The Model

Call the model class

```php
use NguyenHuy\Menu\Models\Menus;
use NguyenHuy\Menu\Models\MenuItems;

```

### Menu Usage Example (a)

A basic two-level menu can be displayed in your blade template

##### Using Model Class
```php

/* get menu by id*/

$menu = Menus::find(1);
/* or by name */
$menu = Menus::where('name','Test Menu')->first();

/* or get menu by name and the items with EAGER LOADING (RECOMENDED for better performance and less query call)*/
$menu = Menus::where('name','Test Menu')->with('items')->first();
/*or by id */
$menu = Menus::where('id', 1)->with('items')->first();

//you can access by model result
$public_menu = $menu->items;

//or you can convert it to array
$public_menu = $menu->items->toArray();

```

##### or Using helper
```php
// Using Helper 
$public_menu = Menu::getByName('Public'); //return array

```

### Menu Usage Example (b)

Now inside your blade template file place the menu using this simple example

```php
<div class="nav-wrap">
    <div class="btn-menu">
        <span></span>
    </div><!-- //mobile menu button -->
    <nav id="mainnav" class="mainnav">

        @if($public_menu)
        <ul class="menu">
            @foreach($public_menu as $menu)
            <li class="">
                <a href="{{ $menu['link'] }}" title="">{{ $menu['label'] }}</a>
                @if( $menu['child'] )
                <ul class="sub-menu">
                    @foreach( $menu['child'] as $child )
                        <li class=""><a href="{{ $child['link'] }}" title="">{{ $child['label'] }}</a></li>
                    @endforeach
                </ul><!-- /.sub-menu -->
                @endif
            </li>
            @endforeach
        @endif

        </ul><!-- /.menu -->
    </nav><!-- /#mainnav -->
 </div><!-- /.nav-wrap -->
```

### HELPERS

### Get Menu Items By Menu ID

```php
use NguyenHuy\Menu\Facades\Menu;
...
/*
Parameter: Menu ID
Return: Array
*/
$menuList = Menu::get(1);
```

### Get Menu Items By Menu Name

In this example, you must have a menu named _Admin_

```php
use NguyenHuy\Menu\Facades\Menu;
...
/*
Parameter: Menu ID
Return: Array
*/
$menuList = Menu::getByName('Admin');
```

### Customization

You can edit the menu interface in **_resources/views/vendor/nguyendachuy-menu/menu-html.blade.php_**
