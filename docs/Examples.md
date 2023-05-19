

## Config

The configuration tab contains all the necessary settings to enable encryption and decryption functionality. Within the configuration, you can select whether to apply encryption and decryption to requests, responses, or both. Additionally, you can specify the preferred programming language and load any necessary encryption/decryption files, among other options.

!!! Warning "Warning"

    This extension is designed to operate exclusively on URLs that have been included within the target scope of Burp Suite.

### Request Type

The Request Type feature enables you to choose the specific request or message type that requires encryption or decryption.









=== "Complete Body"

    The "Complete Body" option within the request type of your Burp Suite extension offers valuable functionality in various scenarios. It proves useful when the entire request body is encrypted, or when the body contains encrypted data along with key/iv within it. Additionally, this option is beneficial if you desire to implement custom parsing of the request body through a script of your choice.

    By selecting the "Complete Body" option, Pycript will take the entire request body and pass it to the encryption and decryption script provided by the user. This allows for seamless integration of your own encryption and decryption logic with Burp Suite.

    === "Example 1"

        ```http
        POST /api/getuser HTTP/1.1
        Host: 127.0.0.1:8000
        Accept-Encoding: gzip, deflate
        Accept: */*
        Accept-Language: en-US;q=0.9,en;q=0.8
        User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/103.0.5060.134 Safari/537.36
        Connection: close
        Cache-Control: max-age=0
        Content-Type: application/json
        Content-Length: 24

        AEIwJOtaQXdO9qzIJFIhEQ==
        ```

        <img src="/assets/Complete%20Body%20-%20Example%201.gif"/>

    === "Example 2"

        ```http
        POST /api/getuser2 HTTP/1.1
        Host: 127.0.0.1:8000
        Accept-Encoding: gzip, deflate
        Accept: */*
        Accept-Language: en-US;q=0.9,en;q=0.8
        User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/103.0.5060.134 Safari/537.36
        Connection: close
        Cache-Control: max-age=0
        Content-Type: application/json
        Content-Length: 88

        {"user_id": "W12wkCO+v4IOkEN3ENbRNQ==","key":"mysecretkey12345","iv":"n2r5u8x/A%D*G-Ka"}
        ```
        <img src="/assets/Complete%20Body%20-%20Example%202.gif"/>



=== "Parameter Value"

    When dealing with multiple parameters, where only the parameter values are encrypted, the parameter value can be retrieved from query parameters, JSON values, or body parameters. The PyCript module handles this process by passing one parameter value at a time to the user-provided script. The complete body or parameters are not passed to the script.

    === "Example 1"

        ```http
        POST /api/getuser2 HTTP/1.1
        Host: 127.0.0.1:8000
        Accept-Encoding: gzip, deflate
        Accept: */*
        Accept-Language: en-US;q=0.9,en;q=0.8
        User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/103.0.5060.134 Safari/537.36
        Connection: close
        Cache-Control: max-age=0
        Content-Type: application/json
        Content-Length: 39

        {"user_id": "kmcduz+eWvqNbPAq//UQGA=="}
        ```
        <img src="/assets/Paramter%20Value%20-%20Example%201.gif"/>

    === "Example 2"

        ```http
        GET /api/getuser5?user_id=W12wkCO+v4IOkEN3ENbRNQ== HTTP/1.1
        Host: 127.0.0.1:8000
        Accept-Encoding: gzip, deflate
        Accept: */*
        Accept-Language: en-US;q=0.9,en;q=0.8
        User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/103.0.5060.134 Safari/537.36
        Connection: close
        Cache-Control: max-age=0

        ```

        <img src="/assets/Paramter%20Value%20-%20Example%202.gif"/>


    === "Example 3"

        ```http
        POST /api/getuser5?user_id=kmcduz+eWvqNbPAq//UQGA== HTTP/1.1
        Host: 127.0.0.1:8000
        Accept-Encoding: gzip, deflate
        Accept: */*
        Accept-Language: en-US;q=0.9,en;q=0.8
        User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/103.0.5060.134 Safari/537.36
        Connection: close
        Cache-Control: max-age=0
        Content-Type: application/json
        Content-Length: 39

        {"user_id": "kmcduz+eWvqNbPAq//UQGA=="}

        ```

        <img src="/assets/Paramter%20Value%20-%20Example%203.gif"/>


=== "Parameter Key and Value"


    The Parameter Key and Value method proves to be valuable when dealing with encrypted parameter names and values in an application. By utilizing Pycript, the extension follows a consistent process of combining each parameter and its corresponding value. Subsequently, it proceeds to individually pass each resulting string to the encryption and decryption script.

    === "Example 1"

        ```http
        POST /api/getuser3 HTTP/1.1
        Host: 127.0.0.1:8000
        Accept-Encoding: gzip, deflate
        Accept: */*
        Accept-Language: en-US;q=0.9,en;q=0.8
        User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/103.0.5060.134 Safari/537.36
        Connection: close
        Cache-Control: max-age=0
        Content-Type: application/json
        Content-Length: 56

        {"elHLzVxVH4e3AayiZkfB9g==": "W12wkCO+v4IOkEN3ENbRNQ=="}

        ```

        <img src="/assets/Paramter%20Key%20and%20value%20-%20Example%201.gif"/>


