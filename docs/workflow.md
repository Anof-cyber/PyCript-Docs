## Flow of the Extension


PyCript utilizes the system command to run encryption and decryption scripts that are specified by the user. Depending on the chosen configuration, the extension acquires HTTP request data such as the body, string, parameters, headers, and so on, and then transfers the information to the designated user-provided encryption decryption file. Once the command has been executed, the extension anticipates the command output, which should be either encrypted or decrypted data. The extension then proceeds to read the command output and adjust the request/response within Burp Suite.


## Commands


The PyCript extension generates a temp file (from version 0.4) and stores the request body/parameter and header in JSON format. The extension passes the temp file location to the encryption-decryption script using -d command line argument. The JSON data will be based on the PyCript configuration.


* node script.js -d tempfile-path
* python script.py -d tempfile-path
* java -jar script.jar -d tempfile-path

The temp file will have encrypted/decrpyted paramter or request body in JSON data key and request header in JSON header key as below.

```json
    
{"data":"base64-encoded-paramter/or/body","header":"base64-encoded-headers"}
    
```


!!! info "Note"

    header will only be present if Custom request is selected in PyCript (Check writing custom script for details)

!!! info "Note"

    both header and data value will be base64 encoded prevent errors that may occur due to special characters. Make sure to decode the value in your script.

!!! info "Note"
    
    Based on the configuration provided, this extension will offer different values for the JSON data key.


* Complete Body - The extension will take the request/response body and will pass the value to JSON data key
- Paramter Value - PyCript is designed to iterate through the request/response parameter values and pass each value to JSON data key. It will then update each value one by one.

- Paramter key and value - The PyCript extension adopts a similar approach to that of parameter value. It iterates through each parameter name and value, passing them one by one to the script for updating.


PyCript offers two methods to work with headers. The first method enables you to read headers in your script, which is useful if your application sends the encryption key or initialization vector (IV) in the header and each request has a different key or IV. However, this method does not allow you to update the header. The extension expects the script to only return the updated value of JSON data key. Additionally, the JSON header key will be in a list/array format provided by Burp APIs.

The second method allows you to update the headers as well and can be useful where some application implementat the signature verification in headers that store the signaure like hash of the request body and won't allow you edit the paramter as signature mismach.

1. Read Headers
    * When we select Custom Request in Request Type the temp file will have header in array format like: ``['GET / http/1.1','Host: localhost']``
        * Remember it will be in base64 format so get array first base64 decode the value
        
2. Edit Headers
    * When we select Custom Request(Edit Header) in Request Type the temp file will have header in raw HTTP format like
        * Remember it will be in base64 format so get raw format first base64 decode the value.

    ```html
    POST / HTTP/1.1
    Host: google.com
    Accept-Encoding: gzip, deflate, br
    Accept: */*
    Accept-Language: en-US;q=0.9,en;q=0.8
    User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/126.0.6478.127 Safari/537.36
    Connection: close
    Cache-Control: max-age=0
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 9

    ```
    


    


## Usage

* Start by loading the PyCript extension into Burp Suite.
* Choose the language you want to use for encryption and decryption. **(See Select Language for more details)**
* Load the encryption and decryption file.
* Decide whether you want to apply encryption and decryption to the request, response, or both, and select the appropriate type.
* For request encryption and decryption, specify the method you want to use (GET, BODY, or Both).
* Select the Request or Response Type like Complete Body, Paramter Value etc.
* Make sure the URL you want to work with is within the scope of Burp Suite.
* Once you've located the encrypted request or response, a new PyCript tab will be generated next to it, with a plaintext version that you can edit as needed.


## Parameters

It is important to verify the supported request parameters or content types by PyCript. The following methods encompass the content types and parameters supported by PyCript:

=== "Body Parameters"
    

    The PyCript extension leverages the APIs offered by Burp Suite to examine get and body parameters. However, there may be situations where its performance is suboptimal when faced with encrypted or encoded parameters containing multiple `=` characters. If you encounter challenges in parsing such requests, it is recommended to use the complete body or custom request options and incorporate customized parsing into your script.

    ```http
    POST /crypto/myprofile?username=KtKB81Oamvuzo9entPFKZQ%3d%3d&password=KtKB81Oamvuzo9entPFKZQ%3d%3d HTTP/1.1
    Host: localhost:8000
    Accept-Encoding: gzip, deflate
    Accept: */*
    Accept-Language: en-US;q=0.9,en;q=0.8
    User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/103.0.5060.134 Safari/537.36
    Connection: close
    Cache-Control: max-age=0
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 24

    username=KtKB81Oamvuzo9entPFKZQ%3d%3d&password=KtKB81Oamvuzo9entPFKZQ%3d%3d
    ```

=== "JSON"
    PyCript has included JSON support since its initial release. The extension utilizes custom parsing methods to handle JSON objects, as Burp Suite itself does not natively support direct manipulation of JSON objects. Starting from version 0.2, PyCript extends its support to handle nested and complex JSON objects.

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


!!! info "Info"

    The current extension does not offer direct support for parsing multipart requests for parameters. However, you have the option to manually parse the request body using either the complete body or a custom request method within your script.

