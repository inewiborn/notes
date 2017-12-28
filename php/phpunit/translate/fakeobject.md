### PHPUnit Test Doubles (тестовые двойники)
Концепцию тестирования с использованием двойников (Test Doubles),
которая была введена Джерардом Месарошом (Gerard Meszaros), можно описать следующим образом.

Иногда бывает просто невозможно проверить работоспособность тестируемого объекта (system uder test — SUT), через его зависимость от других компонентов, которые нельзя использовать в тестовой среде: они могут быть недоступными, могут возвращать не верные результаты, необходимые для теста, или потому, что их выполнение будет иметь нежелательные побочные эффекты. В других случаях, наша стратегия тестирования требует от нас большего контроля или видимости внутреннего поведения SUT.

Когда мы пишем тест, в котором мы не можем (или не хотим) использовать реальный зависимый компонент (real depended-on component — DOC), мы можем заменить его на двойник (Test Double). Двойник (Test Double) не должен вести себя точно так же, как и реальный DOC; он просто должен предоставить тот же API, что и реальный объект, так что SUT думает, что это и есть реальный объект!

Методы createMock($type) и getMockBuilder($type), предоставляемые каркасом PHPUnit, могут использоваться в тесте для автоматической генерации объекта, который будет действовать как PHPUnit test doubles (тестовый двойник) для заданного исходного типа (интерфейс или имя класса). Этот объект, PHPUnit test doubles (тестовый двойник), может использоваться в коде, где ожидается или требуется наличия оригинального объекта.

Метод createMock($type) незамедлительно возвращает объект PHPUnit тестового двойника для указанного типа (интерфейса или класса).
Создание этого тестового двойника выполняется с использованием настроек по умолчанию (методы ~__construct()~ и ~__clone()~ исходного класса не выполняются), а аргументы, переданные методу тестового двойника, не будут клонироваться. Если эти значения по умолчанию вам не нужны, вы можете использовать метод getMockBuilder($type) для настройки тестового двойника с использованием «плавающего» интерфейса.

По умолчанию, все методы исходного класса заменяются фиктивной реализацией, которая просто возвращает null (без вызова оригинального метода). Используя метод will($this->returnValue()), вы можете настроить эти фиктивные реализации для возврата необходимых значения при их вызове.

##### Замечания
>Обратите внимание, что final, private и static методы не могут быть в заглушке (stub) или в мок-объекте (mock). Они игнорируются функциональностью PHPUnit и сохраняют свое первоначальное поведение.

Мы рассмотрели принцип использования PHPUnit test doubles (PHPUnit тестовые двойники). Продолжение будет в следующих статьях

Материал статьи взят с официального сайта PHPUnit.

### PHPUnit заглушки (stub)
Практика замены объекта тестовым двойником, который (опционально) возвращает сконфигурированные значения, называется заглушкой. Вы можете использовать PHPUnit заглушки, для подменить реальных компонентов, от которого зависит SUT.

Следующий пример показывает, как вызывать методы PHPUnit заглушки и устанавливать возвращаемые значения. Для начала мы используем метод createMock(), который предоставляется классом PHPUnit\Framework\TestCase для создания объекта-заглушки, который будет выглядит как объект класса SomeClass. Затем мы используем Fluent Interface, который PHPUnit предоставляет для указания поведения PHPUnit заглушки. По существу, это означает, что вам не нужно создавать несколько временных объектов и соединять их потом. Вместо этого вы связываете вызовы методов, как показано в примере. Это приводит к более читабельному и красивому коду.

Класс, объект которого будет подменен PHPUnit заглушкой:

```php
<?php
use PHPUnit\Framework\TestCase;
 
class SomeClass
{
    public function doSomething()
    {
        // Do something.
    }
}
?>
```
Вызов метода PHPUnit заглушки для возвращение фиктивных данных

```php
<?php
use PHPUnit\Framework\TestCase;
 
class StubTest extends TestCase
{
    public function testStub()
    {
        // Create a stub for the SomeClass class.
        $stub = $this->createMock(SomeClass::class);
 
        // Configure the stub.
        $stub->method('doSomething')
             ->willReturn('foo');
 
        // Calling $stub->doSomething() will now return
        // 'foo'.
        $this->assertEquals('foo', $stub->doSomething());
    }
}
?>
```
#### Ограничение: методы, названные «method»
>Пример, показанный выше, работает только тогда, когда в исходном классе не объявляет метод с именем «method».
>Если в исходном классе объявлен метод с именем «method», то должно быть следующим образом: $stub->expects($this->any())->method('doSomething')->willReturn('foo');.

