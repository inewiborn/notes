```php
<?php
//file FirstTest.php
use PHPUnit\Framework\TestCase;
class FirstTest extends TestCase {
	public function test_firstTest()
	{
		$this->assertTrue(true);
	}
}
```
<table border="1">
<tr>
	<td>
		assertEquals($vall, $val2,  $delta,  $message) 
	</td>
	<td>
		Заканчивается неудачей, если $vall не эквивалентен $val2 ($delta представляет допустимую погрешность) 
	</td>
</tr>
<tr>
	<td>
		assertFalse ($expression, $message) 
	</td>
	<td>
		Вычисляет выражение $expression. Заканчивается неудачей, если не обращается в значение "ложь" 
	</td>
</tr>
<tr>
	<td>
		assertTrue ($expression, $message) 
	</td>
	<td>
		Вычисляет выражение $expression. Заканчивается неудачей, если не обращается в значение "истина"
	</td>
</tr>
<tr>
	<td>
		assertNotNull($val,  $message) 
	</td>
	<td>
		Заканчивается неудачей, если $val - нуль
	</td>
</tr>
<tr>
	<td>
		assertNull($val,  $message) 
	</td>
	<td>
		Заканчивается неудачей, если $val отличен от нуля 
	</td>
</tr>
<tr>
	<td>
		assertSame ($vall,  $val2, $message) 
	</td>
	<td>
		Заканчивается неудачей, если $val 1 и $val2 не являются ссылками на один и тот же объект или если они -
	</td>
</tr>
<tr>
	<td>
		assertNotSame ( $val 1,  $val2, $message) 
	</td>
	<td>
		переменные, имеющие разные типы и значения Заканчивается неудачей, если $vall и $val2 являются ссылками на один и тот же объект или если они - переменные, имеющие одинаковые типы и значения
	</td>
</tr>
<tr>
	<td>
		assertRegExp($regexp, $val,  $message) 
	</td>
	<td>
		Заканчивается неудачей, если $val не соответствует регулярному выражению $regexp 
	</td>
</tr>
<tr>
	<td>
		assertType ($typestring, $val,  $message) 
	</td>
	<td>
		Заканчивается неудачей, если $val имеет не тот тип, который описан в $typestring 
	</td>
</tr>
<tr>
	<td>
		assertAttributeSame ($val, $attribute,  $classname,  $message) 
	</td>
	<td>
		Заканчивается неудачей, если $val имеет не такие тип и значение, как $classname::$аttribute 
	</td>
</tr>
<tr>
	<td>
		fail () 
	</td>
	<td>
		Заканчивается неудачей
	</td>
</tr>
</table>
