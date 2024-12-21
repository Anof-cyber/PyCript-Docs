## Selecting Language


The PyCript from version 0.4 supports multiple languages, and also it allows you to run scripts without needing the language in the system environment.

In the earlier version, you could select the language from the drop-down from Python, Java and Nodejs and extensions except you to have these languages in your system environment so it should be accessible from anywhere in the system terminal or CMD.

To work with different language you have to use different method to allow PyCript to execute the script or binary provided for encryption and decryption.


The extension now allows you to select language binary like Python.exe or Go.exe or other binary based on your OS.

You have 2 option 

- Click on the Select Language Binary Path button and select the language binary.

    <img src="https://raw.githubusercontent.com/Anof-cyber/PyCript-Docs/refs/heads/gh-pages/0.4/assets/select-lang.png"/>

- Or you can paste the full path directly into the text box.

    <img src="https://raw.githubusercontent.com/Anof-cyber/PyCript-Docs/refs/heads/gh-pages/0.4/assets/type-lang.png"/>

This is only if you don't have language in your system env and get an error as command not found such as python not found but it is installed. 

If it is in your env instead of full path you can type python or python3 or go or node etc in the text box as per your encryption decryption script language.


### Examples

- If you are using bash script for enc dec script you can just type ``bash`` or you can select shell path like ``/bin/sh`` or ``/bin/bash``
- If you are using powershell script for enc dec script you can type ``powershell.exe -File``
- If you are using Java you can just select the java binary full path or just type ``java``. 
    * you don't need to add ``-jar`` like ``java -jar``, extension check if enc dec file is ``.jar`` it will auto add ``-jar`` to execute it.

### Binary

If you are providing an encryption-decryption file as binary like compiled code from C C++ or Go language or any other like C# etc. The compiled code doesn't need any language like python script.exe etc.

For compiled binary, you can click on the Clear Language Selection to remove it, directly removing just from the text will not work as language path are persistent. The extension will run the executable directly.



### Notes

- You cannot have separate languages for encryption and decryption like encryption script is in Python and decryption in Java or any other both should be in the same language.
    - You can have different language if its compiled code like for Windows EXE one is C++ one in GO or C# etc.
- The same is true for the request and response, you cannot have both encryption decryption scripts for requests in Python and for response both scripts are in another language.
- When using bash script you cannot use Windows WSL. Path for WSL and windows are different.