Следующий пример показывает как использовать текучий интерфейс Mock Builder для настройки создания тестового двойника. Конфигурация этого тестового двойника использует те же самые настройки по умолчанию, которые используются при createMock().

```php
<?php
use PHPUnit\Framework\TestCase;
 
class StubTest extends TestCase
{
    public function testStub()
    {
        // Create a stub for the SomeClass class.
        $stub = $this->getMockBuilder($originalClassName)
                     ->disableOriginalConstructor()
                     ->disableOriginalClone()
                     ->disableArgumentCloning()
                     ->disallowMockingUnknownTypes()
                     ->getMock();
 
        // Configure the stub.
        $stub->method('doSomething')
             ->willReturn('foo');
 
        // Calling $stub->doSomething() will now return
        // 'foo'.
        $this->assertEquals('foo', $stub->doSomething());
    }
}
?>
```
До этой поры, в примерах, мы возвращали простые значения с помощью willReturn($value). Этот короткий синтаксис такой же, как и will($this->returnValue($value)). Использования более длинного синтаксиса дает более сложное поведения заглушки.

PHPUnit returnArgument и returnValue
Иногда вы хотите вернуть один из аргументов вызванного метода (неизменного) как результат вызова метода PHPUnit заглушки. Пример ниже, показывает, как вы можете достичь этого с помощью returnArgument() вместо returnValue().

```php
<?php
use PHPUnit\Framework\TestCase;
 
class StubTest extends TestCase
{
    public function testReturnArgumentStub()
    {
        // Create a stub for the SomeClass class.
        $stub = $this->createMock(SomeClass::class);
 
        // Configure the stub.
        $stub->method('doSomething')
             ->will($this->returnArgument(0));
 
        // $stub->doSomething('foo') returns 'foo'
        $this->assertEquals('foo', $stub->doSomething('foo'));
 
        // $stub->doSomething('bar') returns 'bar'
        $this->assertEquals('bar', $stub->doSomething('bar'));
    }
}
?>
```
PHPUnit returnSelf
При тестировании текучего интерфейса (fluent interface), иногда полезно, что бы метод заглушки возвращал ссылку на объект PHPUnit заглушки. Пример ниже показывает, как вы можете использовать returnSelf() для достижения этого.

```php
<?php
use PHPUnit\Framework\TestCase;
 
class StubTest extends TestCase
{
    public function testReturnSelf()
    {
        // Create a stub for the SomeClass class.
        $stub = $this->createMock(SomeClass::class);
 
        // Configure the stub.
        $stub->method('doSomething')
             ->will($this->returnSelf());
 
        // $stub->doSomething() returns $stub
        $this->assertSame($stub, $stub->doSomething());
    }
}
?>
```
PHPUnit returnValueMap
Иногда метод PHPUnit заглушки должен возвращать различные значения в зависимости от предопределенного списка аргументов. Вы можете использовать returnValueMap(), чтобы создать карту, которая связывает аргументы с соответствующими возвращаемыми значениями. Пример приведен ниже.

```php
<?php
use PHPUnit\Framework\TestCase;
 
class StubTest extends TestCase
{
    public function testReturnValueMapStub()
    {
        // Create a stub for the SomeClass class.
        $stub = $this->createMock(SomeClass::class);
 
        // Create a map of arguments to return values.
        $map = [
            ['a', 'b', 'c', 'd'],
            ['e', 'f', 'g', 'h']
        ];
 
        // Configure the stub.
        $stub->method('doSomething')
             ->will($this->returnValueMap($map));
 
        // $stub->doSomething() returns different values depending on
        // the provided arguments.
        $this->assertEquals('d', $stub->doSomething('a', 'b', 'c'));
        $this->assertEquals('h', $stub->doSomething('e', 'f', 'g'));
    }
}
?>
```
PHPUnit returnCallback
Когда вызов метода заглушки должен возвращать вычисляемое значение вместо фиксированного (см. returnValue()) или (неизменный) аргумент (см. ReturnArgument()), вы можете использовать returnCallback(), чтобы метод заглушки возвратил результат работы функции обратного вызова или метода. Смотри пример ниже.

