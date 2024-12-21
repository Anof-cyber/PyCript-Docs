### Working of the Extension

The extension utilizes user provided encryption decryption files for executing encryption and decryption operations by running system commands.


## Demo Code
If you are looking for demo encryption decryption code for common type of encryption or logic you can get it from the [PyCript Template](https://github.com/Anof-cyber/PyCript-Template)


!!! Warning "Warning"

    PyCript version 1.0 always pass headers along with body or parameter. Where Headers are Editable with Complete Body Type. Older PyCript Custom Script will not work.


!!! Warning "Warning"

    PyCript version 1.0 use Byte array as parameter/body  value like [80, 53, 50] instead of Base64.

PyCript expect user to provide encryption decryption code file, the code languages could be anything Like Python, Java, JavaScript, Go, Bash script, Powershell script, C/C++ or Any. Check [Select Language](/latest/Select-Language/) page for more details.


!!! Info "Info"

    From PyCript version 0.4 you can use any language for encryption decryption as long as it uses the format of PyCript to read data and pass data to and from PyCript.


!!! Info "Info"

    Check the Dummy Code repository [PyCript Template](https://github.com/Anof-cyber/PyCript-Template/) Has example codes in  JavaScript


PyCript run system command to execute the script or binary provided for encryption and decryption. User is suppose to provide the language path. Check [Select Language](/latest/Select-Language/) page for more details.

Next user is suppose to provide the encryption decryption file path. Later based on the setting on PyCript config tab, the extension will provided the encrypted decrypted body/parameter/header to the script.


### PyCript Flow for Request Decryption

- Take Request body [If Request Type is Complete Body], Convert it into the Byte Array format like [80, 44, 56].
    - If Request Type is Parameter Value or Parameter Key Value, PyCript will loop each parameter from Script. Convert to Byte Array Format.
- PyCript create a Temp file, Save the Byte Array body/parameter/Parameter Key along with Plain with Raw Header and will use ``--BODY_END--`` to separate them.

Example Temp Files with Original Request in Burp Suite:

Example Request in Burp Suite

    ```http
    POST / HTTP/2
    Host: google.com
    Accept-Encoding: gzip, deflate, br
    Accept: */*
    Accept-Language: en-US;q=0.9,en;q=0.8
    User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/131.0.6778.86 Safari/537.36
    Cache-Control: max-age=0
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 16


    EnCRYPTED-BODY
    ```

Example Temp File Create By PyCript

    [69, 110, 67, 82, 89, 80, 84, 69, 68, 45, 66, 79, 68, 89]
    --BODY_END--
    POST / HTTP/2
    Host: google.com
    Accept-Encoding: gzip, deflate, br
    Accept: */*
    Accept-Language: en-US;q=0.9,en;q=0.8
    User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/131.0.6778.86 Safari/537.36
    Cache-Control: max-age=0
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 16

In case of Parameter Key or Parameter Key value is used, for each parameter and key a temp file will be created by PyCript and each will have Byte array instead of Body Byte Array. 


You can convert it back to string before decryption and encryption like below Python and JavaScript example code

=== "Python"

    ```python
    ascii_values = [69, 110, 67, 82, 89, 80, 84, 69, 68, 45, 66, 79, 68, 89]
    result = ''.join(chr(value) for value in ascii_values)
    print(result)
    ```

=== "NodeJS"

    ```javascript
    const asciiValues = [69, 110, 67, 82, 89, 80, 84, 69, 68, 45, 66, 79, 68, 89];
    const result = String.fromCharCode(...asciiValues);
    console.log(result);
    ```
- Once the temp file is created PyCript  will execute the user provided encryption/decryption script and will provide the path of the temp file to user script like:


```terminfo
/user/path/to/language/like/node user/file/path/encryption.js -d temp-file-path
```


#### Command Line Argument for Body/Parameters

The extension utilizes the -d command line argument to pass the full path of the temp file storing the Byte body or parameters along with Plain text headers to the encryption/decryption code. If you are writing a script, ensure that your script supports command line arguments with the -d flag.


The value provided with the -d argument will be the path of the temp file. The temp file will split the parameter/body and Header with `--BODY_END--`. Your script need to split the data with divider `--BODY_END--` to get parameter/body separately and headers separately. Once one your script can convert the Byte to String for body/parameter value.

#### Byte array for String Consistency

The extension uses Byte array from version 1.0 to support binary data, Non ASCII data in request body.

#### Performing Actions and Adding Logic

Your code can perform any action or add any logic to the provided value. Once your script is done with the changes on request body/parameter and header. your script should again change the request body/parameter in same byte array format.

Save the updated request in the same temp file provided on -d argument by the PyCript. Updated request should be in same format like:

```http
[69, 110, 67, 82, 89, 80, 84, 69, 68, 45, 66, 79, 68, 89]
--BODY_END--
POST / HTTP/2
Host: google.com
Accept-Encoding: gzip, deflate, br
Accept: */*
Accept-Language: en-US;q=0.9,en;q=0.8
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/131.0.6778.86 Safari/537.36
Cache-Control: max-age=0
Content-Type: application/x-www-form-urlencoded
Content-Length: 16
```

- PyCript validate if command to execute encryption/decryption had any error, If not will try to read the same temp file to get updated request. Convert the Byte array for Parameter/Body to String and Updated the Request headers and Body/parameter in request.

#### Encryption and Decryption Code

The same logic described above is applicable to encryption and decryption code as well. Ensure that your code follows the specified format and guidelines for encryption and decryption operations.

#### Notes

- All Request Type will have headers in the temp file. Your script can read header header in all Request Type
- Only Request Type Complete Body allows your script to provide ability to Provide updated header so PyCript can update header as well. 
- Even if you select Request Type as Parameter Value or Parameter Key Value, where your script can only read header cannot provide updated headers. Your script must add `--BODY_END--` in the temp file.
- Response encryption decryption does get header. Your script will only have Response Body or Parameters not header but your script must add `--BODY_END--` in the temp file.
- PyCript will always add `--BODY_END--` at the end of Parameter/Body even if header is not there. It always recommended to split the request to remove the `--BODY_END--` even if header is not here like for Response.




=== "Python"

    ``` python
    import json
    from pathlib import Path
    import argparse

    # Function to handle AES decryption
    def decrypt_aes(ciphertext):
        # your logic to decrypt the ciphertext
        decrypted = ciphertext
        return decrypted

    # Function to read and parse the header
    def read_parse_header(headers_raw):
        # Logic to edit header if needed
        return headers_raw

    # Parse command-line arguments
    parser = argparse.ArgumentParser(description='Process encrypted/decrypted data.')
    parser.add_argument('-d', '--data', required=True, help='Path to temp file containing request/response data')
    args = parser.parse_args()

    # Resolve file path and read the file
    file_path = Path(args.data).resolve()
    data = file_path.read_text(encoding='utf-8')

    # Split the data into body and header
    body_end_marker = '\n--BODY_END--\n'   # Marker to separate body and header, remove the new line character as well
    byte_array_str, headers_raw = data.split(body_end_marker)

    # Convert byte array string to bytes
    byte_array = json.loads(byte_array_str.strip())
    ciphertext = bytes(byte_array).decode('utf-8')   # convert byte array to string to get original ciphertext/plaintext same as burp Suite. or you can do ''.join(chr(value) for value in byte_array_str)


    # Decrypt the ciphertext
    original_text = decrypt_aes(ciphertext)
    # Parse and update the header
    updated_header = read_parse_header(headers_raw)

    updated_output_byte = [ord(char) for char in original_text] # Convert the updated string to byte array, only the data, parameter or body, not the header
    output = f"{json.dumps(updated_output_byte)}{body_end_marker}{updated_header}"  # create the same format as byte array string \n--BODY_END--\n Plaintext header
    file_path.write_text(output, encoding='utf-8') # Write back to the same file , you need to overwrite to replace old request with new in the temp file.
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
    
    const filePath = options.data;
    const absoluteFilePath = path.resolve(filePath); // Get the absolute path of the file FROM -d option
    var data = fs.readFileSync(absoluteFilePath, 'utf8')  // Read the file data
    const bodyEndMarker = '\n--BODY_END--\n';  // Marker to separate body and headers
    const [byteArrayStr, headersRaw] = data.split(bodyEndMarker); // Split the data into byte array and headers

    const byteArray = JSON.parse(byteArrayStr.trim())  
    const buffer = Buffer.from(byteArray); // Convert byte array to Buffer
    const ciphertext = buffer.toString('utf8') // convert it string 


    // call the functions to handle decrpytion, headers 
    const originalText = Decryption(ciphertext);
    const updatedHeader = Read_parse_Header(headersRaw);


    const updated_output_byte = Array.from(originalText).map(char => char.charCodeAt(0)); // convert the updated string to byte array again
    var output = updated_output_byte +"\n--BODY_END--\n"+updatedHeader  // create the final output string in the same format as input file
    fs.writeFileSync(absoluteFilePath,output)  // write to same temp file in same formamt body\n--BODY_END--\nheader

    function Decryption(ciphertext) {
        // your logic to decrypt/encrypt the ciphertext and return updated text
    return ciphertext;
    }

    function Read_parse_Header(headersRaw) {
    // logic to read/edit header
    return headersRaw;
    }
    ```


### Parsing Headers
The headers are in the raw format, For Request Type Complete body, you can read, edit the headers and save the updated header in the temp file. In case of other request type selected, you can read the headers to get any value you want you can edit as well in your script.

PyCript will ignore the headers from the temp file even if your script has updated if the request type is not completed body. But in any case if you want to read header to get values or read and update headers. you have to parse the headers

To parse the headers you can split the headers like below 



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
    #to just read the headers 
    headersplit = header.splitlines()
    Host = next((line for line in headersplit if line.startswith('Host:')), None)
    User_Agent = next((line for line in headersplit if line.startswith('User-Agent:')), None)

    #to read or edit the header value so you can save updated output file
    headersplit = header.splitlines()
    for i, line in enumerate(headersplit):
        if line.startswith('Host:'):
            headersplit[i] = 'Host: updated.host.com'
            break
    # rejoin the header, this is important
    updatedheader = '\r\n'.join(headersplit)  # we can now save updatedheader in the temp file.
    
    
    ```

=== "JavaScript"

    ``` javascript

    // to just read the headers 
    const headersplit = header.split(/\r?\n/);
    const Host = headersplit.find(line => line.startsWith('Host:'));   
    const User_Agent = headersplit.find(line => line.startsWith('User-Agent:'));

    // to read or edit the header value so you can save updated output file

    var headersplit = header.split(/\r?\n/)
    for (var i = 0; i < headersplit.length; i++) {
    if (headersplit[i].startsWith('Host:')) {
        headersplit[i] = 'Host: updated.host.com' ;
        break;
        }
    }
    // rejoin the header, this is important
    var updatedheader = headersplit.join("\r\n")  // we can now save updatedheader in the temp file.
    ```





## Debug

If you encounter difficulties decrypting or encrypting the request within the extension, it is important to ensure that your code is functioning correctly. To encrypt or decrypt the request body or string, you can follow these steps: 

1. The log tab within the PyCript will always print the value of temp file created by the PyCript.
2. If your script is not working or having some errors, you can manually save the temp file data from the log tab and run your script locally on that data.
3. Temp file created by the PyCript is deleted as soon as your script is execution is completed for that PyCript write the temp file data to logs in case you need it for debugging.
4. If your script does not have any errors its working but you are not getting expected output in the Burp Suite request. It could be error from PyCript to read the updated temp file or could error from your script.
5. To debug the cause you can add as many print in your script. you can even print the final output from your script along with saving it into the the file and can see the print output in the PyCript log to verify if your script is giving correct output or not.

!!! Info "Info"

    The version 0.3 comes with logger support for debug. You can click on the Log tab and enable logging to see the background command execution for debugging.


!!! Info "Info"

    The temp file generated by the extension will be deleted as soon as command is execution is completed so from Log tab if you copy the same command and execute in your terminal if will give as error the path of the -d file will be deleted.





