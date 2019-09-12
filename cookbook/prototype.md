# Cookbook - Prototyping
Spiral Framework comes with development extension which speed up the development of application services, controllers,
middleware and other classes via automatic code modification (AST). Extension includes IDE friendly tooltips for most 
common framework components and Cycle Repositories.

## Installation
To install extension:

```bash
$ composer require spiral/prototype
```

Make sure to add `Spiral\Prototype\Bootloader\PrototypeBootloader` to your App class:

```php
class App extends Kernel
{
    /*
     * List of components and extensions to be automatically registered
     * within system container on application start.
     */
    protected const LOAD = [
        // ...

        // Framework commands
        Bootloader\CommandBootloader::class,
        PrototypeBootloader::class
    ];
    
    // ...   
}
```

Now you can run `php app.php configure` to generate IDE tooltips.

## Usage of Prototype Properties
To use prototyping abilities of framework add `Spiral\Prototype\Traits\PrototypeTrait` to any of your classes. 
Once complete your IDE will immediately suggest you available classes and Cycle Repositories:

![IDE Tooltips](https://user-images.githubusercontent.com/796136/64488784-a04d0a00-d254-11e9-8650-6a25c71bf46c.png)

You can use this suggestions directly, without need for any import:

```php
namespace App\Controller;

use Spiral\Prototype\Traits\PrototypeTrait;

class HomeController
{
    use PrototypeTrait;

    public function index()
    {
        $select = $this->users->select();
    }
}
```

Once your prototyping phase is complete you can remove the trait and inject dependencies via:

```bash
$ php app.php prototype:inject -r
```

> Use `-r` flag to remove `PrototypeTrait`.

Extension will modify your class into given form:


```php
namespace App\Controller;

use App\UserRepository;

class HomeController
{
    /** @var UserRepository */
    private $users;

    /**
     * @param UserRepository $users
     */
    public function __construct(UserRepository $users)
    {
        $this->users = $users;
    }

    public function index()
    {
        $select = $this->users->select();
    }
}
```

To view all the classes which use prototyped properties without modifying them:

```bash
$ php app.php prototype:list
```

> You can remove `spiral/prototype` extension after all injects are complete.

## Custom Properties
You can register any number of prototyped properties using `Spiral\Prototype\Bootloader\PrototypeBootloader` in your bootloader:

```php
public function boot(PrototypeBootloader $prototype)
{
    $prototype->bindProperty('myService', MyService::class);
}
```