```php
<?php
use PHPUnit\Framework\TestCase;
 
class StubTest extends TestCase
{
    public function testReturnCallbackStub()
    {
        // Create a stub for the SomeClass class.
        $stub = $this->createMock(SomeClass::class);
 
        // Configure the stub.
        $stub->method('doSomething')
             ->will($this->returnCallback('str_rot13'));
 
        // $stub->doSomething($argument) returns str_rot13($argument)
        $this->assertEquals('fbzrguvat', $stub->doSomething('something'));
    }
}
?>
```
PHPUnit onConsecutiveCalls
Более простой альтернативой настройке метода обратного вызова, может быть указание списка желаемых возвращаемых значений. Вы можете сделать это с помощью метода onConsecutiveCalls(). Пример ниже.

```php
<?php
use PHPUnit\Framework\TestCase;
 
class StubTest extends TestCase
{
    public function testOnConsecutiveCallsStub()
    {
        // Create a stub for the SomeClass class.
        $stub = $this->createMock(SomeClass::class);
 
        // Configure the stub.
        $stub->method('doSomething')
             ->will($this->onConsecutiveCalls(2, 3, 5, 7));
 
        // $stub->doSomething() returns a different value each time
        $this->assertEquals(2, $stub->doSomething());
        $this->assertEquals(3, $stub->doSomething());
        $this->assertEquals(5, $stub->doSomething());
    }
}
```
?
PHPUnit throwException
Метод PHPUnit заглушки также может вызвать исключение. В примере ниже показано, как использовать throwException() для проверки вызова исключения.

```php
<?php
use PHPUnit\Framework\TestCase;
 
class StubTest extends TestCase
{
    public function testThrowExceptionStub()
    {
        // Create a stub for the SomeClass class.
        $stub = $this->createMock(SomeClass::class);
 
        // Configure the stub.
        $stub->method('doSomething')
             ->will($this->throwException(new Exception));
 
        // $stub->doSomething() throws Exception
        $stub->doSomething();
    }
}
?>
```
Кроме того, вы можете написать PHPUnit заглушки самостоятельно. Доступ к широко используемым ресурсам осуществляется через единый фасад, поэтому вы можете легко заменить ресурс заглушкой. Например, вместо прямых обращений к базе данных, разбросанных по всему коду, у вас есть один объект Database, являющийся реализацией интерфейса IDatabase. Затем вы можете создать заглушку, реализующию IDatabase и использовать ее для ваших тестов. Вы даже можете создать опцию для запуска тестов с использованием заглушки для базы данных или реальной базы данных, что позволяет вам использовать свои тесты для локального тестирования во время разработки и тестирования интеграции с реальной базой данных.

Функциональность, для которой необходимо создать PHPUnit заглушки, как правило, группируются в одном объекте, улучшая сплоченность. Представляя функциональность с единым согласованным интерфейсом, вы уменьшаете связь с остальной частью системы.


### PHPUnit mock объекты. PHPUnit getMock
Практика замены объекта двойником, который проверяет ожидания, например, утверждения, что метод был вызван, называется имитацией (PHPUnit mock object — «объект-имитация»).

Вы можете использовать «объект-имитацию» (PHPUnit mock) в качестве точки наблюдения, которая используется для проверки косвенных выходов SUT при выполнении. Как правило, «объект-имитация» также включает в себя функциональность тестовой заглушки (в том, что он должен вернуть значения в SUT), но упор делается на проверку подставных (косвенных) результатов. Таким образом, «объект-имитация» намного больше, чем просто заглушка с утверждением; он используется принципиально иным способом.

#### Ограничение: автоматическая проверка ожиданий
>Только PHPUnit mock («объекты-имитации»), генерируемые в рамках теста, будут автоматически проверяться с помощью PHPUnit. «Объекты-имитации», созданные поставщиками данных, например, или введенные в тест с использованием аннотации @depends, не будут автоматически проверяться PHPUnit.

Вот пример: предположим, что мы хотим проверить правильность метода update(), в нашем примере, который вызывается для объекта, который наблюдает за другим объектом. В примере ниже, показан код для классов Subject и Observer, которые являются частью тестируемой системы (SUT).

