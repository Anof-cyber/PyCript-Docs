### Working of the Extension

The extension utilizes user-provided encryption and decryption files for executing encryption and decryption operations by running system commands.

## Demo Code
If you are looking for demo encryption decryption code for common types of encryption or logic, you can get it from the [PyCript Template](https://github.com/Anof-cyber/PyCript-Template)

!!! Warning "Warning"

    PyCript version 2.0 is a complete rewrite using Java and the Montoya API. The extension now passes raw data (text or binary) instead of byte arrays. Scripts from v1.x will not work with v2.0.

!!! Warning "Warning"

    PyCript version 2.0 passes data in raw format - either as plain text or binary data depending on the encryption type. Your scripts must handle both text and binary data appropriately.

PyCript expects users to provide encryption and decryption code files. The code language can be anything like Python, Java, JavaScript, Go, Bash script, PowerShell script, C/C++, or any other. Check the [Select Language](/latest/Select-Language/) page for more details.

!!! Info "Info"

    You can use any language for encryption and decryption as long as it uses the PyCript format to read and pass data to and from PyCript.

!!! Info "Info"

    Check the Dummy Code repository [PyCript Template](https://github.com/Anof-cyber/PyCript-Template/) for example codes in JavaScript, Python, and other languages.

PyCript runs system commands to execute the script or binary provided for encryption and decryption. Users must provide the language path. Check the [Select Language](/latest/Select-Language/) page for more details.

Next, users must provide the encryption and decryption file paths. Based on the settings in the PyCript config tab, the extension will provide the encrypted/decrypted body/parameter/header to the script.

### PyCript Flow for Request Decryption

- Take Request body [If Request Type is Complete Body] or WebSocket message payload as raw data (text or binary).
    - If Request Type is Parameter Value or Parameter Key Value, PyCript will loop through each parameter.
- PyCript creates a temp file and saves the raw body/parameter/payload along with plain text headers, using ``\n--BODY_END--\n`` to separate them.

Example Temp Files with Original Request in Burp Suite:

Example Request in Burp Suite

    ```http
    POST /api/login HTTP/1.1
    Host: example.com
    Accept-Encoding: gzip, deflate, br
    Accept: */*
    Accept-Language: en-US;q=0.9,en;q=0.8
    User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36
    Cache-Control: max-age=0
    Content-Type: application/json
    Content-Length: 48

    {"username":"admin","password":"admin"}
    ```

Example Temp File Created By PyCript (Text Data)

    ```text
    {"username":"admin","password":"admin"}
    --BODY_END--
    POST /api/login HTTP/1.1
    Host: example.com
    Accept-Encoding: gzip, deflate, br
    Accept: */*
    Accept-Language: en-US;q=0.9,en;q=0.8
    User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36
    Cache-Control: max-age=0
    Content-Type: application/json
    Content-Length: 48
    ```

Example Temp File Created By PyCript (Binary Encrypted Data)

    ```text
    ��V�6��K� �����[Q}�18'tt�|^KAF��	섐���j~.H�
    --BODY_END--
    POST /decrypt HTTP/1.1
    Host: localhost:8000
    Content-Length: 48
    Content-Type: application/octet-stream
    ```

For WebSocket messages, headers will be empty:

    ```text
    {"username":"admin","password":"admin"}
    --BODY_END--

    ```

In case of Parameter Key or Parameter Key Value, a temp file will be created for each parameter with the parameter value instead of the complete body.

- Once the temp file is created, PyCript will execute the user-provided encryption/decryption script and provide the path of the temp file to the user script:

```bash
/user/path/to/language/like/node user/file/path/encryption.js -d temp-file-path
```

#### Command Line Argument for Body/Parameters

The extension utilizes the -d command line argument to pass the full path of the temp file storing the raw body or parameters along with plain text headers to the encryption/decryption code. If you are writing a script, ensure that your script supports command line arguments with the -d flag.

The value provided with the -d argument will be the path of the temp file. The temp file separates the parameter/body and header with ``\n--BODY_END--\n``. Your script needs to split the data with this marker to get the parameter/body and headers separately.

#### Reading Binary and Text Data

For text-based encryption (like base64, hex), you can read the file as UTF-8 text. For binary encryption (like AES with raw bytes), you must read the file as binary to preserve the exact bytes.

=== "Python - Text Data"

    ```python
    # Read as text for text-based encryption
    with open(file_path, 'r', encoding='utf-8') as f:
        data = f.read()

    body_end_marker = '\n--BODY_END--\n'
    plaintext, headers = data.split(body_end_marker)
    ```

=== "Python - Binary Data"

    ```python
    # Read as binary for binary encryption
    with open(file_path, 'rb') as f:
        data = f.read()

    marker = b'\n--BODY_END--\n'
    marker_index = data.find(marker)
    ciphertext = data[:marker_index]
    headers = data[marker_index + len(marker):].decode('utf-8')
    ```

=== "Node.js - Text Data"

    ```javascript
    const data = fs.readFileSync(absoluteFilePath, 'utf8');
    const bodyEndMarker = '\n--BODY_END--\n';
    const [plaintext, headersRaw] = data.split(bodyEndMarker);
    ```

=== "Node.js - Binary Data"

    ```javascript
    const dataBuffer = fs.readFileSync(absoluteFilePath);
    const bodyEndMarker = '\n--BODY_END--\n';
    const markerIndex = dataBuffer.indexOf(bodyEndMarker);
    const ciphertext = dataBuffer.slice(0, markerIndex);
    const headersRaw = dataBuffer.slice(markerIndex + bodyEndMarker.length).toString('utf8');
    ```

#### Performing Actions and Adding Logic

Your code can perform any action or add any logic to the provided value. Once your script is done with changes to the request body/parameter and header, your script should save the updated request in the same temp file.

For text-based output:

```text
{"username":"admin","password":"admin"}
--BODY_END--
POST /api/login HTTP/1.1
Host: example.com
Content-Type: application/json
```

For binary output, write as binary Buffer/bytes with the marker and headers appended.

- PyCript validates if the command to execute encryption/decryption had any errors. If not, it will read the same temp file to get the updated request and update the request headers and body/parameter.

#### Encryption and Decryption Code

The same logic described above is applicable to encryption and decryption code as well. Ensure that your code follows the specified format and guidelines for encryption and decryption operations.

#### WebSocket Support

WebSocket messages work similarly to HTTP requests/responses, but with these differences:

- WebSocket messages have no headers (the header section after ``--BODY_END--`` will be empty)
- Only the payload is encrypted/decrypted
- The scope check is based on the WebSocket upgrade request URL
- You must enable WebSocket support in the config tab and provide separate encryption/decryption scripts

Example WebSocket temp file:

```text
{"action":"login","user":"admin"}
--BODY_END--

```

#### Notes

- All Request Types will have headers in the temp file. Your script can read headers in all Request Types.
- Only Request Type Complete Body allows your script to provide updated headers so PyCript can update headers as well.
- Even if you select Request Type as Parameter Value or Parameter Key Value, where your script can only read headers but cannot provide updated headers, your script must add ``--BODY_END--`` in the temp file.
- Response encryption/decryption does not include headers. Your script will only have the response body or parameters, not headers, but your script must add ``--BODY_END--`` in the temp file.
- WebSocket messages do not include headers. Only the payload is processed.
- PyCript will always add ``--BODY_END--`` at the end of parameter/body even if headers are not present. It is always recommended to split the request to handle the marker properly.
- For binary data, read and write files in binary mode to preserve exact bytes.




=== "Python - Text Encryption"

    ``` python
    import argparse
    from pathlib import Path

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
    body_end_marker = '\n--BODY_END--\n'
    ciphertext, headers_raw = data.split(body_end_marker)

    # Decrypt the ciphertext
    original_text = decrypt_aes(ciphertext.strip())

    # Parse and update the header
    updated_header = read_parse_header(headers_raw)

    # Write back in the same format
    output = f"{original_text}{body_end_marker}{updated_header}"
    file_path.write_text(output, encoding='utf-8')
    ```

=== "Python - Binary Encryption"

    ``` python
    import argparse
    from pathlib import Path
    from cryptography.hazmat.primitives.ciphers import Cipher, algorithms, modes
    from cryptography.hazmat.backends import default_backend

    def decrypt_aes_binary(ciphertext, key, iv):
        cipher = Cipher(algorithms.AES(key), modes.CBC(iv), backend=default_backend())
        decryptor = cipher.decryptor()
        decrypted = decryptor.update(ciphertext) + decryptor.final()
        return decrypted

    # Parse command-line arguments
    parser = argparse.ArgumentParser()
    parser.add_argument('-d', '--data', required=True)
    args = parser.parse_args()

    # Read as binary
    file_path = Path(args.data).resolve()
    data = file_path.read_bytes()

    # Split binary data
    marker = b'\n--BODY_END--\n'
    marker_index = data.find(marker)
    ciphertext = data[:marker_index]
    headers_raw = data[marker_index + len(marker):].decode('utf-8')

    # Decrypt
    key = b'mysecretkey12345'
    iv = b'n2r5u8x/A%D*G-Ka'
    decrypted = decrypt_aes_binary(ciphertext, key, iv)

    # Write back as binary
    output = decrypted + marker + headers_raw.encode('utf-8')
    file_path.write_bytes(output)
    ```

=== "JavaScript - Text Encryption"

    ``` javascript
    const fs = require('fs');
    const path = require('path');
    const CryptoJS = require("crypto-js");
    const { program } = require('commander');

    program
      .option('-d, --data <file_path>', 'Path to file containing encrypted data');

    program.parse(process.argv);
    const options = program.opts();

    const filePath = options.data;
    const absoluteFilePath = path.resolve(filePath);
    const data = fs.readFileSync(absoluteFilePath, 'utf8');
    const bodyEndMarker = '\n--BODY_END--\n';
    const [ciphertext, headersRaw] = data.split(bodyEndMarker);

    // Decrypt the ciphertext
    const originalText = Decryption(ciphertext.trim());
    const updatedHeader = Read_parse_Header(headersRaw);

    // Write back in the same format
    const output = originalText + bodyEndMarker + updatedHeader;
    fs.writeFileSync(absoluteFilePath, output);

    function Decryption(ciphertext) {
        // your logic to decrypt/encrypt the ciphertext and return updated text
        return ciphertext;
    }

    function Read_parse_Header(headersRaw) {
        // logic to read/edit header
        return headersRaw;
    }
    ```

=== "JavaScript - Binary Encryption"

    ``` javascript
    const fs = require('fs');
    const path = require('path');
    const crypto = require('crypto');
    const { program } = require('commander');

    program
      .option('-d, --data <file_path>', 'Path to file containing encrypted data');

    program.parse(process.argv);
    const options = program.opts();

    const filePath = options.data;
    const absoluteFilePath = path.resolve(filePath);

    // Read as binary buffer
    const dataBuffer = fs.readFileSync(absoluteFilePath);
    const bodyEndMarker = '\n--BODY_END--\n';
    const markerIndex = dataBuffer.indexOf(bodyEndMarker);

    const ciphertext = dataBuffer.slice(0, markerIndex);
    const headersRaw = dataBuffer.slice(markerIndex + bodyEndMarker.length).toString('utf8');

    // Decrypt
    const key = Buffer.from('mysecretkey12345');
    const iv = Buffer.from('n2r5u8x/A%D*G-Ka');

    const decipher = crypto.createDecipheriv('aes-128-cbc', key, iv);
    let decrypted = decipher.update(ciphertext);
    decrypted = Buffer.concat([decrypted, decipher.final()]);

    // Write back as binary
    const markerBuffer = Buffer.from(bodyEndMarker, 'utf8');
    const headerBuffer = Buffer.from(headersRaw, 'utf8');
    const output = Buffer.concat([decrypted, markerBuffer, headerBuffer]);

    fs.writeFileSync(absoluteFilePath, output);
    ```

=== "WebSocket - JavaScript"

    ``` javascript
    const fs = require('fs');
    const path = require('path');
    const CryptoJS = require("crypto-js");
    const { program } = require('commander');

    program
      .option('-d, --data <file_path>', 'Path to file containing WebSocket data');

    program.parse(process.argv);
    const options = program.opts();

    const filePath = options.data;
    const absoluteFilePath = path.resolve(filePath);
    const data = fs.readFileSync(absoluteFilePath, 'utf8');
    const bodyEndMarker = '\n--BODY_END--\n';
    const [payload, headersRaw] = data.split(bodyEndMarker);

    // Decrypt WebSocket payload (no headers for WebSocket)
    const decryptedPayload = Decryption(payload.trim());

    // Write back - headers will be empty for WebSocket
    const output = decryptedPayload + bodyEndMarker + headersRaw;
    fs.writeFileSync(absoluteFilePath, output);

    function Decryption(payload) {
        // your WebSocket decryption logic
        return payload;
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





