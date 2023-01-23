# Working of the Extension

The extension takes the Javascript for encryption and decryption and run the system command. Since the extension use JavaScript it requires NodeJS to run it.

```bash
node user/provided/javascript/file/path/encryption.js -d bas64-encodeed-encrypted-request-body
```
- The extension use ```-d``` command line argument to provide the body to the JS code. If you are writing the JS script you have to make sure you allow extension to support command line arguments.
- The value of ```-d``` will be base64. If your orignal request is already in base64 the extension will still perform the bas64 encoding and will providee the base64 encode value to the ```JavaScript``` code. To the orignal value of your request body or paramter you have base64 decode the value in your JavaScript file first.
- The base64 encoding is done by the extension to avoid any kind of string related issues like spaces or special character.
- The Javascrript code can perfrom any action or add any logic on the value, the extension will read the system command output so your has to print the updated value using ```console.log("updated value")
- This approch and code will be same for all ```Request Type``` in the extension excluding ```Custom Request``` type in the extension 


!>Since the extension read the system command output, there should not be more than 1 ```console.log``` in the code