```php
<?php
use PHPUnit\Framework\TestCase;
 
class Subject
{
    protected $observers = [];
    protected $name;
 
    public function __construct($name)
    {
        $this->name = $name;
    }
 
    public function getName()
    {
        return $this->name;
    }
 
    public function attach(Observer $observer)
    {
        $this->observers[] = $observer;
    }
 
    public function doSomething()
    {
        // Do something.
        // ...
 
        // Notify observers that we did something.
        $this->notify('something');
    }
 
    public function doSomethingBad()
    {
        foreach ($this->observers as $observer) {
            $observer->reportError(42, 'Something bad happened', $this);
        }
    }
 
    protected function notify($argument)
    {
        foreach ($this->observers as $observer) {
            $observer->update($argument);
        }
    }
 
    // Other methods.
}
 
class Observer
{
    public function update($argument)
    {
        // Do something.
    }
 
    public function reportError($errorCode, $errorMessage, Subject $subject)
    {
        // Do something
    }
 
    // Other methods.
}
?>
```
Теперь разберем пример использования PHPUnit mock и методов getMock, getMockBuilder и других
В примере ниже показано, как использовать подстановку (PHPUnit mock — «объект-имитацию») для проверки взаимодействия объектов Subject и Observer.

Сначала мы используем метод getMockBuilder(), который предоставляется классом PHPUnit\Framework\TestCase чтобы настроить PHPUnit mock для Observer. Поскольку мы предоставляем массив в качестве второго (необязательного) параметра для метода getMock(), только метод update() класса Observer заменяется реализацией «объектом-имитации».

Поскольку нам важно проверить вызываемый метод, и с какими аргументами он вызывается, мы используем методы expects() и with(), чтобы определить, как это взаимодействие должно выглядеть.

```php
<?php
use PHPUnit\Framework\TestCase;
 
class SubjectTest extends TestCase
{
    public function testObserversAreUpdated()
    {
        // Create a mock for the Observer class,
        // only mock the update() method.
        $observer = $this->getMockBuilder(Observer::class)
                         ->setMethods(['update'])
                         ->getMock();
 
        // Set up the expectation for the update() method
        // to be called only once and with the string 'something'
        // as its parameter.
        $observer->expects($this->once())
                 ->method('update')
                 ->with($this->equalTo('something'));
 
        // Create a Subject object and attach the mocked
        // Observer object to it.
        $subject = new Subject('My subject');
        $subject->attach($observer);
 
        // Call the doSomething() method on the $subject object
        // which we expect to call the mocked Observer object's
        // update() method with the string 'something'.
        $subject->doSomething();
    }
}
?>
```
Метод with() может принимать любое количество аргументов, соответствующих числу аргументов метода, который имитированный. Вы можете указать более сложные ограничения на аргументы метода, чем простое совпадение.

Пример тестирование вызова метода с несколькими аргументами (PHPUnit getMock())
```php
<?php
use PHPUnit\Framework\TestCase;
 
class SubjectTest extends TestCase
{
    public function testErrorReported()
    {
        // Create a mock for the Observer class, mocking the
        // reportError() method
        $observer = $this->getMockBuilder(Observer::class)
                         ->setMethods(['reportError'])
                         ->getMock();
 
        $observer->expects($this->once())
                 ->method('reportError')
                 ->with(
                       $this->greaterThan(0),
                       $this->stringContains('Something'),
                       $this->anything()
                   );
 
        $subject = new Subject('My subject');
        $subject->attach($observer);
 
        // The doSomethingBad() method should report an error to the observer
        // via the reportError() method
        $subject->doSomethingBad();
    }
}
?>
```
Метод withConsecutive() может принимать любое количество массивов аргументов, в зависимости от вызовов, которые вы хотите протестировать. Каждый массив представляет собой список ограничений, соответствующих аргументам метода, которые имитированы с with().

Пример тестирование вызова метода два раза с конкретными аргументами (PHPUnit getMock).

```php
<?php
use PHPUnit\Framework\TestCase;
 
class FooTest extends TestCase
{
    public function testFunctionCalledTwoTimesWithSpecificArguments()
    {
        $mock = $this->getMockBuilder(stdClass::class)
                     ->setMethods(['set'])
                     ->getMock();
 
        $mock->expects($this->exactly(2))
             ->method('set')
             ->withConsecutive(
                 [$this->equalTo('foo'), $this->greaterThan(0)],
                 [$this->equalTo('bar'), $this->greaterThan(0)]
             );
 
        $mock->set('foo', 21);
        $mock->set('bar', 48);
    }
}
?>
```
Ограничение callback() может использоваться для более сложной проверки аргументов. Это ограничение принимает функцию обратного вызова PHP (callback) в качестве единственного аргумента. Функция обратного вызова PHP получает аргумент, который будет проверен как единственный аргумент, и должен возвращать значение true, если аргумент передает проверку, и false в противном случае.

