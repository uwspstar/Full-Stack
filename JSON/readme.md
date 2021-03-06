# JSON
### Working with JSON
- Since JSON is nothing more than a string, it can be used to store data in a text file or
transfer data via an HTTP requests between two machines.

### JavaScript provides two methods for working with JSON. 
- The first is ```JSON.stringify``` and the second is ```JSON.parse```. 
- ```JSON.stringify``` converts a JavaScript object into a JSON string
- ```JSON.parse``` converts a JSON string into a JavaScript object

### JSON.stringify
- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify
- ```console.log(JSON.stringify(user));```
- https://medium.com/javascript-in-plain-english/5-secret-features-of-json-stringify-c699340f9f27
- In JSON, functions are not allowed as object values. The JSON.stringify() function will remove any functions from a JavaScript object, both the key and the value:

### JSON.parse()
- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse
- https://www.w3schools.com/js/js_json_parse.asp
- JSON.parse() does not allow single quotes
```
// will throw a SyntaxError
JSON.parse("{'foo': 1}");
```


		
	
	
		


		
	
	
		
			
				
					
						



				

			

		
