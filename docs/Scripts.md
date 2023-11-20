### Working of the Extension

The extension utilizes JavaScript/Python/Java Jar files for executing encryption and decryption operations by running system commands.





```terminfo
node user/provided/javascript/file/path/encryption.js -d bas64-encodeed-encrypted-request-body
```

```terminfo
python user/provided/python/file/path/encryption.py -d bas64-encodeed-encrypted-request-body
```

```terminfo
java -jar user/provided/java/file/path/encryption.jar -d bas64-encodeed-encrypted-request-body
```

#### Command Line Argument for Body/Parameters

The extension utilizes the -d command line argument to pass the body or parameters to the Java/Python/JavaScript code. If you are writing a script, ensure that your script supports command line arguments with the -d flag.


#### Base64 Encoding of -d Value

The value provided with the -d argument will be in base64 format. Even if your original request is already in base64, the extension will perform base64 encoding again and provide the encoded value to your code. To obtain the original value of your request body or parameter, you need to decode the base64 value in your code.



#### Base64 Encoding for String Consistency

The extension performs base64 encoding to ensure consistency and avoid any string-related issues, such as spaces or special characters.


#### Performing Actions and Adding Logic

Your code can perform any action or add any logic to the provided value. The extension will read the output of the system command, so it is essential that your code prints the updated value accordingly.




#### Consistency Across Request Types and Response Types

This approach and code structure apply to all request types and response types in the extension, except for the "Custom Request" and "Custom Request (Edit Header)" types.

#### Encryption and Decryption Code

The same logic described above is applicable to encryption and decryption code as well. Ensure that your code follows the specified format and guidelines for encryption and decryption operations.

#### Output Format

The output or print statement provided by your code will be read by the extension, and it should be in the original format, not in base64. Make sure your code returns the result in the expected format.


!!! Warning "Warning"

    There should not be more than one print statement or output in your code. The extension relies on reading the system command output, so having multiple print statements or outputs can cause unexpected behavior.


=== "Python"

    ``` python
    import parser
    from base64 import b64decode

    parser = argparse.ArgumentParser(description='Process data argument')
    parser.add_argument('-d', '--data', help='Input data')

    args = parser.parse_args()
    ciphertext = b64decode(args.data).decode('utf-8')

    ## Your encryption logic

    print(output)
    ```

