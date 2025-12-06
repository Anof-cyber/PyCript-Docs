## Prerequisites

PyCript provides the flexibility to implement custom encryption and decryption algorithms using any programming language. To utilize PyCript, you must have either a compiled binary of your custom encryption and decryption code (Windows PE, Mach-O, ELF, etc.) or the runtime environment installed for your chosen language (Go, Python, Node.js, etc.).

* Burp Suite Professional or Community Edition (2023.1 or later recommended)

!!! info "Note"

    PyCript v2.0 is a complete rewrite using Java and the Montoya API. Scripts and configurations from v1.x are not compatible with v2.0 and will require updates.

!!! info "Note"

    If you choose to use the Java programming language with PyCript, you will require a JAR file to be provided. Please ensure that you include the JAR file, and not the compiled class file or Java source code, when using PyCript.

!!! info "Note"

    When opting to utilize JavaScript with PyCript, it's essential to ensure that all necessary libraries are present in the folder where encryption and decryption files are stored. This is particularly important as many Node.js libraries require the presence of related dependencies in the same directory as node_modules.