Более сложный пример (с использованием phpunit getMock())
Пример более сложной проверки аргументов (PHPUnit getMock()).

```php
<?php
use PHPUnit\Framework\TestCase;
 
class SubjectTest extends TestCase
{
    public function testErrorReported()
    {
        // Create a mock for the Observer class, mocking the
        // reportError() method
        $observer = $this->getMockBuilder(Observer::class)
                         ->setMethods(['reportError'])
                         ->getMock();
 
        $observer->expects($this->once())
                 ->method('reportError')
                 ->with($this->greaterThan(0),
                        $this->stringContains('Something'),
                        $this->callback(function($subject){
                          return is_callable([$subject, 'getName']) &&
                                 $subject->getName() == 'My subject';
                        }));
 
        $subject = new Subject('My subject');
        $subject->attach($observer);
 
        // The doSomethingBad() method should report an error to the observer
        // via the reportError() method
        $subject->doSomethingBad();
    }
}
?>
```
Пример тестирование метода который вызывается один раз и с идентичным объектом, какой был переданный.

```php
<?php
use PHPUnit\Framework\TestCase;
 
class FooTest extends TestCase
{
    public function testIdenticalObjectPassed()
    {
        $expectedObject = new stdClass;
 
        $mock = $this->getMockBuilder(stdClass::class)
                     ->setMethods(['foo'])
                     ->getMock();
 
        $mock->expects($this->once())
             ->method('foo')
             ->with($this->identicalTo($expectedObject));
 
        $mock->foo($expectedObject);
    }
}
?>
```
Создание PHPUnit mock объекта имитации с включенным параметрами клонирования

```php
<?php
use PHPUnit\Framework\TestCase;
 
class FooTest extends TestCase
{
    public function testIdenticalObjectPassed()
    {
        $cloneArguments = true;
 
        $mock = $this->getMockBuilder(stdClass::class)
                     ->enableArgumentCloning()
                     ->getMock();
 
        // now your mock clones parameters so the identicalTo constraint
        // will fail.
    }
}
?>
```
В таблице, которая будет показана в следующих статьях (при рассмотрении утверждения assertThat()), показаны ограничения, которые могут быть применены к аргументам метода.

PHPUnit mock сопоставления, доступные для указания количества вызовов
А в таблице указанной ниже — приведены сопоставления, доступные для указания количества вызовов.

Сопоставления (Matcher)	Описание
PHPUnit_Framework_MockObject_Matcher_AnyInvokedCount any()	Возвращает совпадение, которое соответствует, когда метод, для которого он вычисляется, выполняется 0 или более раз.
PHPUnit_Framework_MockObject_Matcher_InvokedCount never()	Возвращает совпадение, которое соответствует, когда метод, для которого он вычисляется, никогда не выполняется.
PHPUnit_Framework_MockObject_Matcher_InvokedAtLeastOnce atLeastOnce()	Возвращает совпадение, которое соответствует, когда метод, для которого он вычисляется, выполняется хотя бы один раз.
PHPUnit_Framework_MockObject_Matcher_InvokedCount once()	Возвращает совпадение, которое соответствует, когда метод, для которого он вычисляется, выполняется ровно один раз.
PHPUnit_Framework_MockObject_Matcher_InvokedCount exactly(int $count)	Возвращает совпадение, которое соответствует, когда метод, для которого он вычисляется, выполняется $count раз.
PHPUnit_Framework_MockObject_Matcher_InvokedAtIndex at(int $index)	Возвращает совпадение, которое соответствует, когда метод, для которого он вычисляется, вызывается при заданном $index.

#### Замечания
>Параметр $index для сопоставления (matcher) at() ссылается на индекс, начинающийся с нуля, во всех вызовах метода для данного объекта-имитации. Соблюдайте осторожность при использовании этого сопоставления, поскольку он может привести к «хрупким» тестам, которые слишком тесно связаны с определёнными деталями реализации.

Как упоминалось в начале, когда создания по умолчанию объекта-двойника методом createMock(), не соответствуют вашим потребностям, тогда вы можете использовать метод getMockBuilder($type) для настройки тестового двойника с использованием текучего интерфейса (fluent interface). Вот список методов, предоставленных Mock Builder:

