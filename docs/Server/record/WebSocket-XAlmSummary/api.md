---
title: Proxy Service API v1.0
language_tabs:
  - http: HTTP
language_clients:
  - http: ""
toc_footers: []
includes: []
search: true
highlight_theme: darkula
headingLevel: 2

---

<!-- Generator: Widdershins v4.0.1 -->

<h1 id="proxy-service-api">Proxy Service API v1.0</h1>

> Scroll down for code samples, example requests and responses. Select a language for code samples from the tabs above or the mobile navigation menu.

所有 API(登入API除外), 都需要在 Cookie 中帶入 GraphicSession token

Email: <a href="mailto:junyu.ke@advantech.com.tw;ml.wu@advantech.com.tw;jill.li@advantech.com.tw">API Support</a> 

# Authentication

* API Key (GraphicSession)
    - Parameter Name: **graphicSession**, in: cookie. 

<h1 id="proxy-service-api-websocket-xalmsummary">WebSocket-XAlmSummary</h1>

## post__ws_register

> Code samples

```http
POST /ws/register HTTP/1.1

Content-Type: application/json
Accept: application/json

```

`POST /ws/register`

*Register to WA-X alarm summary*

Client sends JSON to register for a project's alarm summary updates.

> Body parameter

```json
{
  "action": "register",
  "projectName": "project_name",
  "request": null,
  "topic": "xalarmsummary"
}
```

<h3 id="post__ws_register-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|body|body|[wsmodel.XAlarmSumaryRegister](#schemawsmodel.xalarmsumaryregister)|true|Register payload|

> Example responses

> 101 Response

```json
{
  "action": "string",
  "error": 0,
  "reason": "string",
  "request": null,
  "topic": "string"
}
```

<h3 id="post__ws_register-responses">Responses</h3>