=== "JavaScript"

    ``` javascript
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


=== "Java"

    ``` java
    import java.util.Base64;
    
    public class AESCBC128Decrypt {

    public static void main(String[] args) {
        String base64Ciphertext = args[1];

        try {
            String ciphertext = new String(Base64.getDecoder().decode(base64Ciphertext), StandardCharsets.UTF_8);
            
            /// You encryption decryption logic

            System.out.println(output);
        } catch (Exception e) {
            e.printStackTrace();
        }


    }}
    
    ```



### Custom Request Type

When implementing the Custom Request feature in your Burp Suite extension, there are a few changes to consider in your code. The points mentioned earlier in the documentation will still apply, but with the addition of some further details. Custom Request will have additional command line arguments as -h that will allow you to read the request header as well.


```terminfo
node user/provided/javascript/file/path/encryption.js -d bas64-encodeed-encrypted-request-body -h [[POST /crypto/user/ HTTP/1.1, Host: localhost:8000, Key: 1234, Iv: 1234]
```

```terminfo
python user/provided/python/file/path/encryption.py -d bas64-encodeed-encrypted-request-body -h [[POST /crypto/user/ HTTP/1.1, Host: localhost:8000, Key: 1234, Iv: 1234]
```

```terminfo
java -jar user/provided/java/file/path/encryption.jar -d bas64-encodeed-encrypted-request-body -h [[POST /crypto/user/ HTTP/1.1, Host: localhost:8000, Key: 1234, Iv: 1234]
```


The custom request functionality remains unchanged, with the addition of the -h flag to include the request header in a list/array format. Unlike the -d flag, the -h flag does not contain any Base64-encoded data.

!!! Warning "Warning"

    
    From the PyCript version 0.3 -h flag will be provided in base64 encoded format same as -d flag. Make sure to modify the scipt to handle the same. 


```javascript
[POST /crypto/myprofile/ HTTP/1.1, Host: localhost:8000, Accept-Encoding: gzip, deflate, Accept: */*, Accept-Language: en-US;q=0.9,en;q=0.8, User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/103.0.5060.134 Safari/537.36, Connection: close, Key: 1234, Iv: 1234, Cache-Control: max-age=0, Content-Type: application/x-www-form-urlencoded, Content-Length: 24]

```



=== "Python"

    ``` python
    import argparse
    from base64 import b64decode

    parser = argparse.ArgumentParser(description='Process data argument',add_help=False)
    parser.add_argument('-d', '--data', help='Input data')
    parser.add_argument('-h', '--header', help='header data')


    args = parser.parse_args()
    ciphertext = b64decode(args.data).decode('utf-8')
    header = args.header

    ## Your encryption logic

    print(output)
    
    ```

=== "JavaScript"

    ``` javascript
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


=== "Java"

    ``` java
    import java.util.Base64;
    
    public class AESCBC128Decrypt {

    public static void main(String[] args) {


      for (int i = 0; i < args.length; i++) {
            if ("-d".equals(args[i])) {
                base64Ciphertext = args[i + 1];
            }
            if ("-h".equals(args[i])) {
                String headerStr = args[i + 1];
            }
        }

        try {
            String ciphertext = new String(Base64.getDecoder().decode(base64Ciphertext), StandardCharsets.UTF_8);
            
            /// You encryption decryption logic

            System.out.println(output);
        } catch (Exception e) {
            e.printStackTrace();
        }


    }}
    
    ```

!!! Warning "Warning"

    
    Given the limitation of only being able to read the header, it is important to ensure that you only print the output of the encryption/decryption process and not the header. 











### Custom Request (Edit Headers)

Custom Request and Custom Request (Edit Header) have several similarities with a few key differences:

1. In Custom Request (Edit Header), the value of the -h parameter is also base64 encoded, unlike the plain text value in Custom Request.

2. The -h value in Custom Request is in a list/array format, whereas in Custom Request (Edit Header), the header is in raw format, just as you would see it in Burp Suite.

3. Since Custom Request (Edit Header) allows for header editing, it's necessary to print the header as well, but in base64 format.

4. For all types, pycript will pass the -d parameter for the body/parameter in base64 format.

5. The output of the encrypted/decrypted string/body will also be in base64 format.

6. It is important to print the base64 header first, followed by the base64 body/parameter.

7. Since the header is in raw format, you can split it using new line characters (\n) to edit or add new headers.

8. When printing the updated header in base64, it should retain the same raw format. If you split the header with new line characters to make changes, you will need to join it again to restore it to the original raw format.



```terminfo
node user/provided/javascript/file/path/encryption.js -d bas64-encodeed-encrypted-request-body -h UE9TVCAvYXBpL2dldHVzZXI0IEhUVFAvMS4xCkhvc3Q6IDEyNy4wLjAuMTo4MDAwCkFjY2VwdC1FbmNvZGluZzogZ3ppcCwgZGVmbGF0ZQpBY2NlcHQ6ICovKgpTaWduYXR1cmU6IDA4NjFjMDBiODFlNWY2OTZkNGE5MjM3MGE4OTYxYzEyCkFjY2VwdC1MYW5ndWFnZTogZW4tVVM7cT0wLjksZW47cT0wLjgKVXNlci1BZ2VudDogTW96aWxsYS81LjAgKFdpbmRvd3MgTlQgMTAuMDsgV2luNjQ7IHg2NCkgQXBwbGVXZWJLaXQvNTM3LjM2IChLSFRNTCwgbGlrZSBHZWNrbykgQ2hyb21lLzEwMy4wLjUwNjAuMTM0IFNhZmFyaS81MzcuMzYKQ29ubmVjdGlvbjogY2xvc2UKQ2FjaGUtQ29udHJvbDogbWF4LWFnZT0wCkNvbnRlbnQtVHlwZTogYXBwbGljYXRpb24vanNvbgpDb250ZW50LUxlbmd0aDogMzg=
```

```terminfo
python user/provided/python/file/path/encryption.py -d bas64-encodeed-encrypted-request-body -h UE9TVCAvYXBpL2dldHVzZXI0IEhUVFAvMS4xCkhvc3Q6IDEyNy4wLjAuMTo4MDAwCkFjY2VwdC1FbmNvZGluZzogZ3ppcCwgZGVmbGF0ZQpBY2NlcHQ6ICovKgpTaWduYXR1cmU6IDA4NjFjMDBiODFlNWY2OTZkNGE5MjM3MGE4OTYxYzEyCkFjY2VwdC1MYW5ndWFnZTogZW4tVVM7cT0wLjksZW47cT0wLjgKVXNlci1BZ2VudDogTW96aWxsYS81LjAgKFdpbmRvd3MgTlQgMTAuMDsgV2luNjQ7IHg2NCkgQXBwbGVXZWJLaXQvNTM3LjM2IChLSFRNTCwgbGlrZSBHZWNrbykgQ2hyb21lLzEwMy4wLjUwNjAuMTM0IFNhZmFyaS81MzcuMzYKQ29ubmVjdGlvbjogY2xvc2UKQ2FjaGUtQ29udHJvbDogbWF4LWFnZT0wCkNvbnRlbnQtVHlwZTogYXBwbGljYXRpb24vanNvbgpDb250ZW50LUxlbmd0aDogMzg=
```

```terminfo
java -jar user/provided/java/file/path/encryption.jar -d bas64-encodeed-encrypted-request-body -h UE9TVCAvYXBpL2dldHVzZXI0IEhUVFAvMS4xCkhvc3Q6IDEyNy4wLjAuMTo4MDAwCkFjY2VwdC1FbmNvZGluZzogZ3ppcCwgZGVmbGF0ZQpBY2NlcHQ6ICovKgpTaWduYXR1cmU6IDA4NjFjMDBiODFlNWY2OTZkNGE5MjM3MGE4OTYxYzEyCkFjY2VwdC1MYW5ndWFnZTogZW4tVVM7cT0wLjksZW47cT0wLjgKVXNlci1BZ2VudDogTW96aWxsYS81LjAgKFdpbmRvd3MgTlQgMTAuMDsgV2luNjQ7IHg2NCkgQXBwbGVXZWJLaXQvNTM3LjM2IChLSFRNTCwgbGlrZSBHZWNrbykgQ2hyb21lLzEwMy4wLjUwNjAuMTM0IFNhZmFyaS81MzcuMzYKQ29ubmVjdGlvbjogY2xvc2UKQ2FjaGUtQ29udHJvbDogbWF4LWFnZT0wCkNvbnRlbnQtVHlwZTogYXBwbGljYXRpb24vanNvbgpDb250ZW50LUxlbmd0aDogMzg=
```





=== "Python"

    ``` python
    import argparse
    from base64 import b64decode

    parser = argparse.ArgumentParser(description='Process data argument',add_help=False)
    parser.add_argument('-d', '--data', help='Input data')
    parser.add_argument('-h', '--header', help='header data')


    args = parser.parse_args()
    ciphertext = b64decode(args.data).decode('utf-8')
    header = b64decode(args.header).decode('utf-8')

    ## Your encryption logic
    ## Update the header

    ## print the heeader first then bod
    print(b64encode(updated_header))
    print(b64encode(updatedbody))
    
    ```

=== "JavaScript"

    ``` javascript
    const program = require("commander");
    const { Buffer } = require('buffer');

    program
      .option("-d, --data <data>", "Data to process")
      .option("-h, --header <data>", "Header")
      .parse(process.argv);

    const options = program.opts();
    const requestbody = Buffer.from(options.data, 'base64').toString('utf8');
    const header = Buffer.from(options.header, 'base64').toString('utf8');

    'Your encryption and decryption logic should be here'
    'Updated the header'


    // printt the updated base64 header and updated base64 body
    console.log(Buffer.from(updatedheader).toString('base64'));
    console.log(Buffer.from(updatedbody).toString('base64'));
    ```


=== "Java"

    ``` java
    import java.util.Base64;
    
    public class AESCBC128Decrypt {

    public static void main(String[] args) {


      for (int i = 0; i < args.length; i++) {
            if ("-d".equals(args[i])) {
                base64Ciphertext = args[i + 1];
            }
            if ("-h".equals(args[i])) {
                String base64Header = args[i + 1];
            }
        }

        try {
            String ciphertext = new String(Base64.getDecoder().decode(base64Ciphertext), StandardCharsets.UTF_8);
            // Decode the Base64 header
            byte[] decodedHeader = Base64.getDecoder().decode(base64Header);
            String rawHeader = new String(decodedHeader, StandardCharsets.UTF_8);
            
            /// You encryption decryption logic

            String updatedHeaderBase64Body = Base64.getEncoder().encodeToString(updatedHeader.getBytes(StandardCharsets.UTF_8));
            String updatedBase64Body = Base64.getEncoder().encodeToString(updatedBody.getBytes(StandardCharsets.UTF_8));
            System.out.println(updatedHeaderBase64Body);
            System.out.println(updatedBase64Body);
        } catch (Exception e) {
            e.printStackTrace();
        }


    }}
    
    ```





## Debug

If you encounter difficulties decrypting or encrypting the request within the extension, it is important to ensure that your code is functioning correctly. To encrypt or decrypt the request body or string, you can follow these steps: 

1. Base64 encode the value you wish to encrypt or decrypt.
2. Execute the same command in your terminal or CMD.

By performing these steps, you can verify the functionality of the encryption or decryption process outside of the extension environment.




## Demo Code
If you are looking for demo encryption decryption code for common type of encryption or logic you can get it from the [PyCript Template](https://github.com/Anof-cyber/PyCript-Template)


