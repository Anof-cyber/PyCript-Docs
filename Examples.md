# Flow of the Extension

- Load the Encryption and Decryption JavaScript File in the extension
- Select the Request Type according to your request and encryption
- Add the URL in Burp Suite Scope
- Go to Repeater, Proxy Intercept, Proxy History,Intruder, Target
- You can see a new tab in the reequest message named PyCript
- If everyting is setup correctly and extnsion is given correct encryption decryption code you see the decrypted text
- Edit the decrpyted text and send the request
- The extension will take care of encrpytion of your modified value

# Config

The config tab has all the settings you will be required to perform the encryption and decryption. The PyCript extension encodes the request body of a message in Burp Suite using base64 encoding. It then provides both the headers and encoded body to JavaScript code. The JavaScript code is responsible for decrypting or encrypting the request body. After the JavaScript code is finished, the extension retrieves the updated body and updates the request in Burp Suite.

!>The extension works only on URL added in the burp suite Scope



## Request Type

The Request Type allows you to select the type of request or message that needs to e encrypted or decrypt.

- ### Whole Body (JSON) and Custom Body

This type can be used if your complete request body is encrypted. The extension will take the body and base64 encode it and will send it to the JavaScript code.

```http
POST /crypto/myprofile/ HTTP/1.1
Host: localhost:8000
Accept-Encoding: gzip, deflate
Accept: */*
Accept-Language: en-US;q=0.9,en;q=0.8
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/103.0.5060.134 Safari/537.36
Connection: close
Cache-Control: max-age=0
Content-Type: application/x-www-form-urlencoded
Content-Length: 24

KtKB81Oamvuzo9entPFKZQ==
```

The above request has a completely encrypted body. If the Decrypted output is in JSON format then we can use ```Whole Body (JSON)``` else we can use ```Custom Body```.


<iframe width="560" height="315" src="https://www.youtube.com/embed/VSrrzB83uHo" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

?> **IMPORTANT:** The only difference between Whole Body (JSON) and Custom Body is Whole Body (JSON) expects the decrypted output in JSON whereas Custom Body doesn't have any validation for output.
***



- ### JSON Value

This type can be used if your request body is in JSON Format but only values are encrypted. The extension will go through each value and will perform the encryption decryption on each value one by one. each value will be base64 encoded and will send to the JavaScript code.

```http
POST /crypto/login/ HTTP/1.1
Host: localhost:8000
Accept-Encoding: gzip, deflate
Accept: */*
Accept-Language: en-US;q=0.9,en;q=0.8
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/103.0.5060.134 Safari/537.36
Connection: close
Cache-Control: max-age=0
Content-Type: application/x-www-form-urlencoded
Content-Length: 77

{"username":"HgE40uCnUDbOfuat6om5QA==","password":"FZbf3I/h9ORUV+H6Wzv4gA=="}
```

The above request has only value encrypted and the ```JSON Value``` type can be used.

?> **IMPORTANT:** You can still use Whole Body (JSON) and Custom Body in this type of request but in that case, you will be required to add the logic in your JavaScript code to go through each value and provide the updated request body.
***



- ### JSON Key & Value

This type can be used if your request body is in JSON Format with the key and value both encrypted. The extension will go through each key and value pair and will perform the encryption decryption on each key and value one by one. each key and value will be base64 encoded and will send to the JavaScript code.

```http
POST /crypto/login/ HTTP/1.1
Host: localhost:8000
Accept-Encoding: gzip, deflate
Accept: */*
Accept-Language: en-US;q=0.9,en;q=0.8
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/103.0.5060.134 Safari/537.36
Connection: close
Cache-Control: max-age=0
Content-Type: application/x-www-form-urlencoded
Content-Length: 77

{"I8DMuVVrqGFOFsHLCjZDog==":"HgE40uCnUDbOfuat6om5QA==","JWJ3uwqkCOenhQxZuS9ILA==":"FZbf3I/h9ORUV+H6Wzv4gA=="}
```

The above request is same as JSON Value but with key and value both are encrypted and the ```JSON Key & Value``` type can be used.

?> **IMPORTANT:** You can still use Whole Body (JSON) and Custom Body in this type of request but in that case, you will be required to add the logic in your JavaScript code to go through each key and value and provide the updated request body.
***


- ### Custom Request

The custom request is useful when you need to perform any action using javascript code like the body is encrypted but the key or IV is in the header and you can't use Whole Body (JSON) or Custom Body option.

The Custom Request type will take the request body and will encode the request body with base64 and will provide it to the JavaScript code. The extension will also take the Request header and which will be also provided to JavaScript code and you can use Javascript code to get the key and IV from the header.

