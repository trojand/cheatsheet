# Mobile App Security Testing

Some commands

## Logcat
* Following process
    * Can be made more verbose/detailed though
    ```bash
    adb logcat --pid=$(adb shell pidof -s <mobile_app_name_i.e._com.imacompany.imanandroidapp>)
    ```