setMethods(array $methods) может быть вызван из объекта Mock Builder для указания методов, которые должны быть заменены настраиваемым тестовым двойником. Поведение других методов не изменяется. Если вы вызовете setMethods(null), то никакие методы не будут заменены.
setConstructorArgs(array $args) может быть вызван для предоставления массива параметров, который передается конструктору исходного класса (который по умолчанию не заменяется фиктивной реализацией).
setMockClassName($name) может использоваться для указания имени класса для сгенерированного тестового двойного класса.
disableOriginalConstructor() может быть использован для отключения вызова конструктора исходного класса.
disableOriginalClone() может быть использован для отключения вызова клонирования конструктора исходного класса.
disableAutoload() может использоваться для отключения ~__autoload()~ во время генерации тестового двойника класса.
—

В данном уроке, мы рассмотрели создания PHPUnit mock объектов (тестовых двойников), методы phpunit getMock и phpunit getMockBuilder, и много другого.
__

### Использования PHPUnit Prophecy для Mock-объектов

Prophecy — это очень мощный и гибкий каркас PHP для Mock-объектов. Хотя первоначально он был создан для удовлетворения потребностей phpspec2, он достаточно гибок, чтобы его можно было использовать внутри любого каркаса тестирования с минимальными усилиями.

PHPUnit имеет встроенную поддержку использования Prophecy для создания тестовых двойников. Пример ниже демонстрирует, как тот же тест, показанный в примере из предыдущей статьи, может быть выражен с помощью философии Prophecy.

Пример использования PHPUnit Prophecy для тестирование, метод вызывается один раз и с указанным аргументом:

```php
<?php
use PHPUnit\Framework\TestCase;
 
class SubjectTest extends TestCase
{
    public function testObserversAreUpdated()
    {
        $subject = new Subject('My subject');
 
        // Create a prophecy for the Observer class.
        $observer = $this->prophesize(Observer::class);
 
        // Set up the expectation for the update() method
        // to be called only once and with the string 'something'
        // as its parameter.
        $observer->update('something')->shouldBeCalled();
 
        // Reveal the prophecy and attach the mock object
        // to the Subject.
        $subject->attach($observer->reveal());
 
        // Call the doSomething() method on the $subject object
        // which we expect to call the mocked Observer object's
        // update() method with the string 'something'.
        $subject->doSomething();
    }
}
?>
```
Дополнительную информацию о том, как создавать, настраивать и использовать заглушки, шпионы и имитации, можно найти в документации для Prophecy, используя этот альтернативный каркас для тестирования двойников.

—

Мы изучили использования PHPUnit Prophecy для создания Mock-объектов. Продолжение будет в следующих уроках

### PHPUnit имитация трейтов и абстрактных классов 

Метод PHPUnit getMockForTrait
Метод getMockForTrait() возвращает «объект-имитацию» (mock object), который использует указанный trait. Все абстрактные методы данного trait подвергаются имитированию. Это позволяет тестировать конкретные методы trait.

Пример тестирования конкретных методов trait:

```php
<?php
use PHPUnit\Framework\TestCase;
 
trait AbstractTrait
{
    public function concreteMethod()
    {
        return $this->abstractMethod();
    }
 
    public abstract function abstractMethod();
}
 
class TraitClassTest extends TestCase
{
    public function testConcreteMethod()
    {
        $mock = $this->getMockForTrait(AbstractTrait::class);
 
        $mock->expects($this->any())
             ->method('abstractMethod')
             ->will($this->returnValue(true));
 
        $this->assertTrue($mock->concreteMethod());
    }
}
?>
```
Метод PHPUnit getMockForAbstractClass

Метод getMockForAbstractClass() возвращает «объект-имитацию» (mock object) для абстрактного класса. Все абстрактные методы данного абстрактного класса имитируются. Это позволяет протестировать конкретные методы абстрактного класса.

Пример тестирования конкретных методов абстрактного класса:
```php
<?php
use PHPUnit\Framework\TestCase;
 
abstract class AbstractClass
{
    public function concreteMethod()
    {
        return $this->abstractMethod();
    }
 
    public abstract function abstractMethod();
}
 
class AbstractClassTest extends TestCase
{
    public function testConcreteMethod()
    {
        $stub = $this->getMockForAbstractClass(AbstractClass::class);
 
        $stub->expects($this->any())
             ->method('abstractMethod')
             ->will($this->returnValue(true));
 
        $this->assertTrue($stub->concreteMethod());
    }
}
?>
```
—
взято с сайта https://it-news.xyz/
