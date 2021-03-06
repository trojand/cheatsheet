# Archiving and compression of files in Windows before extracting


## Instructions

1. Download **7za.exe** from [7zip](https://www.7-zip.org/download.html)
    * Download the standable version. Usually contains the description: "*7-Zip Extra: standalone console version, 7z DLL, Plugin for Far Manager*"
1. Run on target machine (Windows)
    ```batch
    echo PASSWORD_FOR_ZIP_FILE| 7za.exe a -mhe -p zip_file_name C:\target\folder\*
    ```
    * *Note: Make sure `PASSWORD_FOR_ZIP_FILE` is __next__ to the “|” pipe symbol, no space in between. If it has space in between, you have to include the space in the password*


-----------------------------------
## Tips
* Check disk space/partition before archiving a large file or folder
    ```batch
    echo list volume|diskpart
    ```
* In case of timeouts given the limited shell when archiving large files

    * Create a batch file:

        
        ```batch
        archive.bat
        echo PASSWORD_FOR_ZIP_FILE| 7za.exe a -mhe -p zip_file_name C:\target\folder\*
        ```
        

    * Run the batch file with `#!batch start /b archive.bat`
    
