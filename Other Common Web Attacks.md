Navigation: [[Pentesting]]

---
# Table of Contents
1. HTTP Verb Tampering
2. Insecure Direct Object References
3. XML External Entity Injection
# HTTP Verb Tampering
This attack works by attacking the HTTP method used for requests. Other than `GET` and `POST`, here are the most commonly used verbs:

| Method    | Description                                                                                         |
| --------- | --------------------------------------------------------------------------------------------------- |
| `HEAD`    | Identical to a GET request, but its response only contains the `headers`, without the response body |
| `PUT`     | Writes the request payload to the specified location                                                |
| `DELETE`  | Deletes the resource at the specified location                                                      |
| `OPTIONS` | Shows different options accepted by a web server, like accepted HTTP verbs                          |
| `PATCH`   | Apply partial modifications to the resource at the specified location                               |

**Insecure Configuration**
Insecure configurations cause the first type of HTTP verb tampering vulnerabilities. When an admin doesn't take into account other methods, we can use these methods to bypass authentication.

An example of insecure configuration would be:
```xml
<Limit GET POST>
    Require valid-user
</Limit>
```

**Insecure Coding**
Insecure coding causes the other type of verb tampering vulnerability. Usually a regex is used to filter requests. We can bypass these filters to bypass authentication. Here's an example of an improper filter, where the developer forgot to filter all requests:
```php
$pattern = "/^[A-Za-z\s]+$/";

if(preg_match($pattern, $_GET["code"])) {
    $query = "Select * from ports where port_code like '%" . $_REQUEST["code"] . "%'";
    ...SNIP...
}
```

**Verb Tampering Attacks**
To bypass insecure configurations and coding, we can simply change the request method in burp. For example, the `HEAD` method is identical to `GET` but doesn't return a body in the HTTP response. If this request is successful, an action may be performed even if we don't get any output.
# Insecure Direct Object Reference
One of the most common web vulnerabilities, it happens a web app exposes a direct reference to an object, such as a file or database resource. The user is able to directly control this object to access other similar items.

Since building a solid access control is challenging, IDOR vulnerabilities are everywhere. For example, if a download request is made, the user may get a link like `download.php?file_id=123`. If we change the ID, we may be able to access any file by sending a request with its ID.

Just exposing direct references to internal objects or resources isn't a vulnerability. But it makes it possible to exploit another vulnerability: weak access control systems. Many web apps restrict users from accessing pages, functions, or APIs that can retrieve these resources. If a web app didn't have an access control system on the backend to prevent access, then the user may be able to access them.

**Finding IDOR Vulnerabilities**
First, we'll need to find direct object references. These are mostly found in URL parameters or APIs but may also be found in HTTP headers or cookies. 

Sometimes, Ajax calls are able to be found in the front end code. This is insecure, however, this can be seem from time to time. These are often implemented in a way such that the appropriate functions are only active for each role. However, we can still attempt to make a cross domain request and see if it works.

Here's an example of some broken code:
```javascript
function changeUserPassword() {
    $.ajax({
        url:"change_password.php",
        type: "post",
        dataType: "json",
        data: {uid: user.uid, password: user.password, is_admin: is_admin},
        success:function(result){
            //
        }
    });
}
```

**Enumeration**
The best way we can mass enumerate IDOR exposure is through burp intruder. OWASP ZAP is also another option we can use, although burp tends to be the most simplistic. To enumerate, find the request that's being made and iterate through other potential parameters.
# XML External Entities
XXE Injection happens when XML data is taken from user input and passed to XML without parsing. XML documents are formed of element trees, with each element being a tag, following a root/child structure.

Here are some of the key elements of an XML document:

|Key|Definition|Example|
|---|---|---|
|`Tag`|The keys of an XML document, usually wrapped with (`<`/`>`) characters.|`<date>`|
|`Entity`|XML variables, usually wrapped with (`&`/`;`) characters.|`&lt;`|
|`Element`|The root element or any of its child elements, and its value is stored in between a start-tag and an end-tag.|`<date>01-01-2022</date>`|
|`Attribute`|Optional specifications for any element that are stored in the tags, which may be used by the XML parser.|`version="1.0"`/`encoding="UTF-8"`|
|`Declaration`|Usually the first line of an XML document, and defines the XML version and encoding to use when parsing it.|`<?xml version="1.0" encoding="UTF-8"?>`|

Some characters that are part of the XML document structure, like `<`, `>`, `&`, or `"`, need to be replaced with their entity references: `&lt;`, `&gt;`, `&amp;`, `&quot;`. 

The XML Document Type Definition allows validation of documents against a pre-defined structure. Think of it like a filter for XML files.

We can also define custom entities (the equivalent of variables in XML) in XML DTDs. This can be done with the `ENTITY` key word. Once it's been defined, we can reference it as such: `&entity;`.

For a list of potential payloads, reference [HackTricks](https://book.hacktricks.xyz/pentesting-web/xxe-xee-xml-external-entity)

---
Navigation: [[Pentesting]]