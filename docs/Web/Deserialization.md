# Deserialization Tips

## .NET

### Tools
* dnSpy
* [ysoserial for .NET](https://github.com/pwntester/ysoserial.net)

### Debug using dnSpy
* Replace
    ```
    [assembly:Debuggable(DebuggableAttribute.DebuggingModes.IgnoreSymbolStoreSequencePoints)]
    ```
* with (once in dnSpy, right click on “Edit Assembly Attributes”)
    ```
    [assembly: Debuggable(DebuggableAttribute.DebuggingModes.Default | DebuggableAttribute.DebuggingModes.DisableOptimizations | DebuggableAttribute.DebuggingModes.IgnoreSymbolStoreSequencePoints | DebuggableAttribute.DebuggingModes.EnableEditAndContinue)]
    ```
* Make IIS load the module and not copy and execute from a temp directory
    ```
    iisreset /noforce

    # Exit and reload dnSpy and Attach to process "w3p*"
    # Pause the debugging and open the modules
    ```

### General Tips
* Make use of “Call Stack”, “Watch” (Variable) and Breakpoints (F5,F9,F10)


### XML Serialization
* Grep for the following which might give a hint for XML Deserialization 
    ```
    .GetType(
    .GetType().AssemblyQualifiedName
    XmlSerializer(
    Serializer
    .Serialize(
    .Deserialize(
    = new XmlDocument()
    DeSerializeHashtable
    XmlUtils.DeSerializeHashtable
    ```
* Payload or abusable functions
    * `FileSystemUtils.PullFile`
    * `ObjectDataProvider`
        * Can be used to provide a binding source
        * To retrieve data from any of your called methods and classes without violating XMLSerializers restrictions/limitations to public fields and properties
    * `ExtendedWrapper`
        * To have a generic wrapper to fake a method so it would be accepted for example by XmlSerializer
* Public read/write properties and fields of public classes
* Only public properties and fields not public class
* Cannot serialize class methods”
* Objects
    * XmlElement
    * XmlNode
    * DataSet

---

## Java

### Notes from Afinepl's blog
* [URL](https://afinepl.medium.com/testing-and-exploiting-java-deserialization-in-2021-e762f3e43ca2)
* During whitebox analysis look for `readObject()`
* Practice on [Vulnerable Java](https://github.com/0xluk3/Java-Deserialization-Basics/blob/main/Vulnerable.java)
* Use [Nicky Bloor’s](https://twitter.com/NickstaDB) [Serialization dumper](https://github.com/NickstaDB/SerializationDumper) to inspect serialized objects to confirm what they are.
* Apart from deserialization flaws to be exploited with Ysoserial, it is  possible that a logical information is being transported in the  serialized stream (e.g. user=admin)
* Ysoserial has more usages than just getting instant RCE. 
    * For blind or quick testing, use URLDNS or JRMPClient/Listener payloads. 
    * Apart from instant  RCE, it’s worth noticing how to use payloads related to FileUpload or  Object Lookup.
* Be prepared to face stack traces
    * See what to do, If you find errors like SerialUID Mismatch or ClassNotFoundException
