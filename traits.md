# Traits
In PHP le classi possono ereditare da un solo parente. Per ovviare a ciò esistono i `traits`. Essi consentono di importare gli stessi metodi in classi diverse. I traits non sono istanziabili di per se, ma possono solo essere sfruttati dalle classi. 
Consentono la "composition of behevior", ossia la composizione dei metodi della classe senza l'uso dell'ereditarietà.
```php
 <?php
trait message1 {
public function msg1() {
    echo "OOP is fun! ";
  }
}

class Welcome {
  use message1;
}

$obj = new Welcome();
$obj->msg1();
?>
```
## ordine di precedenza
- I metodi della classe sovrascrivono i metodi del `trait` (precedenza massima).
- I metodi del `trait` sovrascrivono i metodi della classe base (precedenza media)
- La classe base è l'ultima in ordine di precedenza
```php
<?php
class Base {
    public function sayHello() {
        echo 'Hello ';
    }
}

trait SayWorld {
    public function sayHello() {
        parent::sayHello();
        echo 'World!';
    }
}

class MyHelloWorld extends Base {
    use SayWorld;
}

$o = new MyHelloWorld();
$o->sayHello();
?>
```
## traits multipli
Sepatrati dalla virgola
```php
<?php
trait Hello {
    public function sayHello() {
        echo 'Hello ';
    }
}

trait World {
    public function sayWorld() {
        echo 'World';
    }
}

class MyHelloWorld {
    use Hello, World;
    public function sayExclamationMark() {
        echo '!';
    }
}

$o = new MyHelloWorld();
$o->sayHello();
$o->sayWorld();
$o->sayExclamationMark();
?>
```
## Risoluzione dei conflitti
Se 2 traits in una classe hanno un metodo con lo stesso nome, si genera un "fatal error". Per ovviare si usa l'operatore `insteadof` per sceglierne un metodo preciso tra i possibili. Siccome cosí facendo si escludono tutti gli altri metodi, è bene usare l'operatore `as` per creare un alias ai metodi esclusi 
e poterli nuovamente utilizzare con un altro nome.
```php
<?php
trait A {
    public function smallTalk() {
        echo 'a';
    }
    public function bigTalk() {
        echo 'A';
    }
}

trait B {
    public function smallTalk() {
        echo 'b';
    }
    public function bigTalk() {
        echo 'B';
    }
}

class Talker {
    use A, B {
        B::smallTalk insteadof A;
        A::bigTalk insteadof B;
    }
}

class Aliased_Talker {
    use A, B {
        B::smallTalk insteadof A;
        A::bigTalk insteadof B;
        B::bigTalk as talk;
    }
}
?>
```
## Cambiare la visibilità dei metodi del trait
```php
<?php
trait HelloWorld {
    public function sayHello() {
        echo 'Hello World!';
    }
}

// Change visibility of sayHello
class MyClass1 {
    use HelloWorld { sayHello as protected; }
}

// Alias method with changed visibility
// sayHello visibility not changed
class MyClass2 {
    use HelloWorld { sayHello as private myPrivateHello; }
}
?>
```
## Traits composti da traits
```php
<?php
trait Hello {
    public function sayHello() {
        echo 'Hello ';
    }
}

trait World {
    public function sayWorld() {
        echo 'World!';
    }
}

trait HelloWorld {
    use Hello, World;
}

class MyHelloWorld {
    use HelloWorld;
}

$o = new MyHelloWorld();
$o->sayHello();
$o->sayWorld();
?>
```
## membri astratti
I traits possono avere dei metodi astratti (senza implementazione), che poi le classi dovranno necessariamente implementare. I metodi astratti possono essere pubbici, protetti e privati. Ma prima di PHP 8.0.0 potevano essere solo pubblici e protetti.
```php
<?php
trait Hello {
    public function sayHelloWorld() {
        echo 'Hello'.$this->getWorld();
    }
    abstract public function getWorld();
}

class MyHelloWorld {
    private $world;
    use Hello;
    public function getWorld() {
        return $this->world;
    }
    public function setWorld($val) {
        $this->world = $val;
    }
}
?>
```
## Membri statici
I traits possono avere funzioni, proprietà e variabili statici.
```php
<?php
trait StaticExample {
    public static function doSomething() {
        return 'Doing something';
    }
}

class Example {
    use StaticExample;
}

Example::doSomething();
?>
```
```php
<?php
trait StaticExample {
    public static function doSomething() {
        return 'Doing something';
    }
}

class Example {
    use StaticExample;
}

Example::doSomething();
?>
```
I membri statici da PHP 8.10 vanno chiamati dalla classe che usa il trait e non dal trait stesso.
## Proprietà
Una classe che usa un trait può definire proprietà che hanno lo stesso nome, modificatore di accesso e valore di quelli del trait. Altrimenti "fatal error".

```php
<?php
trait PropertiesTrait {
    public $same = true;
    public $different1 = false;
    public bool $different2;
    public bool $different3;
}

class PropertiesExample {
    use PropertiesTrait;
    public $same = true;
    public $different1 = true; // Fatal error
    public string $different2; // Fatal error
    readonly protected bool $different3; // Fatal error
}
?>
```
## Costanti nei traits
```php
<?php
trait ConstantsTrait {
    public const FLAG_MUTABLE = 1;
    final public const FLAG_IMMUTABLE = 5;
}

class ConstantsExample {
    use ConstantsTrait;
}

$example = new ConstantsExample;
echo $example::FLAG_MUTABLE; // 1
?>
```
## Conclusioni
Alla fine i traits in php sono le `interface` di altri linguaggi di programmazione (es. java).