```http
POST /crypto/myprofile/ HTTP/1.1
Host: localhost:8000
Accept-Encoding: gzip, deflate
Accept: */*
Accept-Language: en-US;q=0.9,en;q=0.8
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/103.0.5060.134 Safari/537.36
Connection: close
Cache-Control: max-age=0
Key: 1234
Iv: 1234
Content-Type: application/x-www-form-urlencoded
Content-Length: 24

KtKB81Oamvuzo9entPFKZQ==
```

The above request is the same as Whole Body (JSON) and Custom Body but with key and IV in the header hence you can't keep iv and key hardcoded in your JS file. The ```Custom Request``` type can be used here to get headers and in JS we can retrieve the key and iv.


!>The extension will only take the output of the modified body from the JS. If you need to modify the headers as well the extension doesn't support it as of version 0.1.


- ### None

This Request type will inform the extension that the current status is OFF and the extension will not try to encrypt or decrypt the request.


!>The extension will not allow the selection of the ```Request Type``` Unless Encryption and Decryption JS files are not selected.



## Auto Encrypt

The auto encrypts request will allow you to encrypt the request. You can select the tool type for which the request needs to be encrypted. This feature is useful to perform automated scans like Burp Suite Active Scan or SQLMAP.

If you have an encrypted request and you need to run the burp suite scanner on that request. It's not directly since any payload added in plain text format to any encrypted parameter server will reject the request and will give you an error as decryption at the server side is failed.

In that case, you can send the decrypted request to the scanner and turn on the Auto Encrypt and also select the Tool Type for the scanner.

The Scanner will get the decrypted request to add any payload. The extension will encrypt the request (Based on the Request Type selected) before the scanner sends it to the server.

Similarly, you can provide the decrypted request to Sqlmap and configure Sqlmap to send all requests to the burp proxy. Also, turn on the Auto Encrypt.

!>The extension will not allow to turn on ```Auto Encrypt``` unless ```Request Type``` is not selected excluding None Also at least one ```Tool type``` is required





## Encryption Decryption File

To encrypt and decrypt messages, you will need to choose two separate files; one for encryption and one for decryption.

?> **IMPORTANT:** The extension requires the encryption and decryption file before doing anyting.
***




# Additional Features

## Decrypt Request


The PyCript extension provides the ability to automatically decrypt multiple requests. This eliminates the need for manually decrypting individual requests and then sending them to the scanner. To use this feature:

- Open Burp Suite and navigate to the ```"Proxy", "Target", "Intruder" or "Repeater"``` tab.

- Right-click on the request you wish to decrypt and select ```"Extensions"``` from the menu.

- From the submenu, select ```"PyCript"```

- Select ```"Decrypt Request"``` from the options presented.

- The PyCript extension will now decrypt all the requests and will store the decrypted requests in the ```"Decrypted Requests"``` tab inside the table.

- Navigate to the ```PyCript``` Tab and inside select the ```"Decrypted Requests"```

- It will display all the decrpyted request.

- Select any row or multiple rows Right-click. The extension will allow you to send all the selected decrypted requests to Repeater, Intruder, Scanner or Resend the Request.

- The Resend HTTP Request can send the HTTP request to the server with the decrypted request. You can view that request from the logger or use an upstream proxy to send the request to another Burp Suite.

<iframe width="560" height="315" src="https://www.youtube.com/embed/DWzI7JbPEkI" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>


## Encrypt String

The extension allows you to encrypt any string you select within the request and response. 


- Open Burp Suite and navigate to the ```"Proxy", "Target", "Intruder" or "Repeater"``` tab.

- Select anyting within request or response.

- Right-click on the request you wish to encrpyt and select ```"Extensions"``` from the menu.

- From the submenu, select ```"PyCript"```

- Select ```"Encrypt String"``` from the options presented.

- The extension will show a Popup with the encrpyted output for your selected string.

<iframe width="560" height="315" src="https://www.youtube.com/embed/Hkfqb_npfeg" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>



## Decrypt String

The extension allows you to decpryt any string you select within the request and response. 


- Open Burp Suite and navigate to the ```"Proxy", "Target", "Intruder" or "Repeater"``` tab.

- Select anyting within request or response.

- Right-click on the request you wish to decrypt and select ```"Extensions"``` from the menu.

- From the submenu, select ```"PyCript"```

- Select ```"Decrypt String"``` from the options presented.

- The extension will show a Popup with the Decrypted output for your selected string.


# Decrypted Request

The tab will is used to store the decrypted reequest. Once you select ```Decrypt Request``` from the menu. The eextension will store the decryptd request here. Later you can select one or multiple row and right click to perfrom the action such as send the request to ```Repeater, Intruder, Scanner, or Send the decrypted request to the server```

