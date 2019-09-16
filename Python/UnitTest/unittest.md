
# Basic Concepts

* **test fixture**: A test fixture represents the preparation needed to perform one or more tests, and any associate cleanup actions. This may involve, for example, creating temporary or proxy databases, directories, or starting a server process.
* **test case**: A test case is the individual unit of testing. It checks for a specific response to a particular set of inputs. unittest provides a base class, TestCase, which may be used to create new test cases. Usually, you can think one test class is a test case.
* **test suite**: A test suite is a collection of test cases, test suites, or both. It is used to aggregate tests that should be executed together.
* **test runner**:

# Functionalities

## Skipping tests and expected failures

```python
class MyTestCase(unittest.TestCase):

    @unittest.skip("demonstrating skipping")
    def test_nothing(self):
        self.fail("shouldn't happen")

    @unittest.skipIf(mylib.__version__ < (1, 3),
                     "not supported in this library version")
    def test_format(self):
        # Tests that work for only a certain version of the library.
        pass

    @unittest.skipUnless(sys.platform.startswith("win"), "requires Windows")
    def test_windows_support(self):
        # windows specific testing code
        pass

    def test_maybe_skipped(self):
        if not external_resource_available():
            self.skipTest("external resource not available")
        # test code that depends on the external resource
        pass
```

Expected failures use the expectedFailure() decorator.

## The patchers
The patch decorators are used for patching objects only within the scope of the function they decorate. **So it's really useful when you want to limit the mock scope**.

```python
class ExpectedFailureTestCase(unittest.TestCase):
    @unittest.expectedFailure
    def test_fail(self):
        self.assertEqual(1, 0, "broken")
```

# Mock unittest.mock
`unittest.mock` provides a core Mock class removing the need to create a host of stubs throughout your test suite. After performing an action, you can make assertions about which methods / attributes were used and arguments they were called with. You can also specify return values and set needed attributes in the normal way.

Additionally, mock provides a `patch()`` decorator that handles patching module and class level attributes within the scope of a test, along with `sentinel` for creating unique objects.

## Quick Guide

Basic example:
```python
from unittest.mock import MagicMock
thing = ProductionClass()
thing.method = MagicMock(return_value=3)
thing.method(3, 4, 5, key='value') # Return 3
thing.method.assert_called_with(3, 4, 5, key='value')
```]

`side_effect` allows you to perform side effects, including raising an exception when a mock is called:
```python
mock = Mock(side_effect=KeyError('Foo'))
mock() # Raise exception
```

```python
values = {'a': 1, 'b': 2, 'c': 3}
def side_effect(arg):
  return values[arg]

mock.side_effect = side_effect
mock('a'), mock('b'), mock('c') # Return (1, 2, 3)
mock.side_effect = [5, 4, 3, 2, 1]
mock(), mock(), mock() # Return (5, 4, 3)
```

## The patchers
The patch decorators are used for patching objects only within the scope of the function they decorate. They automatically handle the unpatching for you, even if exceptions are raised. So it's really easy to define the boundaries.

> `patch()` is straightforward to use. The key is to do the patching in the right namespace.

### `patch()`
The `patch()` decorator/context manager makes it easy to mock classes or objects in a module under test. The object you specify will be replaced with a mock(or other object) during the test and **restored** when the test ends.

```python
from unittest.mock import patch
@patch('module.ClassName2')
@patch('module.ClassName1')
def test(MockClass1, MockClass2):
  module.ClassName1()
  module.ClassName2()
  assert MockClass1 is module.ClassName1
  assert MockClass2 is module.ClassName2
  assert MockClass1.called
  assert MockClass2.called
```

### Use `patch` module
```python
with path.object(ProductionClass, 'method', return_value=None) as mock_method:
  thing = ProductionClass()
  thing.method(1, 2, 3) # Return None
mock_method.assert_called_once_with(1, 2, 3)
```

There is also patch.dict() for setting values in a dictionary just during a scope and restoring the dictionary to its original state when the test ends:
```python
>>> foo = {'key': 'value'}
>>> original = foo.copy()
>>> with patch.dict(foo, {'newkey': 'newvalue'}, clear=True):
...     assert foo == {'newkey': 'newvalue'}
...
>>> assert foo == original
```

For ensuring that the mock objects in your tests have the same api as the objects they are replacing, you can use `auto-speccing`. Auto-speccing can be done through the `autospec` argument to patch, or the `create_autospec()` function. Auto-speccing creates mock objects that have the same attributes and methods as the objects they are replacing, and any functions and methods (including constructors) have the same call signature as the real object.
```python
>>> from unittest.mock import create_autospec
>>> def function(a, b, c):
...     pass
...
>>> mock_function = create_autospec(function, return_value='fishy')
>>> mock_function(1, 2, 3)
'fishy'
>>> mock_function.assert_called_once_with(1, 2, 3)
>>> mock_function('wrong arguments')
Traceback (most recent call last):
:TypeError method takes exactly 3 arguments (1 given)
```

### patch Decorator
`unittest.mock.patch(target, new=DEFAULT, spec=None, create=False, spec_set=None, autospec=None, new_callable=None, **kwargs)`

* `patch()` acts as a function decorator, class decorator or a context manager. Inside the body of the function or with statement, the target is patched with a `new` objectself.

* If `new` parameter is not given, then the target is replaced with a `MagicMock`.
* `target` should be a string in the format `package.module.ClassName`. The target is imported when the decorator is called, not the decoration time.
* The `spec` and `spec_set` keyword arguments are passed to the `MagicMock` if patch is creating one for you
* You can pass `spec=True` or `spec_set=True` which causes patch to pass in the object being mocked as the `spec/spec_set` object. * `new_callable` allows you to specify a different class, or callable object, that will be called to crate the `new` object.
* A powerful form of spec is autospec. If you set `autospec=True` then the mock will be created with a spec form the object being replaced. All attributes of the mock will also have the spec of the corresponding attribute of the object being replaced. Method being mocked will have their argument checked and will raise `TypeError` if they are called with the wrong signature, which means the mock method have the same signature with the original ones.
* Instead of `autospec=True` you can pass `autospec=some_object` to use an arbitrary object as the spec instead of the one being replaced.
* By default `patch()` will fail to replace attributes that don't exist. If you pass in `create=True`, patch will create the attribute and delete if again after the patched function exit.
* patch can be used as a `TestCase` decorator. It works by decorating each test method in the class. This reduces duplicated work.

```python
@patch('__main__.SomeClass')
def function(normal_argument, mock_class):
  print(mock_class is SomeClass)