|Status|Meaning|Description|Schema|
|---|---|---|---|
|101|[Switching Protocols](https://tools.ietf.org/html/rfc7231#section-6.2.2)|Error: 0|[wsmodel.ErrorMessage](#schemawsmodel.errormessage)|
|400|[Bad Request](https://tools.ietf.org/html/rfc7231#section-6.5.1)|Error: < 0, Reason: Error Reason|[wsmodel.ErrorMessage](#schemawsmodel.errormessage)|

<aside class="success">
This operation does not require authentication
</aside>

## post__ws_unregister

> Code samples

```http
POST /ws/unregister HTTP/1.1

Content-Type: application/json
Accept: application/json

```

`POST /ws/unregister`

*Unregister from WA-X alarm summary*

Client sends JSON to unregister from a project's alarm summary updates.

> Body parameter

```json
{
  "action": "register",
  "projectName": "project_name",
  "request": null,
  "topic": "xalarmsummary"
}
```

<h3 id="post__ws_unregister-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|body|body|[wsmodel.XAlarmSumaryRegister](#schemawsmodel.xalarmsumaryregister)|true|Unregister payload|

> Example responses

> 101 Response

```json
{
  "action": "string",
  "error": 0,
  "reason": "string",
  "request": null,
  "topic": "string"
}
```

<h3 id="post__ws_unregister-responses">Responses</h3>

|Status|Meaning|Description|Schema|
|---|---|---|---|
|101|[Switching Protocols](https://tools.ietf.org/html/rfc7231#section-6.2.2)|Error: 0|[wsmodel.ErrorMessage](#schemawsmodel.errormessage)|
|400|[Bad Request](https://tools.ietf.org/html/rfc7231#section-6.5.1)|Error: < 0, Reason: Error Reason|[wsmodel.ErrorMessage](#schemawsmodel.errormessage)|

<aside class="success">
This operation does not require authentication
</aside>

# Schemas

<h2 id="tocS_controller.RecordControlPageReceive">controller.RecordControlPageReceive</h2>
<!-- backwards compatibility -->
<a id="schemacontroller.recordcontrolpagereceive"></a>
<a id="schema_controller.RecordControlPageReceive"></a>
<a id="tocScontroller.recordcontrolpagereceive"></a>
<a id="tocscontroller.recordcontrolpagereceive"></a>

```json
{
  "action": "string",
  "request": {
    "id": 0,
    "page": 0
  },
  "topic": "string"
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|action|string|false|none|每個 handler 下可以有多個 action|
|request|[controller.RecordControlPageRequest](#schemacontroller.recordcontrolpagerequest)|false|none|none|
|topic|string|false|none|每個 topic 由不同的 message handler(controller) 處理|

<h2 id="tocS_controller.RecordControlPageRequest">controller.RecordControlPageRequest</h2>
<!-- backwards compatibility -->
<a id="schemacontroller.recordcontrolpagerequest"></a>
<a id="schema_controller.RecordControlPageRequest"></a>
<a id="tocScontroller.recordcontrolpagerequest"></a>
<a id="tocscontroller.recordcontrolpagerequest"></a>

```json
{
  "id": 0,
  "page": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|id|integer|false|none|none|
|page|integer|false|none|none|

<h2 id="tocS_controller.RecordQueryData">controller.RecordQueryData</h2>
<!-- backwards compatibility -->
<a id="schemacontroller.recordquerydata"></a>
<a id="schema_controller.RecordQueryData"></a>
<a id="tocScontroller.recordquerydata"></a>
<a id="tocscontroller.recordquerydata"></a>

```json
{
  "id": 0,
  "param": {},
  "version": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|id|integer|false|none|none|
|param|object|false|none|none|
|version|integer|false|none|none|

<h2 id="tocS_controller.RecordRegisterReceive">controller.RecordRegisterReceive</h2>
<!-- backwards compatibility -->
<a id="schemacontroller.recordregisterreceive"></a>
<a id="schema_controller.RecordRegisterReceive"></a>
<a id="tocScontroller.recordregisterreceive"></a>
<a id="tocscontroller.recordregisterreceive"></a>

```json
{
  "action": "string",
  "request": {
    "id": 0,
    "param": {},
    "version": 0
  },
  "topic": "string"
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|action|string|false|none|每個 handler 下可以有多個 action|
|request|[controller.RecordRegisterRequest](#schemacontroller.recordregisterrequest)|false|none|none|
|topic|string|false|none|每個 topic 由不同的 message handler(controller) 處理|

<h2 id="tocS_controller.RecordRegisterRequest">controller.RecordRegisterRequest</h2>
<!-- backwards compatibility -->
<a id="schemacontroller.recordregisterrequest"></a>
<a id="schema_controller.RecordRegisterRequest"></a>
<a id="tocScontroller.recordregisterrequest"></a>
<a id="tocscontroller.recordregisterrequest"></a>

```json
{
  "id": 0,
  "param": {},
  "version": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|id|integer|false|none|none|
|param|object|false|none|none|
|version|integer|false|none|none|

<h2 id="tocS_model.BaseResponse-array_service_DatabaseTable">model.BaseResponse-array_service_DatabaseTable</h2>
<!-- backwards compatibility -->
<a id="schemamodel.baseresponse-array_service_databasetable"></a>
<a id="schema_model.BaseResponse-array_service_DatabaseTable"></a>
<a id="tocSmodel.baseresponse-array_service_databasetable"></a>
<a id="tocsmodel.baseresponse-array_service_databasetable"></a>

```json
{
  "error": 0,
  "reason": "string",
  "result": [
    {
      "columns": [
        {
          "hasTimeZone": true,
          "name": "string",
          "originalType": "string",
          "type": "string"
        }
      ],
      "name": "string"
    }
  ]
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|error|integer|false|none|none|
|reason|string|false|none|none|
|result|[[service.DatabaseTable](#schemaservice.databasetable)]|false|none|none|

<h2 id="tocS_model.BaseResponse-bool">model.BaseResponse-bool</h2>
<!-- backwards compatibility -->
<a id="schemamodel.baseresponse-bool"></a>
<a id="schema_model.BaseResponse-bool"></a>
<a id="tocSmodel.baseresponse-bool"></a>
<a id="tocsmodel.baseresponse-bool"></a>

```json
{
  "error": 0,
  "reason": "string",
  "result": true
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|error|integer|false|none|none|
|reason|string|false|none|none|
|result|boolean|false|none|none|

<h2 id="tocS_model.BaseResponse-service_DatabaseColumn">model.BaseResponse-service_DatabaseColumn</h2>
<!-- backwards compatibility -->
<a id="schemamodel.baseresponse-service_databasecolumn"></a>
<a id="schema_model.BaseResponse-service_DatabaseColumn"></a>
<a id="tocSmodel.baseresponse-service_databasecolumn"></a>
<a id="tocsmodel.baseresponse-service_databasecolumn"></a>

```json
{
  "error": 0,
  "reason": "string",
  "result": {
    "hasTimeZone": true,
    "name": "string",
    "originalType": "string",
    "type": "string"
  }
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|error|integer|false|none|none|
|reason|string|false|none|none|
|result|[service.DatabaseColumn](#schemaservice.databasecolumn)|false|none|none|

<h2 id="tocS_model.BaseResponse-string">model.BaseResponse-string</h2>
<!-- backwards compatibility -->
<a id="schemamodel.baseresponse-string"></a>
<a id="schema_model.BaseResponse-string"></a>
<a id="tocSmodel.baseresponse-string"></a>
<a id="tocsmodel.baseresponse-string"></a>

```json
{
  "error": 0,
  "reason": "string",
  "result": "string"
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|error|integer|false|none|none|
|reason|string|false|none|none|
|result|string|false|none|none|

<h2 id="tocS_model.BaseResponseLoose">model.BaseResponseLoose</h2>
<!-- backwards compatibility -->
<a id="schemamodel.baseresponseloose"></a>
<a id="schema_model.BaseResponseLoose"></a>
<a id="tocSmodel.baseresponseloose"></a>
<a id="tocsmodel.baseresponseloose"></a>

```json
{
  "error": 0,
  "reason": "string",
  "result": null
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|error|integer|false|none|none|
|reason|string|false|none|none|
|result|any|false|none|none|

<h2 id="tocS_model.RecordStruct">model.RecordStruct</h2>
<!-- backwards compatibility -->
<a id="schemamodel.recordstruct"></a>
<a id="schema_model.RecordStruct"></a>
<a id="tocSmodel.recordstruct"></a>
<a id="tocsmodel.recordstruct"></a>

```json
{
  "enable": true,
  "funcname": "string",
  "id": 0,
  "info": {},
  "name": "string",
  "projid": 0,
  "source": "string",
  "type": "string",
  "version": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|enable|boolean|false|none|none|
|funcname|string|false|none|none|
|id|integer|false|none|none|
|info|object|false|none|none|
|name|string|false|none|none|
|projid|integer|false|none|none|
|source|string|false|none|none|
|type|string|false|none|none|
|version|integer|false|none|none|

<h2 id="tocS_service.DatabaseColumn">service.DatabaseColumn</h2>
<!-- backwards compatibility -->
<a id="schemaservice.databasecolumn"></a>
<a id="schema_service.DatabaseColumn"></a>
<a id="tocSservice.databasecolumn"></a>
<a id="tocsservice.databasecolumn"></a>

```json
{
  "hasTimeZone": true,
  "name": "string",
  "originalType": "string",
  "type": "string"
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|hasTimeZone|boolean|false|none|none|
|name|string|false|none|none|
|originalType|string|false|none|none|
|type|string|false|none|none|

<h2 id="tocS_service.DatabaseTable">service.DatabaseTable</h2>
<!-- backwards compatibility -->
<a id="schemaservice.databasetable"></a>
<a id="schema_service.DatabaseTable"></a>
<a id="tocSservice.databasetable"></a>
<a id="tocsservice.databasetable"></a>

```json
{
  "columns": [
    {
      "hasTimeZone": true,
      "name": "string",
      "originalType": "string",
      "type": "string"
    }
  ],
  "name": "string"
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|columns|[[service.DatabaseColumn](#schemaservice.databasecolumn)]|false|none|none|
|name|string|false|none|none|

<h2 id="tocS_wsmodel.EHAlarmSumaryRegister">wsmodel.EHAlarmSumaryRegister</h2>
<!-- backwards compatibility -->
<a id="schemawsmodel.ehalarmsumaryregister"></a>
<a id="schema_wsmodel.EHAlarmSumaryRegister"></a>
<a id="tocSwsmodel.ehalarmsumaryregister"></a>
<a id="tocswsmodel.ehalarmsumaryregister"></a>

```json
{
  "action": "register",
  "groupid": 0,
  "request": null,
  "tenantid": "string",
  "topic": "ehalarmsummary"
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|action|string|false|none|none|
|groupid|integer|false|none|none|
|request|any|false|none|none|
|tenantid|string|false|none|none|
|topic|string|false|none|none|

#### Enumerated Values

|Property|Value|
|---|---|
|action|register|
|action|unregister|
|topic|ehalarmsummary|

<h2 id="tocS_wsmodel.ErrorMessage">wsmodel.ErrorMessage</h2>
<!-- backwards compatibility -->
<a id="schemawsmodel.errormessage"></a>
<a id="schema_wsmodel.ErrorMessage"></a>
<a id="tocSwsmodel.errormessage"></a>
<a id="tocswsmodel.errormessage"></a>

```json
{
  "action": "string",
  "error": 0,
  "reason": "string",
  "request": null,
  "topic": "string"
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|action|string|false|none|每個 handler 下可以有多個 action|
|error|integer|false|none|error = 0 Success, otherwise failed|
|reason|string|false|none|none|
|request|any|false|none|none|
|topic|string|false|none|每個 topic 由不同的 message handler(controller) 處理|

<h2 id="tocS_wsmodel.Login">wsmodel.Login</h2>
<!-- backwards compatibility -->
<a id="schemawsmodel.login"></a>
<a id="schema_wsmodel.Login"></a>
<a id="tocSwsmodel.login"></a>
<a id="tocswsmodel.login"></a>

```json
{
  "action": "login",
  "csrfToken": "string",
  "request": null,
  "topic": "login"
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|action|string|false|none|none|
|csrfToken|string|false|none|none|
|request|any|false|none|none|
|topic|string|false|none|none|

#### Enumerated Values

|Property|Value|
|---|---|
|action|login|
|topic|login|

<h2 id="tocS_wsmodel.TopicMessage">wsmodel.TopicMessage</h2>
<!-- backwards compatibility -->
<a id="schemawsmodel.topicmessage"></a>
<a id="schema_wsmodel.TopicMessage"></a>
<a id="tocSwsmodel.topicmessage"></a>
<a id="tocswsmodel.topicmessage"></a>

```json
{
  "action": "string",
  "request": null,
  "topic": "string"
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|action|string|false|none|每個 handler 下可以有多個 action|
|request|any|false|none|none|
|topic|string|false|none|每個 topic 由不同的 message handler(controller) 處理|

<h2 id="tocS_wsmodel.WAAlarmSummaryRegister">wsmodel.WAAlarmSummaryRegister</h2>
<!-- backwards compatibility -->
<a id="schemawsmodel.waalarmsummaryregister"></a>
<a id="schema_wsmodel.WAAlarmSummaryRegister"></a>
<a id="tocSwsmodel.waalarmsummaryregister"></a>
<a id="tocswsmodel.waalarmsummaryregister"></a>

```json
{
  "action": "register",
  "projectName": "project_name",
  "request": null,
  "topic": "waalarmsummary"
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|action|string|false|none|none|
|projectName|string|false|none|none|
|request|any|false|none|none|
|topic|string|false|none|none|

#### Enumerated Values

|Property|Value|
|---|---|
|action|register|
|action|unregister|
|topic|waalarmsummary|

<h2 id="tocS_wsmodel.XAlarmSumaryRegister">wsmodel.XAlarmSumaryRegister</h2>
<!-- backwards compatibility -->
<a id="schemawsmodel.xalarmsumaryregister"></a>
<a id="schema_wsmodel.XAlarmSumaryRegister"></a>
<a id="tocSwsmodel.xalarmsumaryregister"></a>
<a id="tocswsmodel.xalarmsumaryregister"></a>

```json
{
  "action": "register",
  "projectName": "project_name",
  "request": null,
  "topic": "xalarmsummary"
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|action|string|false|none|none|
|projectName|string|false|none|none|
|request|any|false|none|none|
|topic|string|false|none|none|

#### Enumerated Values

|Property|Value|
|---|---|
|action|register|
|action|unregister|
|topic|xalarmsummary|

