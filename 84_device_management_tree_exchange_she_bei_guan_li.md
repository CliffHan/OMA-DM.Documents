# 8.4 Device Management Tree Exchange 设备管理树交换
The Management Tree is a hierarchical arrangement of managed objects in a device, which defines the management view of the device. In order to effectively manage a device, the DM Server SHOULD be able to manage relevant parts of the Management Tree in the device. Without knowing the Management Tree, the DM server would not be able to access a specific managed object in the device and hence effectively manage the device.<br/>
管理树是设备中管理对象的分层布置，其定义设备的管理视图。为了有效地管理设备，DM服务器应该能够管理设备中管理树的相关部分。在不知道管理树的情况下，DM服务器将不能访问设备中的特定的被管理对象，因此能更有效地管理设备。

The method for exchanging Management Tree information between a DM client and a DM server involves:<br/>
在DM客户端和DM服务器之间交换管理树信息的方法包括：
* Representing the wanted information from the tree, without loss of information about its hierarchical structure.<br/>
从树中表示想要的信息，而不损失其层次结构的信息
* Sending it in an OMA DM command to the recipient.<br/>
在OMA DM命令中将其发送到接收者

Management Tree information can be represented and delivered by using an XML representation as described in section 8.5.<br/>
管理树信息可以通过使用第8.5节中描述的XML表示来表示和传递。

The OMA DM specifications allow the DM server to add, replace and delete parts of a Management Tree in a single package, but to get the information from the client DM server requires multiple round trips. The following section specifies a way to Get a part of a Management Tree in a single package.<br/>
OMA DM规范允许DM服务器在单个包中添加，替换和删除管理树的部分，但是从客户端DM服务器获得信息需要多次往返。以下部分指定了在单个程序包中获取管理树的一部分的方法。

## 8.4.1 Requesting a Part of a Management Tree 请求管理树的一部分
The DM server uses the Get command with an attribute in the URI to retrieve the Management Tree information identified by the attribute in the URI. The client SHOULD send all the requested information and the information of the child Nodes to which the DM server has a read access right as specified in the table below. In case this feature is not supported the client SHOULD return status code(406) Optional Feature Not Supported.<br/>
DM服务器使用具有URI中的属性的Get命令来检索由URI中的属性标识的管理树信息。客户端应该发送所有请求的信息和DM服务器具有的读取访问权限的子节点的信息，如下表所指定。如果不支持此功能，客户端应返回状态代码（406）Optional Feature Not Supported。

The attributes are added to the URI specified in the Item element inside the Get command. The Get command and the URI in it have the following format:
`GET <URI>?list=<attribute>`<br/>
属性将添加到Get命令中的Item元素中指定的URI。Get命令和其中的URI具有以下格式：
`GET <URI>？list = <attribute>`

Where the attribute is addressed by appending `?list=<attribute>` to the retrieved Node’s URI.<br/>
其中通过将 `?<list> = <attribute>`追加到检索到的Node的URI来寻址属性。

The possible attributes are:<br/>
可能的属性是：

| Attribute 属性 | Description 描述 |
| -- | -- |
| Struct | The structure of a Management Tree is returned, without any data.<br/> 返回管理树的结构，没有任何数据。 |
| StructData | The structure of the Management Tree is returned, with the Leaf Nodes data.<br/> 返回管理树的结构，其中包含叶节点数据。 |
| TNDS | The returned data is a serialized sub-tree as defined in [DMTNDS].<br/> 返回的数据是在[DMTNDS]中定义的序列化子树。 |

## 8.4.2 Representation Response of the Management Tree 管理树的表示响应

Representing the tree using multiple Item elements inside the OMA DM Result command is the simplest approach. Requested information from the Node (URI in the Get command with attribute) is embedded into an Item inside a Result command with the following requirements (See also the examples in the Appendix):<br/>
在OMA DM Result命令中使用多个Item元素表示树是最简单的方法。来自节点的请求信息（具有属性的Get命令中的URI）被嵌入到Result命令内的项中，它具有以下要求（另见附录中的示例）：

* Struct attribute – Meta MUST be used to indicate the Type and Format of the Node, unless the Type and Format have the default values [META]. LocURI inside the Source MUST indicate the URI of the Node.<br/>
结构属性 - 元必须用于指示节点的类型和格式，除非类型和格式具有默认值[META]。源中的LocURI必须指示节点的URI。

* StructData attribute - Meta MUST be used to indicate the Type and Format of the Node, unless the Type and Format have the default values [META]. LocURI inside the Source MUST indicate the URI of the Node. Leaf Node data MUST be included to the Data element.<br/>
StructData属性 - 元必须用于指示节点的类型和格式，除非类型和格式具有默认值[META]。源中的LocURI必须指示节点的URI。叶节点数据必须包含在Data元素中。

* TNDS attribute – META MUST be used to indicate the Type and Format as defined in [DMTNDS]. LocURI inside the Source MUST indicate the URI of the starting Node. If no extra attributes are included after “TNDS” the device SHOULD include all valid properties that that server has access rights to receive. Optionally the server MAY request which properties that only SHOULD be included in the Serialized management sub-tree via adding “+” and the property name. More than one property name are allowed in the list. The server MAY also exclude some properties from the default, all property-list, via adding a “-“character followed by the property name. It is also possible to add multiple “-PropertyName” after each other. The include property “+” and remove property “-“are not possible to combine in the same Get command. Valid Property names are all properties defined in chapter 6.1 in [DMTNDS] which are inside “RTProperties” and additionally the “Value” property. The Client MUST return Status Code “406 Optional feature not supported” if the device does not support TNDS or anyone of the present properties in the presented list.<br/>
TNDS属性 - META必须用于指示[DMTNDS]中定义的类型和格式。源中的LocURI必须指示起始节点的URI。如果在“TNDS”之后没有包括额外的属性，设备应该包括服务器有权访问的所有有效属性。可选地，服务器可以通过添加“+”和属性名称来请求哪些属性仅应该被包括在序列化管理子树中。在列表中允许多个属性名称。服务器也可以通过添加一个“-”字符和属性名称，从默认的所有属性列表中排除一些属性。也可以在彼此之后添加多个“-PropertyName”。 include属性“+”和remove属性“-”不能在同一个Get命令中组合。有效属性名称是在[DMTNDS]中的第6.1章中定义的所有在“RTProperties”内部的属性，另外还有“Value”属性。如果设备不支持TNDS或提供列表中的任何现有属性，客户端必须返回状态码“406 Optional feature not supported”。

Example on valid property list: “?list=TNDS+Value+ACL” for only data and ACL or “?list=TNDS-Format” for all supported properties except the Format Property.<br/>
有效属性列表示例：仅对数据和ACL使用“?list=TNDS+Value+ACL”，对格式属性以外的所有受支持属性使用“?list=TNDS-Format”。

For Structand Struct Data this information from the requested Nodes MAY bei ncluded into a multiple Item elements inside a Result command or there MAY be multiple Result commands with single Item.<br/>
对于结构化结构数据，来自所请求节点的这些信息可以被包含在Result命令内的多个Item元素中，或者可以是具有单个项目的多个Result命令。
