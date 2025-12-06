
# Installation

!!! Warning "Warning"

    Please make sure that all the [prerequisites](Prerequisites.md) mentioned are installed first.

## Manual Installation

- Download the latest release JAR file from the [PyCript Releases](https://github.com/Anof-cyber/PyCript/releases) page on GitHub.
- Alternatively, you can build from source by cloning the repository:

```
git clone https://github.com/Anof-cyber/PyCript
cd PyCript
.\gradlew.bat jar    # Windows
./gradlew jar        # Linux/Mac
```

- The JAR file will be located in `build/libs/PyCript-2.0.jar`
- Open Burp Suite, and navigate to the "Extensions" tab.
- Click on the "Add" button to add a new extension.
- In the "Extension Type" field, select "Java"
- Click on the "Select file" button and browse to the location of the PyCript JAR file.
- Click on the "Open" button to load the PyCript extension into Burp Suite.
- The PyCript extension should now be listed under the "Extensions" tab in Burp Suite, and you can use its features.

## Burp Suite BApp Store Installation

!!! Info "Info"

    You can install the extension directly from the Burp Suite BApp Store for easier updates and management.



