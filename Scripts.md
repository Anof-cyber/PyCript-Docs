# Working of the Extension

The extension takes the Javascript for encryption and decryption and run the system command. Since the extension use JavaScript it requires NodeJS to run it.

```bash
node user/provided/javascript/file/path/encryption.js -d bas64-encodeed-encrypted-request-body
```
- The extension use ```-d``` command line argument to provide the body to the JS code. If you are writing the JS script you have to make sure you allow extension to support command line arguments.
- The value of ```-d``` will be base64. If your orignal request is already in base64 the extension will still perform the bas64 encoding and will providee the base64 encode value to the ```JavaScript``` code. To the orignal value of your request body or paramter you have base64 decode the value in your JavaScript file first.
- The base64 encoding is done by the extension to avoid any kind of string related issues like spaces or special character.
- The Javascrript code can perfrom any action or add any logic on the value, the extension will read the system command output so your has to print the updated value using ```console.log("updated value")
- This approch and code will be same for all ```Request Type``` in the extension excluding ```Custom Request``` type in the extension.
- The same logic is applicable for encryption and decryption JavaScript Code
- The output of the ```console.log()``` should be the orignal output which you want to see in the burp. So the output will not de base64 decoded by the extension. Instead the output will be directly replaced in the request.

```bash
node user/provided/javascript/file/path/encryption.js -d bas64-encodeed-encrypted-request-body
```
```bash
node user/provided/javascript/file/path/decryption.js -d bas64-encodeed-decrypted-request-body
```

!>Since the extension read the system command output, there should not be more than 1 ```console.log``` in the code

```javascript
var CryptoJS = require("crypto-js");
const program = require("commander");
const { Buffer } = require('buffer');
program
  .option("-d, --data <data>", "Data to process")
  .parse(process.argv);
  
const options = program.opts();
const requestbody = Buffer.from(options.data, 'base64').toString('utf8');

'Your encryption and decryption logic should be here'

console.log(Output)
```

- In above code we are using cryptojs library you can use any other based your choice.
- The script code is also using command library to allow our code to implement the command line argument.
- Sice our body will be base64 encode we have buffer library to base64 decode the body.
- We are using the command library to add ```-d``` as command line argument in our script
- Later we are reading the value of of ```-d``` and then using buffer library to decode the value with base64.
- After that you can add your encryption or decryption logic.
- Lastly we have to print the output of encrypted or decrypted text using ```console.log()```

## Custom Request Type

There are some change if you write the JavaScript code for Custom request. All the above mentioned points will be applicable here will some additional details.

```bash
node user/provided/javascript/file/path/encryption.js -d bas64-encodeed-encrypted-request-body -h [[POST /crypto/user/ HTTP/1.1, Host: localhost:8000, Key: 1234, Iv: 1234]
```
```bash
node user/provided/javascript/file/path/decryption.js -d bas64-encodeed-decrypted-request-body -h [POST /crypto/user/ HTTP/1.1, Host: localhost:8000, Key: 1234, Iv: 1234]
```
- The extension will again run the system command with base64 encoded body using ```-d```
- The extension will also provide HTTP header to the JS code. The header will not be base64 encoded you can directly read the header.
- The extension doesn't allow you to modify the header values using JavaScript code and update the header in your burp request.
- Since header is not allowed to modify the there should be only 1 ```console.log()``` in your JS code
- You can read the output of the header provided to the JS code of your current request in the Extender tab and in the output section of the PyCript extension

```http
[POST /crypto/myprofile/ HTTP/1.1, Host: localhost:8000, Accept-Encoding: gzip, deflate, Accept: */*, Accept-Language: en-US;q=0.9,en;q=0.8, User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/103.0.5060.134 Safari/537.36, Connection: close, Key: 1234, Iv: 1234, Cache-Control: max-age=0, Content-Type: application/x-www-form-urlencoded, Content-Length: 24]

```

```javascript
var CryptoJS = require("crypto-js");
const program = require("commander");
const { Buffer } = require('buffer');
program
  .option("-d, --data <data>", "Data to process")
  .option("-h, --header <data>", "Header")
  .parse(process.argv);
  
const options = program.opts();
const requestbody = Buffer.from(options.data, 'base64').toString('utf8');
const header = options.header

'Your encryption and decryption logic should be here'

console.log(Output)
```

- The code is same as previous code. Since Custom request requires header we have another command line argument as ```-h```
- Lastly we are reading the output of the header value and the rest of the code is same.
