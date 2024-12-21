

## Config

The configuration tab contains all the necessary settings to enable encryption and decryption functionality. Within the configuration, you can select whether to apply encryption and decryption to requests, responses, or both. Additionally, you can specify the preferred programming language and load any necessary encryption/decryption files, among other options.

!!! Warning "Warning"

    This extension is designed to operate exclusively on URLs that have been included within the target scope of Burp Suite.


!!! Warning "Warning"

    PyCript version 1.0 change the use of encryption decryption language as well how it pass data to the encryption decryption script over command line argument.
    Older script will not work with new PyCript. Check the "Select Language" and "Writing Custom Scripts" page.


### Request Type

The Request Type feature enables you to choose the specific request or message type that requires encryption or decryption.









=== "Complete Body"

    The "Complete Body" provides the capability to modify the request header an body, means complete request. This functionality is not limited to editing alone; you can also add or delete headers as needed. It proves to be particularly useful in applications where data modification is restricted through various proxy or validation mechanisms, such as adding a signature to request parameters or body and enforcing signature verification to prevent any value modification, Or you want to parse the request with your own script.

    By selecting the "Complete Body" option, Pycript will take the entire request body in Byte Array Format and Headers in plain text format and pass it to the encryption and decryption script provided by the user. This allows for seamless integration of your own encryption and decryption logic with Burp Suite.

  


=== "Parameter Value"

    When dealing with multiple parameters, where only the parameter values are encrypted, the parameter value can be retrieved from query parameters, JSON values, or body parameters. The PyCript module handles this process by passing one parameter value at a time to the user-provided script. The complete body or parameters are not passed to the script. Instead PyCript loop through each parameter, convert its value to Byte Array and passes with to User Script along with Plain text Header.

    Parameter value allow your script to read header as well. Your script cannot provide edited headers. You can only update parameter values not request headers. 




### Response Type

The Response Type feature enables you to choose the specific response or message type that requires encryption or decryption.

!!! Warning "Warning"

    The encryption and decryption of responses were introduced in PyCript v0.2. Please note that this functionality is currently limited to JSON response content types.


!!! Info "Info"

    If your complete response body is encrypted and there are possibilities that body will be in double/single quotes. You need to handle the same in your script.

!!! Info "Info"

    The Response Type in your Burp Suite extension mirrors the functionality of the request type. In fact, you can utilize the same script for both request and response actions.


!!! Info "Info"

    The Response Type does not allow you to edit headers in the response. 

### Additional Setting

The Additional settings in your Burp Suite extension allow you to configure the following options:

`Language`

: You can select the language that will be used for encryption and decryption. It's important to note that the same language will be applied to both request and response, as well as for both encryption and decryption operations.

`Clear Language Selection`

: You can use the button to remove the selected language. You don't need to define the language when encryption decryption is in binary format like compiled code from C++ or C etc.


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

<img src="https://raw.githubusercontent.com/Anof-cyber/PyCript-Docs/refs/heads/gh-pages/0.4/assets/string.png"/>


!!! Info "Info"

    When selecting a string from the request in your Burp Suite extension, the encryption/decryption script used for the request will be applied. If you have not added an encryption/decryption script specifically for the request, the script associated with the response will be utilized instead. Similarly, if you choose a string from the response, the corresponding encryption/decryption script will be used accordingly.



<iframe width="560" height="315" src="https://www.youtube.com/embed/Hkfqb_npfeg" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