function(None) # Return True
```
If the class is instantiated multiple times you can use `side_effect` to return a new mock each time. Alternatively you can set the `return_value` to be anything you want.
```python
class Class:
  def method(self):
    pass
with patch('__main__.Class') as MockClass:
  instance = MockClass.return_value
  instance.method.return_value = 'foo'
  assert Class() is instance
  assert Class().method() == 'foo'
```

```python
from io import StringIO
def foo():
  print('Something')
@patch('sys.stdout', new_callable=StringIO)
def test(mock_stdout):
  foo()
  assert mock_stdout.getvalue() == 'Somthing\n'
test()
```


```python
# This is not easy to understand and error prone. I don't think this is a good way.
>>> config = {'method.return_value': 3, 'other.side_effect': KeyError}
>>> patcher = patch('__main__.thing', **config)
>>> mock_thing = patcher.start()
>>> mock_thing.method()
3
>>> mock_thing.other()
Traceback (most recent call last):
  ...
KeyError
```

### patch.object
You can use `patch.object` to mock static method.
```python
>>> @patch.object(SomeClass, 'class_method')
... def test(mock_method):
...     SomeClass.class_method(3)
...     mock_method.assert_called_with(3)
...
>>> test()
```

### patch.dict
This can be used to mock dict object
```python
>>> import os
>>> with patch.dict('os.environ', {'newkey': 'newvalue'}):
...     print(os.environ['newkey'])
...
newvalue
>>> assert 'newkey' not in os.environ
```

## Patch Examples
If you are patching a module(including builtins), use patch(), instead of `patch.object()`

### patch()
** Mock builtin functions **
```python
mock = MagicMock(return_value=sentinel.file_handle)
with patch("builtins.open", mock): # Second parameter is mock object
  handle = open('filename', 'r')
mock.assert_called_with('filename', 'r')
assert handle == sentinel.file_handle
```

### patch.object()
**Mock class static method**
```python
>>> original = SomeClass.attribute
>>> @patch.object(SomeClass, 'attribute', sentinel.attribute) # Second parameter is method name, third parameter is mock object
... def test():
...     assert SomeClass.attribute == sentinel.attribute
...
>>> test()
>>> assert SomeClass.attribute == original
```

** If you want to patch with a Mock, you can use patch() with only one argument or patch.object() with two argument. The mock will be created for you and passed into the test function**

```python
class MyTest(unittest.TestCase):
  @patch.object(SomeClass, 'static_method'):
  SomeClass.static_method()
  mock_method.assert_called_with()
```

** Multiple mocks **
```python
>>> class MyTest(unittest.TestCase):
...     @patch('package.module.ClassName1')
...     @patch('package.module.ClassName2')
...     def test_something(self, MockClass2, MockClass1):
...         self.assertIs(package.module.ClassName1, MockClass1)
...         self.assertIs(package.module.ClassName2, MockClass2)
```

**with word **
```python
>>> class Foo:
...   def foo(self):
...     pass
...
>>> with patch.object(Foo, 'foo', autospec=True) as mock_foo:
...   mock_foo.return_value = 'foo'
...   foo = Foo()
...   foo.foo()
...
'foo'
>>> mock_foo.assert_called_once_with(foo)
```

### How to mock `open`
https://stackoverflow.com/questions/1289894/how-do-i-mock-an-open-used-in-a-with-statement-using-the-mock-framework-in-pyth/19146253#19146253

```python
>>> from mock import mock_open, patch
>>> m = mock_open()
>>> with patch('builtins.open'.format(__name__), m, create=True):  # I change to 'builtins.open', it worked
...    with open('foo', 'w') as h:
...        h.write('some stuff')

>>> m.assert_called_once_with('foo', 'w')
>>> handle = m()
>>> handle.write.assert_called_once_with('some stuff')
```

### How to mock constructor
The key is to import that carrier after the mock.
```python
with patch.object(RunParametersHolderBuilder, "build") as m1:
    with patch("oih_revenue_analysis_tool.engine.analysis_processor.AnalysisProcessor") as MockAnalysis:
        with patch("oih_revenue_analysis_tool.engine.calculation_processor.CalculationProcessor") as MockCalc:
            from oih_revenue_analysis_tool.engine.engine import AnalysisEngine # Besure to import this after mock.
            engine = AnalysisEngine(MockGeneraOptions())
            mock_calc = MockCalc.return_value
            mock_calc.process_and_return_audit_detail_info_holder.return_value = "holder"

            mock_analysis = MockAnalysis.return_value
            mock_analysis.process_and_return_reports.return_value = "reports"

            engine.start()
```
