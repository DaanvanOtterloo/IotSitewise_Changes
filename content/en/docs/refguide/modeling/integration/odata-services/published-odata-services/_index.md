---
title: "Published OData Services"
url: /refguide/published-odata-services/
weight: 10
tags: ["studio pro","OData","publish"]
#If moving or renaming this doc file, implement a temporary redirect and let the respective team know they should update the URL in the product. See Mapping to Products for more details.
---

## 1 Introduction

In Studio Pro, entities can be exposed as [OData resources](/refguide/published-odata-resource/) by adding them to a published OData service. You can expose any number of related resources in a published OData service. By default, the plural of the non-qualified names of entities are used in the URI to uniquely identify them, but you can override the name of the resource as well. 

A published OData service is a REST service with an OpenAPI contract, which means that OpenAPI compatible REST clients can easily interact with it. 

The standard used for OData in Mendix is [OData v4](http://www.odata.org/documentation), which returns data in JSON format.

⚠ The option to publish [OData v3](http://www.odata.org/documentation/odata-version-3-0) services, which return data in Atom XML format, is deprecated and will be removed in a future version.

Not all parts of the standard are implemented. If something is not documented here, it is has not yet been added.

This document describes the options available to you when you create a published OData service, and ends with some runtime considerations.

{{% alert color="info" %}}
Published OData services deployed to the [Mendix Cloud](/developerportal/deploy/mendix-cloud-deploy/) are automatically registered in the [Catalog](/catalog/).{{% /alert %}}

## 2 General

To create a Published OData Service, right-click on the module in your app and choose **Add other** > **Published OData service**. You can also edit an existing published OData service by double-clicking on it, or right-clicking on it and selecting **Open**.

### 2.1 Service Name

The service name uniquely identifies the published OData service within the app.

### 2.2 Version

Use the **Version** field to assign a version number to the service. This number will be shown in the API documentation.

{{% alert color="info" %}}
It is recommended to use [semantic versioning](https://semver.org/) for services that you publish.
{{% /alert %}}

{{% alert color="warning" %}}
Once a version is released to production, any further changes should be made to a new version of the service.

This is because changes to a particular version of a published OData service will be reflected in the entities and attributes available through the catalog for every environment for which the service is published. For example, if you have version 1.0.0 published to both non-production and production environments, any changes you make to version 1.0.0 of the service in the non-production environment will also be reflected in the service in production.  
{{% /alert %}}

### 2.3 Location

The location denotes where the service will be available. The part before `/odata/` may be different depending on where the app is running. You can specify the part after `/odata/` yourself. It is recommended to specify the service name and the major version in the location.

### 2.4 Namespace

In OData, the namespace is used to refer to data types. You can customize this namespace, changing it to any value which starts with a letter followed by letters, digits, or dots with a maximum length of 512 characters.

### 2.5 Entities

This list gives an overview of all entities published as [OData resources](/refguide/published-odata-resource/).

#### 2.5.1 Entity Details

This list gives an overview of all published attributes and associations.

### 2.6 Enumerations

This list gives an overview of all [enumerations](/refguide/enumerations/) that are published by the service (for OData v4 only). When a published entity has an attribute with an enumeration type then the enumeration appears in this list. The list does not appear when there are no published enumerations. There is no need to add enumerations yourself, because Studio Pro will add them when needed.

Click **Edit** to change the exposed name of the enumeration (the name as it appears to clients of the service) and to provide documentation.

#### 2.6.1 Enumeration Details

This list gives an overview of the values of the published enumeration.

Click **Edit** to change the exposed name of the enumeration value (the name as it appears to clients of the service) and to provide documentation.

Use the **Refresh** button when the enumeration values have changed to update the list with the new values.

### 2.7 Microflows

This list gives an overview of all microflows published as [OData actions](/refguide/published-odata-microflow/).

### 2.8 Parameters

This list gives an overview of the [parameters](/refguide/published-odata-microflow/#pub-odata-mflow-parameters) of a selected microflow.

## 3 Settings

### 3.1 Configuration

#### 3.1.1 OData Version

You can choose between OData 4 (recommended) and OData 3. One of the main differences is that OData 4 services return results in JSON, and OData 3 services return results in XML.

#### 3.1.2 Associations

You can select how you want to represent associations. For more information, see the [Associations](/refguide/odata-representation/#associations) section of *OData Representation*.

### 3.2 Export

In this section, you can save the different representations of the service to file.

#### 3.2.1 Service feed

The service feed, available in XML and JSON, contains a list of the published entities.

#### 3.2.2 Metadata

The $metadata XML file contains the service's contract in OData's [CSDL](https://docs.oasis-open.org/odata/odata-csdl-xml/v4.01/odata-csdl-xml-v4.01.html) format.

#### 3.2.3 OpenAPI {#openapi}

The OpenAPI JSON file contains the service's REST contract in [OpenAPI 3.0](https://www.openapis.org/) format.

### 3.3 Security {#security}

You can configure security for the OData service when [App Security](/refguide/app-security/) is enabled.

#### 3.3.1 Requires Authentication {#authentication}

Select whether clients need to authenticate or not. Select **No** to allow access to the resources without restrictions. Select **Yes** to be able to select which authentication methods to support.

Even when you choose **Yes**, you can still expose OData resources to anonymous users. For detailed information on allowing anonymous users, see [Anonymous User Role](/refguide/anonymous-users/).

{{% alert color="info" %}}
The **Authentication** section of a published OData service is only visible when you have enabled [app security](/refguide/app-security/).
{{% /alert %}}

#### 3.3.2 Authentication Methods {#authentication-methods}

If authentication is required, you can select which authentication methods you would like to support.

* Select **Username and password** to allow clients to authenticate themselves using a username and a password in the **Authorization** header (this is called "basic authentication")
* Select **Active session** to allow access from JavaScript inside your current application
* Select **Custom** to authenticate using a microflow (this microflow is called every time a user wants to access a resource)

Check more than one authentication method to have the service try each of them. It will first try **Custom** authentication, then **Username and password**, and then **Active session**.

##### 3.3.2.1 Username and Password {#username-password}

Authentication can be done by including basic authentication in the HTTP header of the call. To do this you need to construct a header called **Authorization** and its content should be constructed as follows:

1. Username and password are combined into a string "username:password".
2. The resulting string is then encoded using the [RFC2045-MIME](https://tools.ietf.org/html/rfc2045) variant of Base64 (except not limited to 76 char/line).
3. The authorization method and a single space (meaning, "Basic " is then put before the encoded string).

This result is a header which looks like `Authorization: Basic QWxhZGRpbjpvcGVuIHNlc2FtZQ==`.

##### 3.3.2.2 Active Session {#authentication-active-session}

When you check this authentication method, the JavaScript in your app can access the REST service using the current user's session.

To prevent cross-site request forgery, the `X-Csrf-Token` header needs to be set on each request, for example:

```js
var xmlHttp = new XMLHttpRequest();
xmlHttp.open("GET", "http://mysite/odata/myservice/myresource", false);
xmlHttp.setRequestHeader("X-Csrf-Token", mx.session.getConfig("csrftoken"));
xmlHttp.send(null);
```

##### 3.3.2.3 Custom {#authentication-microflow}

Specify which microflow to use for custom authentication.

The microflow may take an [HttpRequest](/refguide/http-request-and-response-entities/#http-request) as a parameter, so it can inspect the incoming request.

The microflow may also take an [HttpResponse](/refguide/http-request-and-response-entities/#http-response) as a parameter. When the microflow sets the status code of this response to something other then **200**, this value is returned and the operation will not be executed. Any headers set on the response are returned (except when the microflow returns an empty user).

The authentication microflow should return a User.

There are three possible outcomes of the authentication microflow:

* When the status code of the HttpResponse parameter is set to something other then **200**, this value is returned and the operation will not be executed
* When the resulting User is not empty, the operation is executed in the context of that user
* When the resulting User is empty, the next authentication method is attempted (when there are no other authentication methods, the result is **404 Not Found**)

###### 3.3.2.3.1 Mendix SSO {#authentication-mendix-sso}

You can configure a published OData service to authenticate with the [Mendix SSO](/appstore/modules/mendix-sso/) module. This is a form of [Custom](#authentication-microflow) authentication.

To set up authentication with Mendix SSO, do the following:

1. Ensure that the [Mendix SSO](/appstore/modules/mendix-sso/) module has been installed and configured in your app.
2. In the published OData service, choose **Custom** authentication and select the **AuthorizeRequestWithAccessTokenFrom Request** microflow.

#### 3.3.3 Allowed Roles

The allowed roles define which [module role](/refguide/module-security/#module-role) a user must have to be able to access the service. This option is only available when **Requires authentication** is set to **Yes**.

{{% alert color="warning" %}}
Web service users cannot access OData services.
{{% /alert %}}

## 4 Public Documentation

In the **Public documentation** tab, you can write a summary and a description intended for people using the service.

## 5 Properties

In the properties pane when an OData service document is displayed, you can edit some of the values that you can also set in the **General** tab, such as **Service name**, **Version**, and **Namespace**.

This section describes the additional values that you can set.

### 5.1 Documentation

Here you can add a description of the service. This is available to other users working on this app and is not published when the OData service is deployed.

### 5.2 Replace Illegal XML Characters

Some special characters cannot be used in XML. If your data contains these
characters, the client will get an error. If you set this setting to *Yes*,
those illegal characters are replaced by the DEL character, and the client will
not get an error. However, the data that the client receives will not be exactly
what is stored in your database, because these characters have been replaced.

This *Replace Illegal XML Characters* option is not available when the OData
version is OData 4, because OData 4 returns data in JSON format.

Default value: *No*

## 6 Runtime Considerations

### 6.1 General

Once your app is published, a list of the published OData services will be available on the root URL of the app followed by `/odata-doc/`. For example, `http://localhost:8080/odata-doc/`. For each OData 4 service, there is a link to a Swagger UI page that shows an interactive documentation page on which users can interact with the service.

{{% alert color="warning" %}}
While the API documentation for OData resources is enabled by default, access to it may be restricted by the administrator for apps running in production.
{{% /alert %}}

For details on how to filter the OData response, refer to [OData Query Options](/refguide/odata-query-options/).

For details on how Mendix attributes are represented in OData, refer to [OData Representation](/refguide/odata-representation/).

When exposing entities through OData, the entities are retrieved from the Mendix database in a streaming fashion, to avoid out-of-memory errors in the Mendix Runtime.

### 6.2 On-Premises Deployments

Some on-premises servers, in particular those using Microsoft IIS, will strip the host header from requests. This means that your OData service and documentation will be published on an unexpected URL.

To resolve this issue, you will need to ensure your server preserves host headers. See the section [Preserving the Host Header](/developerportal/deploy/deploy-mendix-on-microsoft-windows/#preserve-header) in the *Microsoft Windows* deployment documentation.

## 7 Runtime Status Codes {#status-codes}

The Mendix runtime returns status codes for OData payloads. The possible status codes are the following:

* `200`, `201`, `204` – [Successful responses](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status#successful_responses)
* `401`, `402`, `403`, `404`, `405`, `422` – [Client error responses](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status#client_error_responses)
* `500` – Mendix default when something goes wrong and it has not been modelled; may or may not be the standard [internal server error](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/500)

## 8 Publishing OData Services

To publish an OData resource with full CRUD (Create, Read, Update, or Delete functionality, or in Studio Pro, **Insertable**, **Readable**, **Updateable**, and **Deletable**), select the relevant checkboxes in the [Capabilities](/refguide/published-odata-resource/#capabilities) section in the [Published OData Resource](/refguide/published-odata-resource/). You can then [Send](/refguide/send-external-object/) and [Delete](/refguide/delete-external-object/) these resources using [External Object activities](/refguide/external-object-activities/). 

## 9 Limitations

Studio Pro currently does not support publishing media entities with OData services. To learn about consuming media entities with OData, see the [Binary Attributes](/refguide/consumed-odata-service-requirements/#binary-attributes) section of *Consumed OData Service Requirements*. You can also [Publish and Retrieve Images and Files with REST](/refguide/send-receive-files-rest/).
