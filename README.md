# HLSJava
Hierarhical Location Service application for Communication and Information Security course

## Author: Michael J. May

## Course: Communication and Information Security
### Semester 2 5782

## List of Students in the group

Sasha Chernin https://github.com/sashachernin 32345871

Hai Palatshi https://github.com/Hai-palatshi 203499025

### List of tasks and who worked on what
| Task | Performed by | Hours
|------|--------------|-------
| Create function that encrypts messages  | Sasha    | 2.5 Hours
| Create function that encrypts files  | Sasha    | 2.5 Hours
| Create function that decrypts messages  | Sasha    | 2.5 Hours
| Create function that decrypts file  | Sasha    | 2.5 Hours
| Create function retrieves password  | Sasha    | 2.5 Hours
| Integrate message encryption with the code  | Hai    | 3 Hours
| Integrate file encryption with the code  | Hai    | 4 Hours
| Create test regime  | Sasha    | 1 Hours
| Create test regime  | Hai    | 1 Hours
| General testing of functionality  | Hai    | 2 Hours

## Instructions

### Compilation instructions
The compilation was done via IntelliJ IDEA. After opening the project in IDEA:

File -> Project Structure -> Project Settings -> Artifacts -> Click green plus sign -> Jar -> From modules with dependencies -> Choose HLSApplication as main class -> OK -> OK.

Build -> Build Artifact -> Build.

The JAR file can be found in :

ProjectName | out | artifacts | ProjectName_jar | ProjectName.jar
### Running instructions
1. Edit the Config file and set your desired password. Make sure its the same password across all nodes.
2. Double-click runme.bat file

### Configuration file format
There is only one line in the Config file, which is basically the password:

  <img src="https://user-images.githubusercontent.com/50183122/164942919-d10dd31f-9a16-486c-8d22-a5853dc6602b.png" width="50%" height="50%"><br>


## Documentation

### Thread safety
To ensure thread safety we wanted to make sure that all the changes that we make (like adding encryption), don't have any affect on the way that the application currently working, all aspects wise including thread safety.

For example:

1. **Sending/receiving encrypted message** - the only change that we made is encrypting the original message right before its being sent, and decrypting any message that is received. That way, no additional changes needed on the code that will improve thread safety.
2. **Sending/receiving encrypted file** - There is no change in the way that the file is being sent, the only thing we added is when a user adds a local file to the indexer, the addition function creates a encrypted version of that file that is stored next to the original file, and that is the version that is being sent when someone tries to retrieves it. Again, no changes to the internal mechanism of the application.
### How encryption and decryption work
#### encryption/decryption of messages
* right before each node sends a message, we call a function to encrypt this message, so that the encrypted string would be sent instead of the original
* the encryption function receives a string and returns a string
* the encryption function generates random iv with **SecureRandom**
* both encrypted message and the iv are being appended to an instance of **ByteArrayOutputStream**. iv is occupying the first 16 bytes
* this **ByteArrayOutputStream** object is being convert to bytes array and then to a string with the **Base64** library.
---
* uppon receiving a message, a decryption function is call, which decrypts that message
* only after received message is decrypted, its being processed accordantly
* the decryption function converts the received string to array of bytes with **Base64** library
* the decryption slices the bytes array using **Arrays.copyOfRange**. first slice is the iv and the second slice the the message to decrypt
* after decryotion is done, the bytes array is converted to string and returned

#### encryption/decryption of files
* when each file is being added to the local indexer(Browse->Add), a encryption function is called
* this encryption function encrypts this file and stores it in the same path as the original and adds a ".enc" suffix to the filename(e.g fileName.txt->fileName.txt.enc)
* the encryption function generates random iv with **SecureRandom**
* the files is converted to bytes array using **Files.readAllBytes**
* both encrypted files bytes and the iv are being appended to the same file using **FileOutputStream** and **CipherOutputStream**
---
* whenever someone tries to retrieve a file, the ".enc" copy is being sent
* upon receiving a file, its being decrypted right away with a decryption function
* this function converts the file to bytes array with **Files.readAllBytes**
* get the bytes without the iv using **Arrays.copyOfRange**, and gets the iv using **FileInputStream**, reading the first 16 bytes of the original array
* finally, a new decrypted file is being created without the ".enc" suffix, using **FileOutputStream** and **CipherOutputStream**

All the encryption and decryption functions can be founc in the "Encryption.java" file