=== "Custom Request"

    The custom request feature is valuable for executing specific actions using scripts. It proves particularly helpful in scenarios where the request body is encrypted, and the encryption key or initialization vector (IV) is stored in the header. In such cases, the "Complete Body" option cannot be used.

    When utilizing the Custom Request type, both the request body and header are passed to the script for encryption and decryption purposes. This functionality enables you to access and analyze the headers while performing the necessary encryption or decryption operations.

    === "Example 1"

        ```http
        POST /api/getuser HTTP/1.1
        Host: 127.0.0.1:8000
        Accept-Encoding: gzip, deflate
        Accept: */*
        Key: mysecretkey12345
        Iv: n2r5u8x/A%D*G-Ka
        Accept-Language: en-US;q=0.9,en;q=0.8
        User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/103.0.5060.134 Safari/537.36
        Connection: close
        Cache-Control: max-age=0
        Content-Type: application/json
        Content-Length: 24

        AEIwJOtaQXdO9qzIJFIhEQ==

        ```

        <img src="/assets/Custom%20Request%20-%20Example%201.gif"/>





=== "Custom Request Edit Header"

    
    The "Custom Request Edit Header" feature was introduced in version 0.2 of the Burp Suite extension. It provides the capability to modify the request header. This functionality is not limited to editing alone; you can also add or delete headers as needed. It proves to be particularly useful in applications where data modification is restricted through various proxy or validation mechanisms, such as adding a signature to request parameters or body and enforcing signature verification to prevent any value modification.

    === "Example 1"

        ```http
        POST /api/getuser4 HTTP/1.1
        Host: 127.0.0.1:8000
        Accept-Encoding: gzip, deflate
        Accept: */*
        Signature: 7d1b73091d0f089a3a790af24404f6b4
        Accept-Language: en-US;q=0.9,en;q=0.8
        User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/103.0.5060.134 Safari/537.36
        Connection: close
        Cache-Control: max-age=0
        Content-Type: application/json
        Content-Length: 39

        {"user_id": "W12wkCO+v4IOkEN3ENbRNQ=="}

        ```

        <img src="/assets/Edit%20Header%20-%20Example%201.gif"/>



### Response Type

The Response Type feature enables you to choose the specific response or message type that requires encryption or decryption.

!!! Warning "Warning"

    The encryption and decryption of responses were introduced in PyCript v0.2. Please note that this functionality is currently limited to JSON response content types.


!!! Info "Info"

    If your complete response body is encrypted and there are possibilities that body will be in double/single quotes. You need to handle the same in your script.

!!! Info "Info"

    The Response Type in your Burp Suite extension mirrors the functionality of the request type. In fact, you can utilize the same script for both request and response actions, with the exception of the Custom Request and Custom Request (Edit Header) options.


### Additional Setting

The Additional settings in your Burp Suite extension allow you to configure the following options:

`Language`

: You can select the language that will be used for encryption and decryption. It's important to note that the same language will be applied to both request and response, as well as for both encryption and decryption operations.

`Encryption Method`

: This option is available only for request-related settings. You can choose the encryption method to be applied to the GET parameters, request body, or both.

`Encryption/Decryption For`

: Here, you can select whether you want to perform encryption and decryption operations for the request, response, or both. Additionally, you will need to specify the request and response types to define the scope of these operations.



### Request Encryption Files

To encrypt and decrypt request data, you have the option to choose the encryption and decryption files. It's important to select the appropriate file based on the programming language specified in the additional settings.

### Response Encryption Files

For encrypting and decrypting response data, you can also select the encryption and decryption files. Make sure to choose the file that aligns with the programming language selected in the additional settings.



### Auto Encrypt

The auto-encryption feature enables you to encrypt requests within your Burp Suite extension. It allows you to specify the tool type for which the request should be encrypted. This functionality is particularly useful when performing automated scans, such as Burp Suite Active Scan or SQLMAP.

When dealing with an encrypted request that needs to be scanned using the Burp Suite scanner, a challenge arises because adding any payload in plain text format to an encrypted parameter would result in the server rejecting the request. This rejection occurs because the server fails to decrypt the request on its side.

To address this issue, you can provide the decrypted request to the scanner and activate the "Auto Encrypt" option. Additionally, you can select the appropriate tool type for the scanner.

By doing so, the scanner will receive the decrypted request and add any necessary payload. Subsequently, the extension will encrypt the request (based on the selected Request Type) before the scanner sends it to the server.

Similarly, for SQLMAP, you can supply the decrypted request and configure SQLMAP to send all requests to the Burp proxy. Make sure to enable the "Auto Encrypt" option in this case as well.

<iframe width="560" height="315" src="https://www.youtube.com/embed/DWzI7JbPEkI" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>



## Menu

PyCript offers several additional options accessible through the right-click menu. To access these options, simply right-click on any request within Burp Suite, whether it's in Repeater, Target, Proxy, or elsewhere. In the menu that appears, you will find an "Extensions" option, and within that submenu, you will see "PyCript."

Upon selecting the PyCript submenu, you will be presented with three distinct options:

* Decrypt Request: This option allows you to decrypt the request, enabling you to analyze its contents in its original, unencrypted form. The decrypted request will be stored in the PyCript extension for easy access. You can find it in the "PyCript" tab, under the "Decrypted Request" sub-tab.

* Decrypt String: With this option, you can select any data within the request and response. PyCript will then show you the decrypted version of the selected string, providing a way to analyze the decrypted data.

* Encrypt String: This option allows you to encrypt selected data within the request and response. PyCript will show you the encrypted version of the selected string.


!!! Info "Info"

    When selecting a string from the request in your Burp Suite extension, the encryption/decryption script used for the request will be applied. If you have not added an encryption/decryption script specifically for the request, the script associated with the response will be utilized instead. Similarly, if you choose a string from the response, the corresponding encryption/decryption script will be used accordingly.



<iframe width="560" height="315" src="https://www.youtube.com/embed/Hkfqb_npfeg" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

