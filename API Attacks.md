Navigation: [[Pentesting]]

---
# Table of Contents
1. API Types
2. OWASP Top 10 API Attacks
# API Types
APIs can be build in various styles, each with their own use case:

| Style                                                                                               | Description                                                                                                                                                                                                                                                              |
| --------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| [Representational State Transfer](https://ics.uci.edu/~fielding/pubs/dissertation/top.htm) (`REST`) | Most popular style, uses `client-server` model where client sends request using HTTP methods. All of the requests are stateless, with responses typically sent back in JSON or XML.                                                                                      |
| [Simple Object Access Protocol](https://www.w3.org/TR/2000/NOTE-SOAP-20000508/) (`SOAP`)            | Uses XML for message exchange. Highly standardized and offers many features for security, transactions and error handling. Much more complex to implement and use than REST APIs.                                                                                        |
| [GraphQL](https://graphql.org/)                                                                     | Alternative style that's flexible and efficient for fetching and updating data. Instead of a fixed set of fields, it allows clients to specify what data they need. This API uses a single endpoint and a strongly typed query language to retrieve data.                |
| [gRPC](https://grpc.io/)                                                                            | Newer style that uses [Protocol Buffers](https://protobuf.dev/) for message serialization, providing a high performance and efficient way to communicate between systems. Developed using a variety of languages, very useful for microservices and distributed systems. |
# OWASP Top 10 API Attacks
|**Risk**|**Description**|
|---|---|
|[API1:2023 - Broken Object Level Authorization](https://owasp.org/API-Security/editions/2023/en/0xa1-broken-object-level-authorization/)|The API allows authenticated users to access data they are not authorized to view.|
|[API2:2023 - Broken Authentication](https://owasp.org/API-Security/editions/2023/en/0xa2-broken-authentication/)|The authentication mechanisms of the API can be bypassed or circumvented, allowing unauthorized access.|
|[API3:2023 - Broken Object Property Level Authorization](https://owasp.org/API-Security/editions/2023/en/0xa3-broken-object-property-level-authorization/)|The API reveals sensitive data to authorized users that they should not access or permits them to manipulate sensitive properties.|
|[API4:2023 - Unrestricted Resource Consumption](https://owasp.org/API-Security/editions/2023/en/0xa4-unrestricted-resource-consumption/)|The API does not limit the amount of resources users can consume.|
|[API5:2023 - Broken Function Level Authorization](https://owasp.org/API-Security/editions/2023/en/0xa5-broken-function-level-authorization/)|The API allows unauthorized users to perform authorized operations.|
|[API6:2023 - Unrestricted Access to Sensitive Business Flows](https://owasp.org/API-Security/editions/2023/en/0xa6-unrestricted-access-to-sensitive-business-flows/)|The API exposes sensitive business flows, leading to potential financial losses and other damages.|
|[API7:2023 - Server Side Request Forgery](https://owasp.org/API-Security/editions/2023/en/0xa7-server-side-request-forgery/)|The API does not validate requests adequately, allowing attackers to send malicious requests and interact with internal resources.|
|[API8:2023 - Security Misconfiguration](https://owasp.org/API-Security/editions/2023/en/0xa8-security-misconfiguration/)|The API suffers from security misconfigurations, including vulnerabilities that lead to Injection Attacks.|
|[API9:2023 - Improper Inventory Management](https://owasp.org/API-Security/editions/2023/en/0xa9-improper-inventory-management/)|The API does not properly and securely manage version inventory.|
|[API10:2023 - Unsafe Consumption of APIs](https://owasp.org/API-Security/editions/2023/en/0xaa-unsafe-consumption-of-apis/)|The API consumes another API unsafely, leading to potential security risks.|



---
Navigation: [[Pentesting]]