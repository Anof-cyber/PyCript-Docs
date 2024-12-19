
## Prerequisites


PyCript provides the flexibility to implement custom encryption and decryption algorithms using any programming language. To utilize PyCript, one must have a compiled binary of the custom encryption and decryption code with Windows PE or Mach-O etc or the language to run the script or executable like Go, Python, Nodejs etc.

* Burp Suite configured with Jython to install the Python extension 



!!! info "Note"

    
    PyCript v1.0 change the working of the extension, the older script of encryption and decryption will not work with version 1.0
	

	

!!! info "Note"

    If you choose to use the Java programming language with PyCript, you will require a JAR file to be provided. Please ensure that you include the JAR file, and not the compiled class file or Java source code, when using PyCript.


!!! info "Note"

    When opting to utilize JavaScript with PyCript, it's essential to ensure that all necessary libraries are present in the folder where encryption and decryption files are stored. This is particularly important as many Node.js libraries require the presence of related dependencies in the same directory as node_modules.



