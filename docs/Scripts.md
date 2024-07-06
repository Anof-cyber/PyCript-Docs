### Working of the Extension

The extension utilizes user provided encrpyption decrpytion files for executing encryption and decryption operations by running system commands.




!!! Warning "Warning"

    PyCript before version 0.4 used to pass encrpyted paramter/body etc over -d argument in bass64 format and -h for header in base64 format.
    from version 0.4 there will be not -h argument, also -d will not have parameter/body.[Check issue for more details](https://github.com/Anof-cyber/PyCript/issues/5)

PyCript expect user to provide encryption decryption code file, the code langauge could be anything Like Python, Java, JavaScript, Go, Bash script, Powershell script, C/C++ or Any. Check [Select Language](/latest/Select-Language/) page for more details.


!!! Info "Info"

    From PyCript version 0.4 you can use any language for encryption decryption as long as it uses the format of PyCript to read data and pass data to and from PyCript.


!!! Info "Info"

    Check the Dummy Code repository [PyCript Template](https://github.com/Anof-cyber/PyCript-Template/) Has example codes in  Python, Go, Bash, Powershell, JavaScript and Java


PyCript run system command to execute the script or binary provided for encryption and decryption. User is suppose to provide the language path. Check [Select Language](/latest/Select-Language/) page for more details.

Next user is suppose to provide the encrpytion decrpytion file path. Later based on the setting on PyCript config tab, the extension will provided the encrpyted decrpyted body/paramter/header to the script.


The extension will take the encrypted/plaintext parameter/body create a temp file in the OS and add the encrypted/plaintext parameter/body in the temp file JSON format. The data will be base64 encoded by extension to avoid special characters.

```json
{"data":"cGxhaW50ZXh0"}
```


```terminfo
/user/path/to/language/like/node user/file/path/encryption.js -d temp-file-path
```


#### Command Line Argument for Body/Parameters

The extension utilizes the -d command line argument to pass the full path of the temp file storing the body or parameters to the encryption/decryption code. If you are writing a script, ensure that your script supports command line arguments with the -d flag.


#### Base64 Encoding in temp file JSON

The value provided with the -d argument will be the path of the temp file. The temp file will have a JSON format where ``data`` key in the JSON will store the parameter/body in base64 format. Even if your original request is already in base64, the extension will perform base64 encoding again and provide the encoded value to your code. To obtain the original value of your request body or parameter, you need to decode the base64 value in your code.


#### Base64 Encoding for String Consistency

The extension performs base64 encoding to ensure consistency and avoid any string-related issues, such as spaces or special characters.


#### Performing Actions and Adding Logic

Your code can perform any action or add any logic to the provided value. The extension will read the output of the system command, so it is essential that your code prints the updated value accordingly.




#### Consistency Across Request Types and Response Types

This approach and code structure apply to all request types and response types in the extension, except for the "Custom Request" and "Custom Request (Edit Header)" types.

#### Encryption and Decryption Code

The same logic described above is applicable to encryption and decryption code as well. Ensure that your code follows the specified format and guidelines for encryption and decryption operations.

#### Output Format

The output or print statement provided by your code will be read by the extension, and it should be in the original format, not in base64 except for "Custom Request (Edit Header)". Make sure your code returns the result in the expected format.


!!! Warning "Warning"

    There should not be more than one print statement or output in your code. The extension relies on reading the system command output, so having multiple print statements or outputs can cause unexpected behavior.


=== "Python"

    ``` python
    import argparse
    from base64 import b64decode,b64encode
    import json

    # Create an argument parser
    parser = argparse.ArgumentParser(description='Process data argument')
    parser.add_argument('-d', '--data', help='File path with plaintext data + base64 in JSON format')

    # Parse the arguments
    args = parser.parse_args()

    ## Read the temp file from -d path
    with open(args.data, 'r') as file:
        content = json.load(file)

    # get the data key from the JSON    
    cihper_or_plaintext_in_base64 = content.get("data")

    # Base64 decode to the original data 
    cihper_or_plaintext = b64decode(cihper_or_plaintext_in_base64).decode('utf-8')


    ## Your encryption logic

    print(output)
    ```

=== "JavaScript"

    ``` javascript
    const fs = require('fs');
    const path = require('path');
    var CryptoJS = require("crypto-js");
    const { program } = require('commander');
    const { Buffer } = require('buffer');

    program
    .option('-d, --data <file_path>', 'Path to JSON file containing base64 encoded + encrypted data');
    
    program.parse(process.argv);
    const options = program.opts();
    
    // Read the -d value
    const filePath = options.data;

    // reolve the full of -d provided path to avoid error in reading file.
    const absoluteFilePath = path.resolve(filePath);
    
    // read the file.
    var data = fs.readFileSync(absoluteFilePath).toString();

    //parse the json from the file
    const jsonData = JSON.parse(data);

    // get the JSON data key value
    const base64Data = jsonData.data; 

    // base64 decode the value to get original data.
    const plaintext = Buffer.from(base64Data, 'base64').toString('utf8');

    ///Your encryption logic here

    // print the output
    console.log(output)
    ```


=== "Java"

    ``` java
    import org.json.JSONObject;   //https://github.com/stleary/JSON-java
    import java.io.FileReader;
    import java.io.IOException;
    import java.nio.charset.StandardCharsets;
    import java.text.ParseException;
    import java.util.Base64;
    
    public class AESCBC128Decrypt {

    public static void main(String[] args) {

        // Validate if -d is there or not
        if (args.length != 2 || !args[0].equals("-d")) {
            System.out.println("Usage: java AESCBCDecrypt -d cipher-data-file-path");
            return;
        }

        // get -d value for temp file path
        String ciphertextfilepath = args[1];
        String base64Data = null;

        // read the file content
        try (FileReader reader = new FileReader(ciphertextfilepath)) {
            // parse the file content as JSON
            JSONObject jsonObject = new JSONObject(reader);

            // parse JSON and get Data key from JSON Object
            base64Data = (String) jsonObject.get("data");

        } catch (IOException  e) {
            System.err.println("Error reading JSON file: " + e.getMessage());
        };
        // base64 decode the value to get original data.
        String cipher_plain_text = new String(Base64.getDecoder().decode(base64Data), StandardCharsets.UTF_8);

        ///Your encryption logic here

        // print the output
        System.out.println(output);

    }}
    
    ```



### Custom Request Type

When implementing the Custom Request feature in your Burp Suite extension, there are a few changes to consider in your code. The points mentioned earlier in the documentation will still apply but with the addition of some further details. Custom Request will have the headers as an array/list in base64 format in the JSON temp file as ``header`` key that will allow you to read the request header as well. The temp file will have content like below.

```json
{
  "data": "cGxhaW50ZXh0",
  "header": "W1BPU1QgL2NyeXB0by91c2VyLyBIVFRQLzEuMSwgSG9zdDogbG9jYWxob3N0OjgwMDAsIEtleTogMTIzNCwgSXY6IDEyMzRd"
}

```
The ``data`` will have same encrpyted/plain text body/parameter and header will have request header in list format, Both will be base64 encoded by PyCript to get original data you have to decode both. 

The header will be in list format so if you base64 decode header it will be in a list/array format as below:


```javascript
[POST /crypto/myprofile/ HTTP/1.1, Host: localhost:8000, Accept-Encoding: gzip, deflate, Accept: */*, Accept-Language: en-US;q=0.9,en;q=0.8, User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/103.0.5060.134 Safari/537.36, Connection: close, Key: 1234, Iv: 1234, Cache-Control: max-age=0, Content-Type: application/x-www-form-urlencoded, Content-Length: 24]

```
The encryption/decryption code will be the same as above with you having the headers as key in the JSON, You can use the above script as well as an extension to give you a header but only expect an updated parameter/body, not the headers so you can use the above script as well as it's up to you to read the header as well if needed.

=== "Python"

    ``` python
    import argparse
    from base64 import b64decode,b64encode
    import json

    # Create an argument parser
    parser = argparse.ArgumentParser(description='Process data argument')
    parser.add_argument('-d', '--data', help='File path with plaintext data + base64 in JSON format')

    # Parse the arguments
    args = parser.parse_args()

    ## Read the temp file from -d path
    with open(args.data, 'r') as file:
        content = json.load(file)

    # get the data and header key from the JSON    
    cihper_or_plaintext_in_base64 = content.get("data")
    header_in_base64 = content.get("header")

    # Base64 decode to the original data and header
    cihper_or_plaintext = b64decode(cihper_or_plaintext_in_base64).decode('utf-8')
    header_plaintext_list = b64decode(header_in_base64).decode('utf-8')


    ## Your encryption logic

    print(output)  # Only print output of cihper_or_plaintext not header as header is read only
    ```

=== "JavaScript"

    ``` javascript
    const fs = require('fs');
    const path = require('path');
    var CryptoJS = require("crypto-js");
    const { program } = require('commander');
    const { Buffer } = require('buffer');

    program
    .option('-d, --data <file_path>', 'Path to JSON file containing base64 encoded + encrypted data');
    
    program.parse(process.argv);
    const options = program.opts();
    
    // Read the -d value
    const filePath = options.data;

    // reolve the full of -d provided path to avoid error in reading file.
    const absoluteFilePath = path.resolve(filePath);
    
    // read the file.
    var data = fs.readFileSync(absoluteFilePath).toString();

    //parse the json from the file
    const jsonData = JSON.parse(data);

    // get the JSON data and header key value
    const base64Data = jsonData.data; 
    const base64header = jsonData.header; 

    // base64 decode the value to get original data and header.
    const cihper_or_plaintext = Buffer.from(base64Data, 'base64').toString('utf8');
    const plaintext_list_header = Buffer.from(base64header, 'base64').toString('utf8');

    ///Your encryption logic here

    // print the output
    console.log(output) // Only print output of cihper_or_plaintext not header as header is read only
    ```


=== "Java"

    ``` java
    import org.json.JSONObject;   //https://github.com/stleary/JSON-java
    import java.io.FileReader;
    import java.io.IOException;
    import java.nio.charset.StandardCharsets;
    import java.text.ParseException;
    import java.util.Base64;
    
    public class AESCBC128Decrypt {

    public static void main(String[] args) {

        // Validate if -d is there or not
        if (args.length != 2 || !args[0].equals("-d")) {
            System.out.println("Usage: java AESCBCDecrypt -d cipher-data-file-path");
            return;
        }

        // get -d value for temp file path
        String ciphertextfilepath = args[1];
        String base64Data = null;

        // read the file content
        try (FileReader reader = new FileReader(ciphertextfilepath)) {
            // parse the file content as JSON
            JSONObject jsonObject = new JSONObject(reader);

            // parse JSON and get Data and header key from JSON Object
            base64Data = (String) jsonObject.get("data");
            base64headerStr = (String) jsonObject.get("header");

        } catch (IOException  e) {
            System.err.println("Error reading JSON file: " + e.getMessage());
        };
        // base64 decode the value to get original data.
        String cipher_plain_text = new String(Base64.getDecoder().decode(base64Data), StandardCharsets.UTF_8);
        String header_in_plain_text = new String(Base64.getDecoder().decode(base64headerStr), StandardCharsets.UTF_8); // you can use it as string or convert to list

        ///Your encryption logic here

        // print the output
        System.out.println(output); // Only print output of cipher_plain_text not header as header is read only

    }}
    
    ```

!!! Warning "Warning"

    
    Given the limitation of only being able to read the header, it is important to ensure that you only print the output of the encryption/decryption process on Data key value and not the header. 






### Custom Request (Edit Headers)

Custom Request and Custom Request (Edit Header) have several similarities with a few key differences:


- The header value in Custom Request is in a list/array format, whereas in Custom Request (Edit Header), the header is in raw format, just as you would see it in Burp Suite.

- Since Custom Request (Edit Header) allows for header editing, it's necessary to print the header as well, but in base64 format.

- The output of the encrypted/decrypted string/body will also be in the base64 format.

- It is important to print the base64 header first, followed by the base64 body/parameter.

- Since the header is in raw format, you can split it using new line characters (\n) to edit or add new headers.

- When printing the updated header in base64, it should retain the same raw format. If you split the header with new line characters to make changes, you will need to join it again to restore it to the original raw format.



```json
{"data":"cGxhaW50ZXh0","header":"UE9TVCAvYXBpL2dldHVzZXI0IEhUVFAvMS4xCkhvc3Q6IDEyNy4wLjAuMTo4MDAwCkFjY2VwdC1FbmNvZGluZzogZ3ppcCwgZGVmbGF0ZQpBY2NlcHQ6ICovKgpTaWduYXR1cmU6IDA4NjFjMDBiODFlNWY2OTZkNGE5MjM3MGE4OTYxYzEyCkFjY2VwdC1MYW5ndWFnZTogZW4tVVM7cT0wLjksZW47cT0wLjgKVXNlci1BZ2VudDogTW96aWxsYS81LjAgKFdpbmRvd3MgTlQgMTAuMDsgV2luNjQ7IHg2NCkgQXBwbGVXZWJLaXQvNTM3LjM2IChLSFRNTCwgbGlrZSBHZWNrbykgQ2hyb21lLzEwMy4wLjUwNjAuMTM0IFNhZmFyaS81MzcuMzYKQ29ubmVjdGlvbjogY2xvc2UKQ2FjaGUtQ29udHJvbDogbWF4LWFnZT0wCkNvbnRlbnQtVHlwZTogYXBwbGljYXRpb24vanNvbgpDb250ZW50LUxlbmd0aDogMzg="}
```

If you base64 decode the header you will see like this same as raw http request.

```http
POST /api/getuser4 HTTP/1.1
Host: 127.0.0.1:8000
Accept-Encoding: gzip, deflate
Accept: */*
Signature: 0861c00b81e5f696d4a92370a8961c12
Accept-Language: en-US;q=0.9,en;q=0.8
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/103.0.5060.134 Safari/537.36
Connection: close
Cache-Control: max-age=0
Content-Type: application/json
Content-Length: 38
```



=== "Python"

    ``` python
    import argparse
    from base64 import b64decode,b64encode
    import json

    # Create an argument parser
    parser = argparse.ArgumentParser(description='Process data argument')
    parser.add_argument('-d', '--data', help='File path with plaintext data + base64 in JSON format')

    # Parse the arguments
    args = parser.parse_args()

    ## Read the temp file from -d path
    with open(args.data, 'r') as file:
        content = json.load(file)

    # get the data and header key from the JSON    
    cihper_or_plaintext_in_base64 = content.get("data")
    header_in_base64 = content.get("header")

    # Base64 decode to the original data and header
    cihper_or_plaintext = b64decode(cihper_or_plaintext_in_base64).decode('utf-8')
    header_plaintext_list = b64decode(header_in_base64).decode('utf-8')

    ## Your encryption logic

    ## Update the header

    ## print the base64 encoded header first then body
    print(b64encode(updated_header))
    print(b64encode(updatedbody))
    
    ```

=== "JavaScript"

    ``` javascript
    const fs = require('fs');
    const path = require('path');
    var CryptoJS = require("crypto-js");
    const { program } = require('commander');
    const { Buffer } = require('buffer');

    program
    .option('-d, --data <file_path>', 'Path to JSON file containing base64 encoded + encrypted data');

    program.parse(process.argv);
    const options = program.opts();

    // Read the -d value
    const filePath = options.data;

    // reolve the full of -d provided path to avoid error in reading file.
    const absoluteFilePath = path.resolve(filePath);

    // read the file.
    var data = fs.readFileSync(absoluteFilePath).toString();

    //parse the json from the file
    const jsonData = JSON.parse(data);

    // get the JSON data and header key value
    const base64Data = jsonData.data; 
    const base64header = jsonData.header; 

    // base64 decode the value to get original data and header.
    const cihper_or_plaintext = Buffer.from(base64Data, 'base64').toString('utf8');
    const plaintext_list_header = Buffer.from(base64header, 'base64').toString('utf8');


    'Your encryption and decryption logic should be here'
    'Updated the header'


    // print the updated base64 header and followed by updated base64 body
    console.log(Buffer.from(updatedheader).toString('base64'));
    console.log(Buffer.from(updatedbody).toString('base64'));
    ```


=== "Java"

    ```java
    import org.json.JSONObject;   //https://github.com/stleary/JSON-java
    import java.io.FileReader;
    import java.io.IOException;
    import java.nio.charset.StandardCharsets;
    import java.text.ParseException;
    import java.util.Base64;

    public class AESCBC128Decrypt {

    public static void main(String[] args) {

        // Validate if -d is there or not
        if (args.length != 2 || !args[0].equals("-d")) {
            System.out.println("Usage: java AESCBCDecrypt -d cipher-data-file-path");
            return;
        }

        // get -d value for temp file path
        String ciphertextfilepath = args[1];
        String base64Data = null;

        // read the file content
        try (FileReader reader = new FileReader(ciphertextfilepath)) {
            // parse the file content as JSON
            JSONObject jsonObject = new JSONObject(reader);

            // parse JSON and get Data and header key from JSON Object
            base64Data = (String) jsonObject.get("data");
            base64headerStr = (String) jsonObject.get("header");

        } catch (IOException  e) {
            System.err.println("Error reading JSON file: " + e.getMessage());
        };
        // base64 decode the value to get original data.
        String cipher_plain_text = new String(Base64.getDecoder().decode(base64Data), StandardCharsets.UTF_8);
        String header_in_plain_text = new String(Base64.getDecoder().decode(base64headerStr), StandardCharsets.UTF_8); // you can use it as string or convert to list

        ///Your encryption logic here

        // print the output
        String updatedHeaderBase64 = Base64.getEncoder().encodeToString(updatedHeader.getBytes(StandardCharsets.UTF_8));
        String updatedBase64Body = Base64.getEncoder().encodeToString(updatedBody.getBytes(StandardCharsets.UTF_8));

        //print the header first then body
        System.out.println(updatedHeaderBase64);
        System.out.println(updatedBase64Body);

    }}

    ```





## Debug

If you encounter difficulties decrypting or encrypting the request within the extension, it is important to ensure that your code is functioning correctly. To encrypt or decrypt the request body or string, you can follow these steps: 

1. Base64 encode the value you wish to encrypt or decrypt.
2. Store the value in a file as JSON content like ``{"data":"your-base64-encoded-value-of-plaintext/encrypted-data"}``
3. Execute the same command in your terminal or CMD, like youscript -d path-tofile

By performing these steps, you can verify the functionality of the encryption or decryption process outside of the extension environment.

!!! Info "Info"

    The version 0.3 comes with logger support for debug. You can click on the Log tab and enable logging to see the background command execution for debugging.


!!! Info "Info"

    The temp file generated by the extension will be deleted as soon as command is execution is completed so from Log tab if you copy the same command and execute in your terminal if will give as error the path of the -d file will be deleted.



## Demo Code
If you are looking for demo encryption decryption code for common type of encryption or logic you can get it from the [PyCript Template](https://github.com/Anof-cyber/PyCript-Template)


