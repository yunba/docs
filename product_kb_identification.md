# device ID, Customid, 以及 MQTT Identification

在 MQTT 协议中，设备根据身份信息连接上服务器（Broker），身份信息包括 ClientIdentifier(下简称 Client ID)、Username 以及 Password。在云巴的 sdk 中，我们也是基于此来定义我们的 Device ID 以及 Customid 的：Device ID 即云巴系统中对于此 MQTT 身份信息的内部映射，Customid 即对于此 socket.io 连接的 session id的用户自定义标识；具体关系可以从下面的示意图中看到。

![productpng_kb_identification](https://raw.githubusercontent.com/yunba/docs/master/image/productpng_kb_identification.png)

这些 ID 和 MQTT 身份信息我们称为`注册信息`。下面我们来看看 `注册信息` 具体代表的含义。

## 注册信息代表着什么

一般来说，注册信息（特指 Username、device ID 以及 Customid）是唯一的，用来在云巴系统中唯一代表该设备或socket io 中的 session id。在云巴中，订阅关系、别名和离线消息都是与注册信息对应的。

另外，在云巴系统中，同一连接信息是互斥的，也就是说*使用相同的注册信息，只有一个设备会保留连接，后连接的会把前一个连接挤下线*

**注：日活的统计即以此为依据**

## 注册信息是怎么生成的

经典的注册流程：
1. 用户在调用了 SDK 中的初始化方法（比如 iOS sdk 中的 setupWithAppkey）后，一般来说，SDK 会检查本地是否有注册信息的缓存，如果有的话就不会申请新的用户；
2. 如果没有，SDK 就会去向我们的注册服务申请；
3. 此时服务器会生成包含 Client ID、Username、Password、Device ID 的注册信息，并返回给 SDK；
4. 如果可能，SDK 会把注册信息保存下来。

在某些 SDK，比如 `C` 和 `JavaScript` 中，用户可以通过 Device ID 或 Customid 来进行初始化。使用这些信息来进行初始化（注册）的流程略有不同。

即：对于 C sdk，它并不会尝试把 Device ID 存储下来。使用 Device ID 来进行初始化的时候，它会以此为依据向服务器请求与此 Device ID 对应的注册信息；

对于 JavaScript 使用的 socket.io 来说，由于 socket.io 使用 session id 来区别每次连接，也就是说 session id 就相当于注册信息，我们使用 Customid 来代表 session id。

## 我怎么找到这些注册信息

对于用户来说，注册信息是透明的，并不会在日常使用中接触到，但是它们在 debug 的时候往往非常有用。

在不同的 sdk 中，存储的方法也会不同，下面我们来具体看看。

### Android
Android sdk 中，注册信息存储在缓存文件`mqpush.serverconfig`中，文件路径一般在应用文件路径中。

### iOS
iOS sdk 中，注册信息存储在 KeyChain 中，用户一般不允许访问此数据。

### JavaScript
如果允许使用 Cookie，注册信息会存在 Cookie 中，在用户下次使用，且未指定新的 Customid 时会使用此连接信息。

### C sharp
C# sdk 中，注册信息存储在文件`MqttLib.dll.config`中。
