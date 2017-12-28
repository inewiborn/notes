```php
	public function test_method() {
		$stubObject = $this->createMock(ClassForStub::class);

		$stubObject->expects($this->any())
			->method('fakeMethod')
			->will($this->returnValue(1);
	}
```

Метод expects - необязательный метод для дополнительного ограничения по вызову метода
<table border="1">
	<tr>
		<td>
			any()
		</td>
		<td>
			Не сделан ни один вызов соответствующего метода
		</td>
	</tr>
	<tr>
		<td>
			never()
		</td>
		<td>
			Не сделан ни один вызов соответствующего метода
		</td>
	</tr>
	<tr>
		<td>
			atLeastOnce()
		</td>
		<td>
			Сделан один или больше вызовов соответствующего метода 
		</td>
	</tr>
	<tr>
		<td>
			once()
		</td>
		<td>
			Сделан единственный вызов соответствующего метода
		</td>
	</tr>
	<tr>
		<td>
			exactly(int $count)
		</td>
		<td>
			Сделано $count вызовов соответствующего метода 
		</td>
	</tr>
	<tr>
		<td>
			at(int $index)
		</td>
		<td>
			Вызов соответствующего метода сделан по индексу $index (каждый вызов метода для сымитированного объекта записывается и индексируется) 
		</td>
	</tr>
</table>

Нужно привязать ограничение к нужному методу
__method($nameMethod)__

Далее можно сделать ограничения на параметры 
__with__($this->equalTo('param') или __with__('param')

Далее метод для возвращаемого значения
* __will__($this->returnValue('value')) __willReturn__('value') 
* __will__($this->returnArgument(0)) 
* __will__($this->returnSelf()) 
* __will__($this->throwException(new Exception)) 
 
```php
	$stub = $this->getMockBuilder($originalClassName)
		->disableOriginalConstructor()
		->disableOriginalClone()
		->disableArgumentCloning()
		->disallowMockingUnknownTypes()
		->getMock();
```
