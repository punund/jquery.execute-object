# jQuery Execute Object plugin

This is a tiny jQuery plugin that enables you to interpret an object as DOM manipulation instructions with a single function call.

## Usage

```javascript
$.executeObject({"#my_div": ["text", "new text"]})   // object passed
$.executeObject('{"#my_div": ["text", "new text"]}') // JSON is ok too
```

## Use cases

Instead of

```javascript
$('table tr:first').remove();
$('input#name').val('John');
$('#my_div').addClass('error').text('Error occured');
```
create an object and pass it to the executeObject function:

```javascript
var obj =
  { 'table tr:first': 'remove'
  , 'input#name': ['val', 'John']
  , '#my_div':
    [ ['addClass', 'error']
    , ['text', 'Error occured']
    ]
  }

$.executeObject(obj)
```
Specifically, if you find yourself creating horrendous view templates in vein of

```erb
$('input#name').val('<%= escape_javascript(@person.name) %>')
$('#my_div').addClass('error').text('<%= escape_javascript(@error) %>')
```

you may simply shift this logic to the controller (example in ruby):
```ruby
obj = {'input#name' => ['val', @person.name], 
  '#my_div' => [%w[addClass error], ['text', @error]]}
  
render js: "$.executeObject( #{obj.to_json} )"
# note that we don't need extra quotes inside function call
```
and scratch the view altogether.  Javascript passed from the server is notoriously hard to debug, so let's pass an object instead.  You may also indicate $.executeObject as a callback function for your ajax call
```javascript
$.post('/my/url', {some: 'data'}, $.executeObject)
```

and simply pass JSON in response:
```ruby
render json: obj
```

In other words, it's always better to pass around data, and not code.

## Return value
The function returns the number of jQuery method calls that it was able to perform.  Wrong methods or empty selectors fail silently and execution continues.


