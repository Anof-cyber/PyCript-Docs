## Flow of the Extension


PyCript utilizes system commands to run user-specified encryption and decryption scripts. In the new version, the extension passes the HTTP request body or parameters as byte arrays and headers as plain raw text, storing them in a temporary file. The file also separates headers and body/parameters using the marker ``\n--BODY_END--\n``. Instead of reading the script's output directly, PyCript executes the system command with the path to the temporary file provided to the user script. After execution, the extension reads the updated request or response data from the same temporary file, converting the byte array (body/parameters) back to strings and directly using the updated headers. These updated values are then replaced in the request or response within Burp Suite.

## Commands


The PyCript extension generates a temp file (from version 1.00) and stores the request body/parameter and header in raw text + byte array format. The extension passes the temp file location to the encryption-decryption script using -d command line argument. The JSON data will be based on the PyCript configuration.


* node script.js -d tempfile-path
* python script.py -d tempfile-path
* java -jar script.jar -d tempfile-path

The temp file will have encrypted/decrypted parameter or request body and request header in below format.

```text
    
[118, 75, 85, 86, 118, 111, 50, 57, 112, 100, 76, 50, 105, 67, 101, 109, 82, 97, 116, 115, 85, 80, 122, 122, 102, 89, 106, 57, 43, 110, 81, 75, 101, 99, 90, 43, 83, 87, 51, 70, 75, 65, 117, 66, 90, 107, 82, 101, 77, 75, 105, 80, 82, 74, 107, 112, 105, 53, 86, 66, 122, 89, 119]
--BODY_END--
POST /complete-body/api/2 HTTP/1.1
Host: 127.0.0.1:8000
Content-Length: 64
sec-ch-ua-platform: "Windows"
    
```


!!! info "Note"

    header is only available for request encryption decryption not for response.

!!! info "Note"

    Header can only be edited if Request Type is Complete Body.

!!! info "Note"
    
    In case of response encryption/decryption temp file will be no data after ``--BODY_END--``as no headers are there for response.


* Complete Body - The extension will take the request/response body convert it in byte array format, take raw plaintext header, save the file in above format.
- Parameter Value - PyCript is designed to iterate through the request/response parameter values and convert it in byte array format take raw plaintext header, save the file in above format. It will then update each value one by one.

- Parameter key and value - The PyCript extension adopts a similar approach to that of parameter value. It iterates through each parameter name and value and convert it in byte array format take raw plaintext header, save the file in above format, passing them one by one to the script for updating.




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
    

    The PyCript extension leverages the APIs offered by Burp Suite to examine get and body parameters. However, there may be situations where its performance is suboptimal when faced with encrypted or encoded parameters containing multiple `=` characters. If you encounter challenges in parsing such requests, it is recommended to use the complete body options and incorporate customized parsing into your script.

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

    The version 1.0 support multipart form data as well. Multi part form can have file upload data as well, It is recommended to add the file upload parameter name in the parameter exclusion list.

