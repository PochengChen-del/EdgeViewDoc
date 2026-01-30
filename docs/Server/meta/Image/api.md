---
title: Metadata Service API v1.0
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

<h1 id="metadata-service-api">Metadata Service API v1.0</h1>

> Scroll down for code samples, example requests and responses. Select a language for code samples from the tabs above or the mobile navigation menu.

所有 API(登入API除外), 都需要在 Cookie 中帶入 SettingSession 或 GraphicSession token

Email: <a href="mailto:junyu.ke@advantech.com.tw;ml.wu@advantech.com.tw;jill.li@advantech.com.tw">API Support</a> 

# Authentication

* API Key (GraphicSession)
    - Parameter Name: **graphicSession**, in: cookie. 

* API Key (SettingSession)
    - Parameter Name: **settingSession**, in: cookie. 

<h1 id="metadata-service-api-image">Image</h1>

## post__api_deleteimage

> Code samples

```http
POST /api/deleteimage HTTP/1.1

Content-Type: application/json
Accept: application/json

```

`POST /api/deleteimage`

*刪除圖片*

根據 ImageIDs 刪除圖片

> Body parameter

```json
{
  "imageids": [
    0
  ]
}
```

<h3 id="post__api_deleteimage-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|body|body|[controller.DeleteImageReq](#schemacontroller.deleteimagereq)|true|要刪除的圖片 ID 列表|

> Example responses

> 200 Response

```json
{
  "error": 0,
  "reason": "string",
  "result": true
}
```

<h3 id="post__api_deleteimage-responses">Responses</h3>

|Status|Meaning|Description|Schema|
|---|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)|成功|[model.BaseResponse-bool](#schemamodel.baseresponse-bool)|
|400|[Bad Request](https://tools.ietf.org/html/rfc7231#section-6.5.1)|參數錯誤|[model.BaseResponse-bool](#schemamodel.baseresponse-bool)|

<aside class="success">
This operation does not require authentication
</aside>

## post__api_getimagelist_{displayid}

> Code samples

```http
POST /api/getimagelist/{displayid} HTTP/1.1

Accept: application/json

```

`POST /api/getimagelist/{displayid}`

*取得顯示器圖片列表*

根據 displayid 取得對應的圖片列表

<h3 id="post__api_getimagelist_{displayid}-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|displayid|path|integer|true|Display ID|

> Example responses

> 200 Response

```json
{
  "error": 0,
  "reason": "string",
  "result": [
    {
      "imageid": 0,
      "name": "string",
      "path": "string",
      "pathid": 0
    }
  ]
}
```

<h3 id="post__api_getimagelist_{displayid}-responses">Responses</h3>

|Status|Meaning|Description|Schema|
|---|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)|成功|[model.BaseResponse-array_controller_ImageListObj](#schemamodel.baseresponse-array_controller_imagelistobj)|
|400|[Bad Request](https://tools.ietf.org/html/rfc7231#section-6.5.1)|參數錯誤|[model.BaseResponse-array_controller_ImageListObj](#schemamodel.baseresponse-array_controller_imagelistobj)|

<aside class="success">
This operation does not require authentication
</aside>

## post__api_getprojectimagelist_{projectid}

> Code samples

```http
POST /api/getprojectimagelist/{projectid} HTTP/1.1

Accept: application/json

```

`POST /api/getprojectimagelist/{projectid}`

*取得專案圖片列表*

根據 projectid 取得對應的圖片列表

<h3 id="post__api_getprojectimagelist_{projectid}-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|projectid|path|integer|true|Project ID|

> Example responses

> 200 Response

```json
{
  "error": 0,
  "reason": "string",
  "result": [
    {
      "imageid": 0,
      "name": "string",
      "path": "string",
      "pathid": 0
    }
  ]
}
```

<h3 id="post__api_getprojectimagelist_{projectid}-responses">Responses</h3>

|Status|Meaning|Description|Schema|
|---|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)|成功|[model.BaseResponse-array_controller_ImageListObj](#schemamodel.baseresponse-array_controller_imagelistobj)|
|400|[Bad Request](https://tools.ietf.org/html/rfc7231#section-6.5.1)|參數錯誤|[model.BaseResponse-bool](#schemamodel.baseresponse-bool)|

<aside class="success">
This operation does not require authentication
</aside>

## post__api_updateimage_{parentpathid}

> Code samples

```http
POST /api/updateimage/{parentpathid} HTTP/1.1

Content-Type: multipart/form-data
Accept: application/json

```

`POST /api/updateimage/{parentpathid}`

*更新圖片*

根據 parentpathid 與圖片 ID 更新圖片檔案

> Body parameter

```yaml
id: 0
files: string
extension: string

```

<h3 id="post__api_updateimage_{parentpathid}-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|parentpathid|path|integer|true|Parent Path ID|
|body|body|object|true|none|
|» id|body|integer|true|Image ID|
|» files|body|string(binary)|true|圖片檔案|
|» extension|body|string|false|檔案副檔名|

> Example responses

> 200 Response

```json
{
  "error": 0,
  "reason": "string",
  "result": {
    "id": 0,
    "name": "string",
    "pathid": 0
  }
}
```

<h3 id="post__api_updateimage_{parentpathid}-responses">Responses</h3>

|Status|Meaning|Description|Schema|
|---|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)|成功|[model.BaseResponse-model_UploadImageRes](#schemamodel.baseresponse-model_uploadimageres)|
|400|[Bad Request](https://tools.ietf.org/html/rfc7231#section-6.5.1)|參數錯誤|[model.BaseResponse-bool](#schemamodel.baseresponse-bool)|

<aside class="success">
This operation does not require authentication
</aside>

## post__api_uploadimage_{parentpathid}

> Code samples

```http
POST /api/uploadimage/{parentpathid} HTTP/1.1

Content-Type: multipart/form-data
Accept: application/json

```

`POST /api/uploadimage/{parentpathid}`

*上傳圖片*

根據 parentpathid 上傳單張圖片

> Body parameter

```yaml
files: string
extension: string

```

<h3 id="post__api_uploadimage_{parentpathid}-parameters">Parameters</h3>

|Name|In|Type|Required|Description|
|---|---|---|---|---|
|parentpathid|path|integer|true|Parent Path ID|
|body|body|object|true|none|
|» files|body|string(binary)|true|圖片檔案|
|» extension|body|string|false|檔案副檔名|

> Example responses

> 200 Response

```json
{
  "error": 0,
  "reason": "string",
  "result": {
    "id": 0,
    "name": "string",
    "pathid": 0
  }
}
```

<h3 id="post__api_uploadimage_{parentpathid}-responses">Responses</h3>

|Status|Meaning|Description|Schema|
|---|---|---|---|
|200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)|成功|[model.BaseResponse-model_UploadImageRes](#schemamodel.baseresponse-model_uploadimageres)|
|400|[Bad Request](https://tools.ietf.org/html/rfc7231#section-6.5.1)|參數錯誤|[model.BaseResponse-model_UploadImageRes](#schemamodel.baseresponse-model_uploadimageres)|

<aside class="success">
This operation does not require authentication
</aside>

# Schemas

<h2 id="tocS_WxMetaService_model_device.ProjectDetail">WxMetaService_model_device.ProjectDetail</h2>
<!-- backwards compatibility -->
<a id="schemawxmetaservice_model_device.projectdetail"></a>
<a id="schema_WxMetaService_model_device.ProjectDetail"></a>
<a id="tocSwxmetaservice_model_device.projectdetail"></a>
<a id="tocswxmetaservice_model_device.projectdetail"></a>

```json
{
  "asyncdraw": true,
  "decimalSeparatorMode": 0,
  "defaultpage": 0,
  "fullscreen": true,
  "highlightchange": true,
  "initenabled": true,
  "lang": "string",
  "projid": 0,
  "scaletype": 0,
  "showconfirmdialog": true,
  "toolbarvisible": true,
  "useDeviceTypeOnly": true,
  "userhomeenabled": true,
  "viewmode": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|asyncdraw|boolean|false|none|none|
|decimalSeparatorMode|integer|false|none|none|
|defaultpage|integer|false|none|none|
|fullscreen|boolean|false|none|none|
|highlightchange|boolean|false|none|none|
|initenabled|boolean|false|none|none|
|lang|string|false|none|none|
|projid|integer|false|none|none|
|scaletype|integer|false|none|none|
|showconfirmdialog|boolean|false|none|none|
|toolbarvisible|boolean|false|none|none|
|useDeviceTypeOnly|boolean|false|none|none|
|userhomeenabled|boolean|false|none|none|
|viewmode|integer|false|none|none|

<h2 id="tocS_WxMetaService_model_device.User">WxMetaService_model_device.User</h2>
<!-- backwards compatibility -->
<a id="schemawxmetaservice_model_device.user"></a>
<a id="schema_WxMetaService_model_device.User"></a>
<a id="tocSwxmetaservice_model_device.user"></a>
<a id="tocswxmetaservice_model_device.user"></a>

```json
{
  "password": "string",
  "setting": true,
  "username": "string",
  "web": true
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|password|string|false|none|none|
|setting|boolean|false|none|none|
|username|string|false|none|none|
|web|boolean|false|none|is web client?|

<h2 id="tocS_WxMetaService_model_device.UserDetail">WxMetaService_model_device.UserDetail</h2>
<!-- backwards compatibility -->
<a id="schemawxmetaservice_model_device.userdetail"></a>
<a id="schema_WxMetaService_model_device.UserDetail"></a>
<a id="tocSwxmetaservice_model_device.userdetail"></a>
<a id="tocswxmetaservice_model_device.userdetail"></a>

```json
{
  "access": [
    {
      "ack": true,
      "change": true,
      "displayid": 0
    }
  ],
  "ack": true,
  "area": [
    0
  ],
  "change": true,
  "home": 0,
  "name": "string",
  "password": "string",
  "projid": 0,
  "sourceuserid": "string",
  "thirdsource": true,
  "usertype": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|access|[[model.UserAccess](#schemamodel.useraccess)]|false|none|none|
|ack|boolean|false|none|none|
|area|[integer]|false|none|none|
|change|boolean|false|none|none|
|home|integer|false|none|none|
|name|string|false|none|none|
|password|string|false|none|none|
|projid|integer|false|none|none|
|sourceuserid|string|false|none|none|
|thirdsource|boolean|false|none|don't update back to source|
|usertype|integer|false|none|none|

<h2 id="tocS_WxMetaService_model_opcua.ProjectDetail">WxMetaService_model_opcua.ProjectDetail</h2>
<!-- backwards compatibility -->
<a id="schemawxmetaservice_model_opcua.projectdetail"></a>
<a id="schema_WxMetaService_model_opcua.ProjectDetail"></a>
<a id="tocSwxmetaservice_model_opcua.projectdetail"></a>
<a id="tocswxmetaservice_model_opcua.projectdetail"></a>

```json
{
  "createtimestamp": "string",
  "description": "string",
  "id": 0,
  "modifytimestamp": "string",
  "name": "string",
  "tagconfig": [
    {
      "displaydec": 0,
      "displayint": 0,
      "projid": 0,
      "spanhi": 0,
      "spanlo": 0,
      "type": "string"
    }
  ]
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|createtimestamp|string|false|none|none|
|description|string|false|none|none|
|id|integer|false|none|none|
|modifytimestamp|string|false|none|none|
|name|string|false|none|none|
|tagconfig|[[model.TagConfigBody](#schemamodel.tagconfigbody)]|false|none|none|

<h2 id="tocS_WxMetaService_model_opcua.UserDetail">WxMetaService_model_opcua.UserDetail</h2>
<!-- backwards compatibility -->
<a id="schemawxmetaservice_model_opcua.userdetail"></a>
<a id="schema_WxMetaService_model_opcua.UserDetail"></a>
<a id="tocSwxmetaservice_model_opcua.userdetail"></a>
<a id="tocswxmetaservice_model_opcua.userdetail"></a>

```json
{
  "active": true,
  "authorizedProjects": [
    {
      "projid": 0,
      "usertype": "string"
    }
  ],
  "createtime": 0,
  "enable": true,
  "functionpermissions": [
    {
      "edit": true,
      "function": "string",
      "id": 0
    }
  ],
  "id": 0,
  "modifytime": 0,
  "name": "string",
  "password": "string",
  "role": "string"
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|active|boolean|false|none|none|
|authorizedProjects|[[model.UserProj](#schemamodel.userproj)]|false|none|none|
|createtime|integer|false|none|none|
|enable|boolean|false|none|none|
|functionpermissions|[[model.UserFunc](#schemamodel.userfunc)]|false|none|none|
|id|integer|false|none|none|
|modifytime|integer|false|none|none|
|name|string|false|none|none|
|password|string|false|none|none|
|role|string|false|none|none|

<h2 id="tocS_controller.DeleteImageReq">controller.DeleteImageReq</h2>
<!-- backwards compatibility -->
<a id="schemacontroller.deleteimagereq"></a>
<a id="schema_controller.DeleteImageReq"></a>
<a id="tocScontroller.deleteimagereq"></a>
<a id="tocscontroller.deleteimagereq"></a>

```json
{
  "imageids": [
    0
  ]
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|imageids|[integer]|false|none|none|

<h2 id="tocS_controller.ImageListObj">controller.ImageListObj</h2>
<!-- backwards compatibility -->
<a id="schemacontroller.imagelistobj"></a>
<a id="schema_controller.ImageListObj"></a>
<a id="tocScontroller.imagelistobj"></a>
<a id="tocscontroller.imagelistobj"></a>

```json
{
  "imageid": 0,
  "name": "string",
  "path": "string",
  "pathid": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|imageid|integer|false|none|none|
|name|string|false|none|none|
|path|string|false|none|none|
|pathid|integer|false|none|none|

<h2 id="tocS_controller.Pong">controller.Pong</h2>
<!-- backwards compatibility -->
<a id="schemacontroller.pong"></a>
<a id="schema_controller.Pong"></a>
<a id="tocScontroller.pong"></a>
<a id="tocscontroller.pong"></a>

```json
{
  "message": "string"
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|message|string|false|none|none|

<h2 id="tocS_controller.RecordSearchQuery">controller.RecordSearchQuery</h2>
<!-- backwards compatibility -->
<a id="schemacontroller.recordsearchquery"></a>
<a id="schema_controller.RecordSearchQuery"></a>
<a id="tocScontroller.recordsearchquery"></a>
<a id="tocscontroller.recordsearchquery"></a>

```json
{
  "froms": [
    {}
  ],
  "name": "string",
  "page": 0,
  "rows": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|froms|[object]|false|none|none|
|name|string|false|none|none|
|page|integer|false|none|none|
|rows|integer|false|none|none|

<h2 id="tocS_controller.TemplateDeleteReq">controller.TemplateDeleteReq</h2>
<!-- backwards compatibility -->
<a id="schemacontroller.templatedeletereq"></a>
<a id="schema_controller.TemplateDeleteReq"></a>
<a id="tocScontroller.templatedeletereq"></a>
<a id="tocscontroller.templatedeletereq"></a>

```json
{
  "tmpids": [
    0
  ]
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|tmpids|[integer]|false|none|none|

<h2 id="tocS_map_int_model.DisplayVersionInfo">map_int_model.DisplayVersionInfo</h2>
<!-- backwards compatibility -->
<a id="schemamap_int_model.displayversioninfo"></a>
<a id="schema_map_int_model.DisplayVersionInfo"></a>
<a id="tocSmap_int_model.displayversioninfo"></a>
<a id="tocsmap_int_model.displayversioninfo"></a>

```json
{
  "property1": {
    "id": 0,
    "phoneversion": 0,
    "tabletversion": 0,
    "version": 0
  },
  "property2": {
    "id": 0,
    "phoneversion": 0,
    "tabletversion": 0,
    "version": 0
  }
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|**additionalProperties**|[model.DisplayVersionInfo](#schemamodel.displayversioninfo)|false|none|none|

<h2 id="tocS_model.AddFolderReq">model.AddFolderReq</h2>
<!-- backwards compatibility -->
<a id="schemamodel.addfolderreq"></a>
<a id="schema_model.AddFolderReq"></a>
<a id="tocSmodel.addfolderreq"></a>
<a id="tocsmodel.addfolderreq"></a>

```json
{
  "foldername": "string",
  "parentpathid": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|foldername|string|false|none|none|
|parentpathid|integer|false|none|none|

<h2 id="tocS_model.AddFolderRes">model.AddFolderRes</h2>
<!-- backwards compatibility -->
<a id="schemamodel.addfolderres"></a>
<a id="schema_model.AddFolderRes"></a>
<a id="tocSmodel.addfolderres"></a>
<a id="tocsmodel.addfolderres"></a>

```json
{
  "pathid": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|pathid|integer|false|none|none|

<h2 id="tocS_model.AlarmAllInfo">model.AlarmAllInfo</h2>
<!-- backwards compatibility -->
<a id="schemamodel.alarmallinfo"></a>
<a id="schema_model.AlarmAllInfo"></a>
<a id="tocSmodel.alarmallinfo"></a>
<a id="tocsmodel.alarmallinfo"></a>

```json
{
  "area": [
    {
      "areaaudio": true,
      "audioid": 0,
      "audioname": "string",
      "audiopath": "string",
      "audiopathtext": "string",
      "description": "string",
      "name": "string",
      "projid": 0,
      "tags": [
        {
          "audioid": 0,
          "audioname": "string",
          "audiopath": "string",
          "audiopathtext": "string",
          "tagname": "string"
        }
      ]
    }
  ],
  "info": {
    "multiaudioid": 0,
    "multiaudioname": "string",
    "multiaudiopath": "string",
    "multiaudiopathtext": "string",
    "projid": 0,
    "singleaudioid": 0,
    "singleaudioname": "string",
    "singleaudiopath": "string",
    "singleaudiopathtext": "string"
  }
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|area|[[model.AlarmDetail](#schemamodel.alarmdetail)]|false|none|none|
|info|[model.AlarmStructDetail](#schemamodel.alarmstructdetail)|false|none|none|

<h2 id="tocS_model.AlarmArea">model.AlarmArea</h2>
<!-- backwards compatibility -->
<a id="schemamodel.alarmarea"></a>
<a id="schema_model.AlarmArea"></a>
<a id="tocSmodel.alarmarea"></a>
<a id="tocsmodel.alarmarea"></a>

```json
{
  "areaaudio": true,
  "audioid": 0,
  "description": "string",
  "name": "string",
  "projid": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|areaaudio|boolean|false|none|none|
|audioid|integer|false|none|none|
|description|string|false|none|none|
|name|string|false|none|none|
|projid|integer|false|none|none|

<h2 id="tocS_model.AlarmDelete">model.AlarmDelete</h2>
<!-- backwards compatibility -->
<a id="schemamodel.alarmdelete"></a>
<a id="schema_model.AlarmDelete"></a>
<a id="tocSmodel.alarmdelete"></a>
<a id="tocsmodel.alarmdelete"></a>

```json
{
  "alarmname": [
    "string"
  ],
  "projid": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|alarmname|[string]|false|none|none|
|projid|integer|false|none|none|

<h2 id="tocS_model.AlarmDetail">model.AlarmDetail</h2>
<!-- backwards compatibility -->
<a id="schemamodel.alarmdetail"></a>
<a id="schema_model.AlarmDetail"></a>
<a id="tocSmodel.alarmdetail"></a>
<a id="tocsmodel.alarmdetail"></a>

```json
{
  "areaaudio": true,
  "audioid": 0,
  "audioname": "string",
  "audiopath": "string",
  "audiopathtext": "string",
  "description": "string",
  "name": "string",
  "projid": 0,
  "tags": [
    {
      "audioid": 0,
      "audioname": "string",
      "audiopath": "string",
      "audiopathtext": "string",
      "tagname": "string"
    }
  ]
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|areaaudio|boolean|false|none|none|
|audioid|integer|false|none|none|
|audioname|string|false|none|none|
|audiopath|string|false|none|none|
|audiopathtext|string|false|none|none|
|description|string|false|none|none|
|name|string|false|none|none|
|projid|integer|false|none|none|
|tags|[[model.AlarmTags](#schemamodel.alarmtags)]|false|none|none|

<h2 id="tocS_model.AlarmID">model.AlarmID</h2>
<!-- backwards compatibility -->
<a id="schemamodel.alarmid"></a>
<a id="schema_model.AlarmID"></a>
<a id="tocSmodel.alarmid"></a>
<a id="tocsmodel.alarmid"></a>

```json
{
  "name": "string",
  "projid": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|name|string|false|none|none|
|projid|integer|false|none|none|

<h2 id="tocS_model.AlarmStruct">model.AlarmStruct</h2>
<!-- backwards compatibility -->
<a id="schemamodel.alarmstruct"></a>
<a id="schema_model.AlarmStruct"></a>
<a id="tocSmodel.alarmstruct"></a>
<a id="tocsmodel.alarmstruct"></a>

```json
{
  "multiaudioid": 0,
  "projid": 0,
  "singleaudioid": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|multiaudioid|integer|false|none|none|
|projid|integer|false|none|none|
|singleaudioid|integer|false|none|none|

<h2 id="tocS_model.AlarmStructDetail">model.AlarmStructDetail</h2>
<!-- backwards compatibility -->
<a id="schemamodel.alarmstructdetail"></a>
<a id="schema_model.AlarmStructDetail"></a>
<a id="tocSmodel.alarmstructdetail"></a>
<a id="tocsmodel.alarmstructdetail"></a>

```json
{
  "multiaudioid": 0,
  "multiaudioname": "string",
  "multiaudiopath": "string",
  "multiaudiopathtext": "string",
  "projid": 0,
  "singleaudioid": 0,
  "singleaudioname": "string",
  "singleaudiopath": "string",
  "singleaudiopathtext": "string"
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|multiaudioid|integer|false|none|none|
|multiaudioname|string|false|none|none|
|multiaudiopath|string|false|none|none|
|multiaudiopathtext|string|false|none|none|
|projid|integer|false|none|none|
|singleaudioid|integer|false|none|none|
|singleaudioname|string|false|none|none|
|singleaudiopath|string|false|none|none|
|singleaudiopathtext|string|false|none|none|

<h2 id="tocS_model.AlarmTags">model.AlarmTags</h2>
<!-- backwards compatibility -->
<a id="schemamodel.alarmtags"></a>
<a id="schema_model.AlarmTags"></a>
<a id="tocSmodel.alarmtags"></a>
<a id="tocsmodel.alarmtags"></a>

```json
{
  "audioid": 0,
  "audioname": "string",
  "audiopath": "string",
  "audiopathtext": "string",
  "tagname": "string"
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|audioid|integer|false|none|none|
|audioname|string|false|none|none|
|audiopath|string|false|none|none|
|audiopathtext|string|false|none|none|
|tagname|string|false|none|none|

<h2 id="tocS_model.AlarmUpsertStruct">model.AlarmUpsertStruct</h2>
<!-- backwards compatibility -->
<a id="schemamodel.alarmupsertstruct"></a>
<a id="schema_model.AlarmUpsertStruct"></a>
<a id="tocSmodel.alarmupsertstruct"></a>
<a id="tocsmodel.alarmupsertstruct"></a>

```json
{
  "areaaudio": true,
  "audioid": 0,
  "description": "string",
  "name": "string",
  "oriname": "string",
  "projid": 0,
  "tags": [
    {
      "audioid": 0,
      "tagname": "string"
    }
  ]
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|areaaudio|boolean|false|none|none|
|audioid|integer|false|none|none|
|description|string|false|none|none|
|name|string|false|none|none|
|oriname|string|false|none|none|
|projid|integer|false|none|none|
|tags|[object]|false|none|none|
|» audioid|integer|false|none|none|
|» tagname|string|false|none|none|

<h2 id="tocS_model.AudioListObj">model.AudioListObj</h2>
<!-- backwards compatibility -->
<a id="schemamodel.audiolistobj"></a>
<a id="schema_model.AudioListObj"></a>
<a id="tocSmodel.audiolistobj"></a>
<a id="tocsmodel.audiolistobj"></a>

```json
{
  "audioid": 0,
  "fullpathtext": "string",
  "name": "string",
  "path": "string",
  "pathid": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|audioid|integer|false|none|none|
|fullpathtext|string|false|none|none|
|name|string|false|none|none|
|path|string|false|none|none|
|pathid|integer|false|none|none|

<h2 id="tocS_model.BaseResponse-array_WxMetaService_model_opcua_ProjectDetail">model.BaseResponse-array_WxMetaService_model_opcua_ProjectDetail</h2>
<!-- backwards compatibility -->
<a id="schemamodel.baseresponse-array_wxmetaservice_model_opcua_projectdetail"></a>
<a id="schema_model.BaseResponse-array_WxMetaService_model_opcua_ProjectDetail"></a>
<a id="tocSmodel.baseresponse-array_wxmetaservice_model_opcua_projectdetail"></a>
<a id="tocsmodel.baseresponse-array_wxmetaservice_model_opcua_projectdetail"></a>

```json
{
  "error": 0,
  "reason": "string",
  "result": [
    {
      "createtimestamp": "string",
      "description": "string",
      "id": 0,
      "modifytimestamp": "string",
      "name": "string",
      "tagconfig": [
        {
          "displaydec": 0,
          "displayint": 0,
          "projid": 0,
          "spanhi": 0,
          "spanlo": 0,
          "type": "string"
        }
      ]
    }
  ]
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|error|integer|false|none|none|
|reason|string|false|none|none|
|result|[[WxMetaService_model_opcua.ProjectDetail](#schemawxmetaservice_model_opcua.projectdetail)]|false|none|none|

<h2 id="tocS_model.BaseResponse-array_controller_ImageListObj">model.BaseResponse-array_controller_ImageListObj</h2>
<!-- backwards compatibility -->
<a id="schemamodel.baseresponse-array_controller_imagelistobj"></a>
<a id="schema_model.BaseResponse-array_controller_ImageListObj"></a>
<a id="tocSmodel.baseresponse-array_controller_imagelistobj"></a>
<a id="tocsmodel.baseresponse-array_controller_imagelistobj"></a>

```json
{
  "error": 0,
  "reason": "string",
  "result": [
    {
      "imageid": 0,
      "name": "string",
      "path": "string",
      "pathid": 0
    }
  ]
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|error|integer|false|none|none|
|reason|string|false|none|none|
|result|[[controller.ImageListObj](#schemacontroller.imagelistobj)]|false|none|none|

<h2 id="tocS_model.BaseResponse-array_model_AlarmArea">model.BaseResponse-array_model_AlarmArea</h2>
<!-- backwards compatibility -->
<a id="schemamodel.baseresponse-array_model_alarmarea"></a>
<a id="schema_model.BaseResponse-array_model_AlarmArea"></a>
<a id="tocSmodel.baseresponse-array_model_alarmarea"></a>
<a id="tocsmodel.baseresponse-array_model_alarmarea"></a>

```json
{
  "error": 0,
  "reason": "string",
  "result": [
    {
      "areaaudio": true,
      "audioid": 0,
      "description": "string",
      "name": "string",
      "projid": 0
    }
  ]
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|error|integer|false|none|none|
|reason|string|false|none|none|
|result|[[model.AlarmArea](#schemamodel.alarmarea)]|false|none|none|

<h2 id="tocS_model.BaseResponse-array_model_AudioListObj">model.BaseResponse-array_model_AudioListObj</h2>
<!-- backwards compatibility -->
<a id="schemamodel.baseresponse-array_model_audiolistobj"></a>
<a id="schema_model.BaseResponse-array_model_AudioListObj"></a>
<a id="tocSmodel.baseresponse-array_model_audiolistobj"></a>
<a id="tocsmodel.baseresponse-array_model_audiolistobj"></a>

```json
{
  "error": 0,
  "reason": "string",
  "result": [
    {
      "audioid": 0,
      "fullpathtext": "string",
      "name": "string",
      "path": "string",
      "pathid": 0
    }
  ]
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|error|integer|false|none|none|
|reason|string|false|none|none|
|result|[[model.AudioListObj](#schemamodel.audiolistobj)]|false|none|none|

<h2 id="tocS_model.BaseResponse-array_model_ConstDetail">model.BaseResponse-array_model_ConstDetail</h2>
<!-- backwards compatibility -->
<a id="schemamodel.baseresponse-array_model_constdetail"></a>
<a id="schema_model.BaseResponse-array_model_ConstDetail"></a>
<a id="tocSmodel.baseresponse-array_model_constdetail"></a>
<a id="tocsmodel.baseresponse-array_model_constdetail"></a>

```json
{
  "error": 0,
  "reason": "string",
  "result": [
    {
      "analen": 0,
      "area": 0,
      "createtimestamp": "string",
      "dataType": "string",
      "description": "string",
      "displaydec": 0,
      "displayint": 0,
      "level": 0,
      "modifytimestamp": "string",
      "projid": 0,
      "spanhi": 0,
      "spanlo": 0,
      "state0": "string",
      "state1": "string",
      "state2": "string",
      "state3": "string",
      "state4": "string",
      "state5": "string",
      "state6": "string",
      "state7": "string",
      "tagname": "string",
      "textarylen": 0,
      "textlen": 0,
      "value": "string"
    }
  ]
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|error|integer|false|none|none|
|reason|string|false|none|none|
|result|[[model.ConstDetail](#schemamodel.constdetail)]|false|none|none|

<h2 id="tocS_model.BaseResponse-array_model_CopyObject">model.BaseResponse-array_model_CopyObject</h2>
<!-- backwards compatibility -->
<a id="schemamodel.baseresponse-array_model_copyobject"></a>
<a id="schema_model.BaseResponse-array_model_CopyObject"></a>
<a id="tocSmodel.baseresponse-array_model_copyobject"></a>
<a id="tocsmodel.baseresponse-array_model_copyobject"></a>

```json
{
  "error": 0,
  "reason": "string",
  "result": [
    {
      "newobjectid": "string",
      "newobjectname": "string",
      "newparentpathid": 0,
      "newpathid": 0,
      "objectid": "string",
      "objectname": "string",
      "objecttype": 0,
      "pathid": 0
    }
  ]
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|error|integer|false|none|none|
|reason|string|false|none|none|
|result|[[model.CopyObject](#schemamodel.copyobject)]|false|none|none|

<h2 id="tocS_model.BaseResponse-array_model_DBDefine">model.BaseResponse-array_model_DBDefine</h2>
<!-- backwards compatibility -->
<a id="schemamodel.baseresponse-array_model_dbdefine"></a>
<a id="schema_model.BaseResponse-array_model_DBDefine"></a>
<a id="tocSmodel.baseresponse-array_model_dbdefine"></a>
<a id="tocsmodel.baseresponse-array_model_dbdefine"></a>

```json
{
  "error": 0,
  "reason": "string",
  "result": [
    {
      "dbtype": "string",
      "descript": "string",
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
|result|[[model.DBDefine](#schemamodel.dbdefine)]|false|none|none|

<h2 id="tocS_model.BaseResponse-array_model_DBDefineBody">model.BaseResponse-array_model_DBDefineBody</h2>
<!-- backwards compatibility -->
<a id="schemamodel.baseresponse-array_model_dbdefinebody"></a>
<a id="schema_model.BaseResponse-array_model_DBDefineBody"></a>
<a id="tocSmodel.baseresponse-array_model_dbdefinebody"></a>
<a id="tocsmodel.baseresponse-array_model_dbdefinebody"></a>

```json
{
  "error": 0,
  "reason": "string",
  "result": [
    {
      "item": "string",
      "value": "string"
    }
  ]
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|error|integer|false|none|none|
|reason|string|false|none|none|
|result|[[model.DBDefineBody](#schemamodel.dbdefinebody)]|false|none|none|

<h2 id="tocS_model.BaseResponse-array_model_DataSource">model.BaseResponse-array_model_DataSource</h2>
<!-- backwards compatibility -->
<a id="schemamodel.baseresponse-array_model_datasource"></a>
<a id="schema_model.BaseResponse-array_model_DataSource"></a>
<a id="tocSmodel.baseresponse-array_model_datasource"></a>
<a id="tocsmodel.baseresponse-array_model_datasource"></a>

```json
{
  "error": 0,
  "reason": "string",
  "result": [
    {
      "dbtype": "string",
      "descript": "string",
      "name": "string",
      "sourcetype": 0
    }
  ]
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|error|integer|false|none|none|
|reason|string|false|none|none|
|result|[[model.DataSource](#schemamodel.datasource)]|false|none|none|

<h2 id="tocS_model.BaseResponse-array_model_DataSourceBody">model.BaseResponse-array_model_DataSourceBody</h2>
<!-- backwards compatibility -->
<a id="schemamodel.baseresponse-array_model_datasourcebody"></a>
<a id="schema_model.BaseResponse-array_model_DataSourceBody"></a>
<a id="tocSmodel.baseresponse-array_model_datasourcebody"></a>
<a id="tocsmodel.baseresponse-array_model_datasourcebody"></a>

```json
{
  "error": 0,
  "reason": "string",
  "result": [
    {
      "item": "string",
      "sourcetype": 0,
      "value": "string"
    }
  ]
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|error|integer|false|none|none|
|reason|string|false|none|none|
|result|[[model.DataSourceBody](#schemamodel.datasourcebody)]|false|none|none|

<h2 id="tocS_model.BaseResponse-array_model_Display">model.BaseResponse-array_model_Display</h2>
<!-- backwards compatibility -->
<a id="schemamodel.baseresponse-array_model_display"></a>
<a id="schema_model.BaseResponse-array_model_Display"></a>
<a id="tocSmodel.baseresponse-array_model_display"></a>
<a id="tocsmodel.baseresponse-array_model_display"></a>

```json
{
  "error": 0,
  "reason": "string",
  "result": [
    {
      "fullpathtext": "string",
      "id": 0,
      "name": "string",
      "thumbnail": "string"
    }
  ]
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|error|integer|false|none|none|
|reason|string|false|none|none|
|result|[[model.Display](#schemamodel.display)]|false|none|none|

<h2 id="tocS_model.BaseResponse-array_model_FileCacheMeta">model.BaseResponse-array_model_FileCacheMeta</h2>
<!-- backwards compatibility -->
<a id="schemamodel.baseresponse-array_model_filecachemeta"></a>
<a id="schema_model.BaseResponse-array_model_FileCacheMeta"></a>
<a id="tocSmodel.baseresponse-array_model_filecachemeta"></a>
<a id="tocsmodel.baseresponse-array_model_filecachemeta"></a>

```json
{
  "error": 0,
  "reason": "string",
  "result": [
    {
      "filename": "string",
      "objecttype": 0,
      "parentpathid": 0,
      "projid": 0,
      "salt": "string",
      "ver": 0
    }
  ]
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|error|integer|false|none|none|
|reason|string|false|none|none|
|result|[[model.FileCacheMeta](#schemamodel.filecachemeta)]|false|none|none|

<h2 id="tocS_model.BaseResponse-array_model_FileStruct">model.BaseResponse-array_model_FileStruct</h2>
<!-- backwards compatibility -->
<a id="schemamodel.baseresponse-array_model_filestruct"></a>
<a id="schema_model.BaseResponse-array_model_FileStruct"></a>
<a id="tocSmodel.baseresponse-array_model_filestruct"></a>
<a id="tocsmodel.baseresponse-array_model_filestruct"></a>

```json
{
  "error": 0,
  "reason": "string",
  "result": [
    {
      "filetype": "string",
      "fullpathtext": "string",
      "id": "string",
      "name": "string",
      "thumbnail": "string"
    }
  ]
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|error|integer|false|none|none|
|reason|string|false|none|none|
|result|[[model.FileStruct](#schemamodel.filestruct)]|false|none|none|

<h2 id="tocS_model.BaseResponse-array_model_JSListObject">model.BaseResponse-array_model_JSListObject</h2>
<!-- backwards compatibility -->
<a id="schemamodel.baseresponse-array_model_jslistobject"></a>
<a id="schema_model.BaseResponse-array_model_JSListObject"></a>
<a id="tocSmodel.baseresponse-array_model_jslistobject"></a>
<a id="tocsmodel.baseresponse-array_model_jslistobject"></a>

```json
{
  "error": 0,
  "reason": "string",
  "result": [
    {
      "fullpath": "string",
      "fullpathtext": "string",
      "id": 0,
      "name": "string",
      "path": "string",
      "version": 0
    }
  ]
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|error|integer|false|none|none|
|reason|string|false|none|none|
|result|[[model.JSListObject](#schemamodel.jslistobject)]|false|none|none|

<h2 id="tocS_model.BaseResponse-array_model_JSPathList">model.BaseResponse-array_model_JSPathList</h2>
<!-- backwards compatibility -->
<a id="schemamodel.baseresponse-array_model_jspathlist"></a>
<a id="schema_model.BaseResponse-array_model_JSPathList"></a>
<a id="tocSmodel.baseresponse-array_model_jspathlist"></a>
<a id="tocsmodel.baseresponse-array_model_jspathlist"></a>

```json
{
  "error": 0,
  "reason": "string",
  "result": [
    {
      "fullpathtext": "string",
      "id": 0,
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
|result|[[model.JSPathList](#schemamodel.jspathlist)]|false|none|none|

<h2 id="tocS_model.BaseResponse-array_model_JSVersionObj">model.BaseResponse-array_model_JSVersionObj</h2>
<!-- backwards compatibility -->
<a id="schemamodel.baseresponse-array_model_jsversionobj"></a>
<a id="schema_model.BaseResponse-array_model_JSVersionObj"></a>
<a id="tocSmodel.baseresponse-array_model_jsversionobj"></a>
<a id="tocsmodel.baseresponse-array_model_jsversionobj"></a>

```json
{
  "error": 0,
  "reason": "string",
  "result": [
    {
      "id": 0,
      "version": 0
    }
  ]
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|error|integer|false|none|none|
|reason|string|false|none|none|
|result|[[model.JSVersionObj](#schemamodel.jsversionobj)]|false|none|none|

<h2 id="tocS_model.BaseResponse-array_model_NextLevelObj">model.BaseResponse-array_model_NextLevelObj</h2>
<!-- backwards compatibility -->
<a id="schemamodel.baseresponse-array_model_nextlevelobj"></a>
<a id="schema_model.BaseResponse-array_model_NextLevelObj"></a>
<a id="tocSmodel.baseresponse-array_model_nextlevelobj"></a>
<a id="tocsmodel.baseresponse-array_model_nextlevelobj"></a>

```json
{
  "error": 0,
  "reason": "string",
  "result": [
    {
      "displayid": 0,
      "fullpath": "string",
      "fullpathtext": "string",
      "id": 0,
      "objectid": "string",
      "objectname": "string",
      "objecttype": 0,
      "parentpathid": 0,
      "projid": 0
    }
  ]
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|error|integer|false|none|none|
|reason|string|false|none|none|
|result|[[model.NextLevelObj](#schemamodel.nextlevelobj)]|false|none|none|

<h2 id="tocS_model.BaseResponse-array_model_PluginFileDrift">model.BaseResponse-array_model_PluginFileDrift</h2>
<!-- backwards compatibility -->
<a id="schemamodel.baseresponse-array_model_pluginfiledrift"></a>
<a id="schema_model.BaseResponse-array_model_PluginFileDrift"></a>
<a id="tocSmodel.baseresponse-array_model_pluginfiledrift"></a>
<a id="tocsmodel.baseresponse-array_model_pluginfiledrift"></a>

```json
{
  "error": 0,
  "reason": "string",
  "result": [
    {
      "id": 0,
      "name": "string",
      "type": 0
    }
  ]
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|error|integer|false|none|none|
|reason|string|false|none|none|
|result|[[model.PluginFileDrift](#schemamodel.pluginfiledrift)]|false|none|none|

<h2 id="tocS_model.BaseResponse-array_model_RecipeListStruct">model.BaseResponse-array_model_RecipeListStruct</h2>
<!-- backwards compatibility -->
<a id="schemamodel.baseresponse-array_model_recipeliststruct"></a>
<a id="schema_model.BaseResponse-array_model_RecipeListStruct"></a>
<a id="tocSmodel.baseresponse-array_model_recipeliststruct"></a>
<a id="tocsmodel.baseresponse-array_model_recipeliststruct"></a>

```json
{
  "error": 0,
  "reason": "string",
  "result": [
    {
      "description": "string",
      "dwarea": 0,
      "dwlevel": 0,
      "editarea": 0,
      "editlevel": 0,
      "name": "string",
      "projid": 0,
      "readonly": true
    }
  ]
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|error|integer|false|none|none|
|reason|string|false|none|none|
|result|[[model.RecipeListStruct](#schemamodel.recipeliststruct)]|false|none|none|

<h2 id="tocS_model.BaseResponse-array_model_TemplateIDName">model.BaseResponse-array_model_TemplateIDName</h2>
<!-- backwards compatibility -->
<a id="schemamodel.baseresponse-array_model_templateidname"></a>
<a id="schema_model.BaseResponse-array_model_TemplateIDName"></a>
<a id="tocSmodel.baseresponse-array_model_templateidname"></a>
<a id="tocsmodel.baseresponse-array_model_templateidname"></a>

```json
{
  "error": 0,
  "reason": "string",
  "result": [
    {
      "id": 0,
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
|result|[[model.TemplateIDName](#schemamodel.templateidname)]|false|none|none|

<h2 id="tocS_model.BaseResponse-array_model_Tenant">model.BaseResponse-array_model_Tenant</h2>
<!-- backwards compatibility -->
<a id="schemamodel.baseresponse-array_model_tenant"></a>
<a id="schema_model.BaseResponse-array_model_Tenant"></a>
<a id="tocSmodel.baseresponse-array_model_tenant"></a>
<a id="tocsmodel.baseresponse-array_model_tenant"></a>

```json
{
  "error": 0,
  "reason": "string",
  "result": [
    {
      "__typename": "string",
      "defaultpage": "string",
      "depth": 0,
      "description": "string",
      "deviceOnBiRootOrgId": "string",
      "id": "string",
      "name": "string",
      "pathid": 0,
      "projid": 0,
      "usertype": 0
    }
  ]
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|error|integer|false|none|none|
|reason|string|false|none|none|
|result|[[model.Tenant](#schemamodel.tenant)]|false|none|none|

<h2 id="tocS_model.BaseResponse-array_model_UserList">model.BaseResponse-array_model_UserList</h2>
<!-- backwards compatibility -->
<a id="schemamodel.baseresponse-array_model_userlist"></a>
<a id="schema_model.BaseResponse-array_model_UserList"></a>
<a id="tocSmodel.baseresponse-array_model_userlist"></a>
<a id="tocsmodel.baseresponse-array_model_userlist"></a>

```json
{
  "error": 0,
  "reason": "string",
  "result": [
    {
      "homename": "string",
      "modifytime": "string",
      "name": "string",
      "usertype": 0
    }
  ]
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|error|integer|false|none|none|
|reason|string|false|none|none|
|result|[[model.UserList](#schemamodel.userlist)]|false|none|none|

<h2 id="tocS_model.BaseResponse-array_string">model.BaseResponse-array_string</h2>
<!-- backwards compatibility -->
<a id="schemamodel.baseresponse-array_string"></a>
<a id="schema_model.BaseResponse-array_string"></a>
<a id="tocSmodel.baseresponse-array_string"></a>
<a id="tocsmodel.baseresponse-array_string"></a>

```json
{
  "error": 0,
  "reason": "string",
  "result": [
    "string"
  ]
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|error|integer|false|none|none|
|reason|string|false|none|none|
|result|[string]|false|none|none|

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

<h2 id="tocS_model.BaseResponse-int">model.BaseResponse-int</h2>
<!-- backwards compatibility -->
<a id="schemamodel.baseresponse-int"></a>
<a id="schema_model.BaseResponse-int"></a>
<a id="tocSmodel.baseresponse-int"></a>
<a id="tocsmodel.baseresponse-int"></a>

```json
{
  "error": 0,
  "reason": "string",
  "result": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|error|integer|false|none|none|
|reason|string|false|none|none|
|result|integer|false|none|none|

<h2 id="tocS_model.BaseResponse-int64">model.BaseResponse-int64</h2>
<!-- backwards compatibility -->
<a id="schemamodel.baseresponse-int64"></a>
<a id="schema_model.BaseResponse-int64"></a>
<a id="tocSmodel.baseresponse-int64"></a>
<a id="tocsmodel.baseresponse-int64"></a>

```json
{
  "error": 0,
  "reason": "string",
  "result": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|error|integer|false|none|none|
|reason|string|false|none|none|
|result|integer|false|none|none|

<h2 id="tocS_model.BaseResponse-map_int_model_DisplayVersionInfo">model.BaseResponse-map_int_model_DisplayVersionInfo</h2>
<!-- backwards compatibility -->
<a id="schemamodel.baseresponse-map_int_model_displayversioninfo"></a>
<a id="schema_model.BaseResponse-map_int_model_DisplayVersionInfo"></a>
<a id="tocSmodel.baseresponse-map_int_model_displayversioninfo"></a>
<a id="tocsmodel.baseresponse-map_int_model_displayversioninfo"></a>

```json
{
  "error": 0,
  "reason": "string",
  "result": {
    "property1": {
      "id": 0,
      "phoneversion": 0,
      "tabletversion": 0,
      "version": 0
    },
    "property2": {
      "id": 0,
      "phoneversion": 0,
      "tabletversion": 0,
      "version": 0
    }
  }
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|error|integer|false|none|none|
|reason|string|false|none|none|
|result|[map_int_model.DisplayVersionInfo](#schemamap_int_model.displayversioninfo)|false|none|none|

<h2 id="tocS_model.BaseResponse-model_AddFolderRes">model.BaseResponse-model_AddFolderRes</h2>
<!-- backwards compatibility -->
<a id="schemamodel.baseresponse-model_addfolderres"></a>
<a id="schema_model.BaseResponse-model_AddFolderRes"></a>
<a id="tocSmodel.baseresponse-model_addfolderres"></a>
<a id="tocsmodel.baseresponse-model_addfolderres"></a>

```json
{
  "error": 0,
  "reason": "string",
  "result": {
    "pathid": 0
  }
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|error|integer|false|none|none|
|reason|string|false|none|none|
|result|[model.AddFolderRes](#schemamodel.addfolderres)|false|none|none|

<h2 id="tocS_model.BaseResponse-model_AlarmAllInfo">model.BaseResponse-model_AlarmAllInfo</h2>
<!-- backwards compatibility -->
<a id="schemamodel.baseresponse-model_alarmallinfo"></a>
<a id="schema_model.BaseResponse-model_AlarmAllInfo"></a>
<a id="tocSmodel.baseresponse-model_alarmallinfo"></a>
<a id="tocsmodel.baseresponse-model_alarmallinfo"></a>

```json
{
  "error": 0,
  "reason": "string",
  "result": {
    "area": [
      {
        "areaaudio": true,
        "audioid": 0,
        "audioname": "string",
        "audiopath": "string",
        "audiopathtext": "string",
        "description": "string",
        "name": "string",
        "projid": 0,
        "tags": [
          {
            "audioid": 0,
            "audioname": "string",
            "audiopath": "string",
            "audiopathtext": "string",
            "tagname": "string"
          }
        ]
      }
    ],
    "info": {
      "multiaudioid": 0,
      "multiaudioname": "string",
      "multiaudiopath": "string",
      "multiaudiopathtext": "string",
      "projid": 0,
      "singleaudioid": 0,
      "singleaudioname": "string",
      "singleaudiopath": "string",
      "singleaudiopathtext": "string"
    }
  }
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|error|integer|false|none|none|
|reason|string|false|none|none|
|result|[model.AlarmAllInfo](#schemamodel.alarmallinfo)|false|none|none|

<h2 id="tocS_model.BaseResponse-model_AlarmDetail">model.BaseResponse-model_AlarmDetail</h2>
<!-- backwards compatibility -->
<a id="schemamodel.baseresponse-model_alarmdetail"></a>
<a id="schema_model.BaseResponse-model_AlarmDetail"></a>
<a id="tocSmodel.baseresponse-model_alarmdetail"></a>
<a id="tocsmodel.baseresponse-model_alarmdetail"></a>

```json
{
  "error": 0,
  "reason": "string",
  "result": {
    "areaaudio": true,
    "audioid": 0,
    "audioname": "string",
    "audiopath": "string",
    "audiopathtext": "string",
    "description": "string",
    "name": "string",
    "projid": 0,
    "tags": [
      {
        "audioid": 0,
        "audioname": "string",
        "audiopath": "string",
        "audiopathtext": "string",
        "tagname": "string"
      }
    ]
  }
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|error|integer|false|none|none|
|reason|string|false|none|none|
|result|[model.AlarmDetail](#schemamodel.alarmdetail)|false|none|none|

<h2 id="tocS_model.BaseResponse-model_AlarmStructDetail">model.BaseResponse-model_AlarmStructDetail</h2>
<!-- backwards compatibility -->
<a id="schemamodel.baseresponse-model_alarmstructdetail"></a>
<a id="schema_model.BaseResponse-model_AlarmStructDetail"></a>
<a id="tocSmodel.baseresponse-model_alarmstructdetail"></a>
<a id="tocsmodel.baseresponse-model_alarmstructdetail"></a>

```json
{
  "error": 0,
  "reason": "string",
  "result": {
    "multiaudioid": 0,
    "multiaudioname": "string",
    "multiaudiopath": "string",
    "multiaudiopathtext": "string",
    "projid": 0,
    "singleaudioid": 0,
    "singleaudioname": "string",
    "singleaudiopath": "string",
    "singleaudiopathtext": "string"
  }
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|error|integer|false|none|none|
|reason|string|false|none|none|
|result|[model.AlarmStructDetail](#schemamodel.alarmstructdetail)|false|none|none|

<h2 id="tocS_model.BaseResponse-model_ConnectionDetail">model.BaseResponse-model_ConnectionDetail</h2>
<!-- backwards compatibility -->
<a id="schemamodel.baseresponse-model_connectiondetail"></a>
<a id="schema_model.BaseResponse-model_ConnectionDetail"></a>
<a id="tocSmodel.baseresponse-model_connectiondetail"></a>
<a id="tocsmodel.baseresponse-model_connectiondetail"></a>

```json
{
  "error": 0,
  "reason": "string",
  "result": {
    "ApplicationName": "string",
    "ApplicationUrl": "string",
    "AuthMode": "string",
    "DomainIP": "string",
    "DomainName": "string",
    "Password": "string",
    "PfxFile": "string",
    "SecurityMode": "string",
    "SecurityPolicy": "string",
    "Username": "string",
    "createtimestamp": "string",
    "modifytimestamp": "string",
    "name": "string",
    "opcuri": "string",
    "projid": 0
  }
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|error|integer|false|none|none|
|reason|string|false|none|none|
|result|[model.ConnectionDetail](#schemamodel.connectiondetail)|false|none|none|

<h2 id="tocS_model.BaseResponse-model_CreateDisplayRes">model.BaseResponse-model_CreateDisplayRes</h2>
<!-- backwards compatibility -->
<a id="schemamodel.baseresponse-model_createdisplayres"></a>
<a id="schema_model.BaseResponse-model_CreateDisplayRes"></a>
<a id="tocSmodel.baseresponse-model_createdisplayres"></a>
<a id="tocsmodel.baseresponse-model_createdisplayres"></a>

```json
{
  "error": 0,
  "reason": "string",
  "result": {
    "createupdateresult": {
      "displayid": 0,
      "name": "string",
      "pathid": 0,
      "phoneversion": 0,
      "tabletversion": 0,
      "version": 0
    }
  }
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|error|integer|false|none|none|
|reason|string|false|none|none|
|result|[model.CreateDisplayRes](#schemamodel.createdisplayres)|false|none|none|

<h2 id="tocS_model.BaseResponse-model_CreateJSRes">model.BaseResponse-model_CreateJSRes</h2>
<!-- backwards compatibility -->
<a id="schemamodel.baseresponse-model_createjsres"></a>
<a id="schema_model.BaseResponse-model_CreateJSRes"></a>
<a id="tocSmodel.baseresponse-model_createjsres"></a>
<a id="tocsmodel.baseresponse-model_createjsres"></a>

```json
{
  "error": 0,
  "reason": "string",
  "result": {
    "id": 0,
    "name": "string",
    "pathid": 0,
    "version": 0
  }
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|error|integer|false|none|none|
|reason|string|false|none|none|
|result|[model.CreateJSRes](#schemamodel.createjsres)|false|none|none|

<h2 id="tocS_model.BaseResponse-model_DisplayDetail">model.BaseResponse-model_DisplayDetail</h2>
<!-- backwards compatibility -->
<a id="schemamodel.baseresponse-model_displaydetail"></a>
<a id="schema_model.BaseResponse-model_DisplayDetail"></a>
<a id="tocSmodel.baseresponse-model_displaydetail"></a>
<a id="tocsmodel.baseresponse-model_displaydetail"></a>

```json
{
  "error": 0,
  "reason": "string",
  "result": {
    "content": "string",
    "fullpath": "string",
    "fullpathtext": "string",
    "id": 0,
    "name": "string",
    "pathid": 0,
    "thumbnail": "string",
    "version": 0
  }
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|error|integer|false|none|none|
|reason|string|false|none|none|
|result|[model.DisplayDetail](#schemamodel.displaydetail)|false|none|none|

<h2 id="tocS_model.BaseResponse-model_DisplayDetailMobileVer">model.BaseResponse-model_DisplayDetailMobileVer</h2>
<!-- backwards compatibility -->
<a id="schemamodel.baseresponse-model_displaydetailmobilever"></a>
<a id="schema_model.BaseResponse-model_DisplayDetailMobileVer"></a>
<a id="tocSmodel.baseresponse-model_displaydetailmobilever"></a>
<a id="tocsmodel.baseresponse-model_displaydetailmobilever"></a>

```json
{
  "error": 0,
  "reason": "string",
  "result": {
    "content": "string",
    "fullpath": "string",
    "fullpathtext": "string",
    "id": 0,
    "name": "string",
    "pathid": 0,
    "phoneversion": 0,
    "tabletversion": 0,
    "thumbnail": "string",
    "version": 0
  }
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|error|integer|false|none|none|
|reason|string|false|none|none|
|result|[model.DisplayDetailMobileVer](#schemamodel.displaydetailmobilever)|false|none|none|

<h2 id="tocS_model.BaseResponse-model_DisplayVersionRes">model.BaseResponse-model_DisplayVersionRes</h2>
<!-- backwards compatibility -->
<a id="schemamodel.baseresponse-model_displayversionres"></a>
<a id="schema_model.BaseResponse-model_DisplayVersionRes"></a>
<a id="tocSmodel.baseresponse-model_displayversionres"></a>
<a id="tocsmodel.baseresponse-model_displayversionres"></a>

```json
{
  "error": 0,
  "reason": "string",
  "result": {
    "phoneversion": 0,
    "tabletversion": 0,
    "version": 0
  }
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|error|integer|false|none|none|
|reason|string|false|none|none|
|result|[model.DisplayVersionRes](#schemamodel.displayversionres)|false|none|none|

<h2 id="tocS_model.BaseResponse-model_GetNextLevelbyidRes">model.BaseResponse-model_GetNextLevelbyidRes</h2>
<!-- backwards compatibility -->
<a id="schemamodel.baseresponse-model_getnextlevelbyidres"></a>
<a id="schema_model.BaseResponse-model_GetNextLevelbyidRes"></a>
<a id="tocSmodel.baseresponse-model_getnextlevelbyidres"></a>
<a id="tocsmodel.baseresponse-model_getnextlevelbyidres"></a>

```json
{
  "error": 0,
  "reason": "string",
  "result": {
    "objectlist": [
      {
        "displayid": 0,
        "fullpath": "string",
        "fullpathtext": "string",
        "id": 0,
        "objectid": "string",
        "objectname": "string",
        "objecttype": 0,
        "parentpathid": 0,
        "projid": 0
      }
    ]
  }
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|error|integer|false|none|none|
|reason|string|false|none|none|
|result|[model.GetNextLevelbyidRes](#schemamodel.getnextlevelbyidres)|false|none|none|

<h2 id="tocS_model.BaseResponse-model_JSDetailRes">model.BaseResponse-model_JSDetailRes</h2>
<!-- backwards compatibility -->
<a id="schemamodel.baseresponse-model_jsdetailres"></a>
<a id="schema_model.BaseResponse-model_JSDetailRes"></a>
<a id="tocSmodel.baseresponse-model_jsdetailres"></a>
<a id="tocsmodel.baseresponse-model_jsdetailres"></a>

```json
{
  "error": 0,
  "reason": "string",
  "result": {
    "fullpath": "string",
    "id": 0,
    "jscontent": "string",
    "name": "string",
    "path": "string",
    "version": 0
  }
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|error|integer|false|none|none|
|reason|string|false|none|none|
|result|[model.JSDetailRes](#schemamodel.jsdetailres)|false|none|none|

<h2 id="tocS_model.BaseResponse-model_MQTTBrokerSetting">model.BaseResponse-model_MQTTBrokerSetting</h2>
<!-- backwards compatibility -->
<a id="schemamodel.baseresponse-model_mqttbrokersetting"></a>
<a id="schema_model.BaseResponse-model_MQTTBrokerSetting"></a>
<a id="tocSmodel.baseresponse-model_mqttbrokersetting"></a>
<a id="tocsmodel.baseresponse-model_mqttbrokersetting"></a>

```json
{
  "error": 0,
  "reason": "string",
  "result": {
    "host": "string",
    "keepalive": 0,
    "password": "string",
    "tcpport": 0,
    "tlsport": 0,
    "username": "string",
    "websocketport": 0,
    "websockettlsport": 0
  }
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|error|integer|false|none|none|
|reason|string|false|none|none|
|result|[model.MQTTBrokerSetting](#schemamodel.mqttbrokersetting)|false|none|none|

<h2 id="tocS_model.BaseResponse-model_NextLevelObj">model.BaseResponse-model_NextLevelObj</h2>
<!-- backwards compatibility -->
<a id="schemamodel.baseresponse-model_nextlevelobj"></a>
<a id="schema_model.BaseResponse-model_NextLevelObj"></a>
<a id="tocSmodel.baseresponse-model_nextlevelobj"></a>
<a id="tocsmodel.baseresponse-model_nextlevelobj"></a>

```json
{
  "error": 0,
  "reason": "string",
  "result": {
    "displayid": 0,
    "fullpath": "string",
    "fullpathtext": "string",
    "id": 0,
    "objectid": "string",
    "objectname": "string",
    "objecttype": 0,
    "parentpathid": 0,
    "projid": 0
  }
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|error|integer|false|none|none|
|reason|string|false|none|none|
|result|[model.NextLevelObj](#schemamodel.nextlevelobj)|false|none|none|

<h2 id="tocS_model.BaseResponse-model_PluginFileDrift">model.BaseResponse-model_PluginFileDrift</h2>
<!-- backwards compatibility -->
<a id="schemamodel.baseresponse-model_pluginfiledrift"></a>
<a id="schema_model.BaseResponse-model_PluginFileDrift"></a>
<a id="tocSmodel.baseresponse-model_pluginfiledrift"></a>
<a id="tocsmodel.baseresponse-model_pluginfiledrift"></a>

```json
{
  "error": 0,
  "reason": "string",
  "result": {
    "id": 0,
    "name": "string",
    "type": 0
  }
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|error|integer|false|none|none|
|reason|string|false|none|none|
|result|[model.PluginFileDrift](#schemamodel.pluginfiledrift)|false|none|none|

<h2 id="tocS_model.BaseResponse-model_RecordStruct">model.BaseResponse-model_RecordStruct</h2>
<!-- backwards compatibility -->
<a id="schemamodel.baseresponse-model_recordstruct"></a>
<a id="schema_model.BaseResponse-model_RecordStruct"></a>
<a id="tocSmodel.baseresponse-model_recordstruct"></a>
<a id="tocsmodel.baseresponse-model_recordstruct"></a>

```json
{
  "error": 0,
  "reason": "string",
  "result": {
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
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|error|integer|false|none|none|
|reason|string|false|none|none|
|result|[model.RecordStruct](#schemamodel.recordstruct)|false|none|none|

<h2 id="tocS_model.BaseResponse-model_TemplateDetail">model.BaseResponse-model_TemplateDetail</h2>
<!-- backwards compatibility -->
<a id="schemamodel.baseresponse-model_templatedetail"></a>
<a id="schema_model.BaseResponse-model_TemplateDetail"></a>
<a id="tocSmodel.baseresponse-model_templatedetail"></a>
<a id="tocsmodel.baseresponse-model_templatedetail"></a>

```json
{
  "error": 0,
  "reason": "string",
  "result": {
    "content": "string",
    "id": 0,
    "name": "string",
    "projid": 0,
    "version": 0
  }
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|error|integer|false|none|none|
|reason|string|false|none|none|
|result|[model.TemplateDetail](#schemamodel.templatedetail)|false|none|none|

<h2 id="tocS_model.BaseResponse-model_UploadAudioRes">model.BaseResponse-model_UploadAudioRes</h2>
<!-- backwards compatibility -->
<a id="schemamodel.baseresponse-model_uploadaudiores"></a>
<a id="schema_model.BaseResponse-model_UploadAudioRes"></a>
<a id="tocSmodel.baseresponse-model_uploadaudiores"></a>
<a id="tocsmodel.baseresponse-model_uploadaudiores"></a>

```json
{
  "error": 0,
  "reason": "string",
  "result": {
    "id": 0,
    "name": "string",
    "pathid": 0
  }
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|error|integer|false|none|none|
|reason|string|false|none|none|
|result|[model.UploadAudioRes](#schemamodel.uploadaudiores)|false|none|none|

<h2 id="tocS_model.BaseResponse-model_UploadImageRes">model.BaseResponse-model_UploadImageRes</h2>
<!-- backwards compatibility -->
<a id="schemamodel.baseresponse-model_uploadimageres"></a>
<a id="schema_model.BaseResponse-model_UploadImageRes"></a>
<a id="tocSmodel.baseresponse-model_uploadimageres"></a>
<a id="tocsmodel.baseresponse-model_uploadimageres"></a>

```json
{
  "error": 0,
  "reason": "string",
  "result": {
    "id": 0,
    "name": "string",
    "pathid": 0
  }
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|error|integer|false|none|none|
|reason|string|false|none|none|
|result|[model.UploadImageRes](#schemamodel.uploadimageres)|false|none|none|

<h2 id="tocS_model.BaseResponse-service_ProductInfo">model.BaseResponse-service_ProductInfo</h2>
<!-- backwards compatibility -->
<a id="schemamodel.baseresponse-service_productinfo"></a>
<a id="schema_model.BaseResponse-service_ProductInfo"></a>
<a id="tocSmodel.baseresponse-service_productinfo"></a>
<a id="tocsmodel.baseresponse-service_productinfo"></a>

```json
{
  "error": 0,
  "reason": "string",
  "result": {
    "dataSource": "string",
    "serialNumber": "string",
    "validUntil": "string",
    "version": "string"
  }
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|error|integer|false|none|none|
|reason|string|false|none|none|
|result|[service.ProductInfo](#schemaservice.productinfo)|false|none|none|

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
  "result": {}
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|error|integer|false|none|none|
|reason|string|false|none|none|
|result|object|false|none|none|

<h2 id="tocS_model.ConnectionBase">model.ConnectionBase</h2>
<!-- backwards compatibility -->
<a id="schemamodel.connectionbase"></a>
<a id="schema_model.ConnectionBase"></a>
<a id="tocSmodel.connectionbase"></a>
<a id="tocsmodel.connectionbase"></a>

```json
{
  "createtimestamp": "string",
  "modifytimestamp": "string",
  "name": "string",
  "opcuri": "string",
  "projid": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|createtimestamp|string|false|none|none|
|modifytimestamp|string|false|none|none|
|name|string|false|none|none|
|opcuri|string|false|none|none|
|projid|integer|false|none|none|

<h2 id="tocS_model.ConnectionDelete">model.ConnectionDelete</h2>
<!-- backwards compatibility -->
<a id="schemamodel.connectiondelete"></a>
<a id="schema_model.ConnectionDelete"></a>
<a id="tocSmodel.connectiondelete"></a>
<a id="tocsmodel.connectiondelete"></a>

```json
{
  "names": [
    "string"
  ],
  "projid": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|names|[string]|false|none|none|
|projid|integer|false|none|none|

<h2 id="tocS_model.ConnectionDetail">model.ConnectionDetail</h2>
<!-- backwards compatibility -->
<a id="schemamodel.connectiondetail"></a>
<a id="schema_model.ConnectionDetail"></a>
<a id="tocSmodel.connectiondetail"></a>
<a id="tocsmodel.connectiondetail"></a>

```json
{
  "ApplicationName": "string",
  "ApplicationUrl": "string",
  "AuthMode": "string",
  "DomainIP": "string",
  "DomainName": "string",
  "Password": "string",
  "PfxFile": "string",
  "SecurityMode": "string",
  "SecurityPolicy": "string",
  "Username": "string",
  "createtimestamp": "string",
  "modifytimestamp": "string",
  "name": "string",
  "opcuri": "string",
  "projid": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|ApplicationName|string|false|none|none|
|ApplicationUrl|string|false|none|none|
|AuthMode|string|false|none|none|
|DomainIP|string|false|none|none|
|DomainName|string|false|none|none|
|Password|string|false|none|none|
|PfxFile|string|false|none|none|
|SecurityMode|string|false|none|none|
|SecurityPolicy|string|false|none|none|
|Username|string|false|none|none|
|createtimestamp|string|false|none|none|
|modifytimestamp|string|false|none|none|
|name|string|false|none|none|
|opcuri|string|false|none|none|
|projid|integer|false|none|none|

<h2 id="tocS_model.ConstDetail">model.ConstDetail</h2>
<!-- backwards compatibility -->
<a id="schemamodel.constdetail"></a>
<a id="schema_model.ConstDetail"></a>
<a id="tocSmodel.constdetail"></a>
<a id="tocsmodel.constdetail"></a>

```json
{
  "analen": 0,
  "area": 0,
  "createtimestamp": "string",
  "dataType": "string",
  "description": "string",
  "displaydec": 0,
  "displayint": 0,
  "level": 0,
  "modifytimestamp": "string",
  "projid": 0,
  "spanhi": 0,
  "spanlo": 0,
  "state0": "string",
  "state1": "string",
  "state2": "string",
  "state3": "string",
  "state4": "string",
  "state5": "string",
  "state6": "string",
  "state7": "string",
  "tagname": "string",
  "textarylen": 0,
  "textlen": 0,
  "value": "string"
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|analen|integer|false|none|none|
|area|integer|false|none|none|
|createtimestamp|string|false|none|none|
|dataType|string|false|none|none|
|description|string|false|none|none|
|displaydec|integer|false|none|none|
|displayint|integer|false|none|analog|
|level|integer|false|none|none|
|modifytimestamp|string|false|none|none|
|projid|integer|false|none|none|
|spanhi|number|false|none|none|
|spanlo|number|false|none|none|
|state0|string|false|none|discrete|
|state1|string|false|none|none|
|state2|string|false|none|none|
|state3|string|false|none|none|
|state4|string|false|none|none|
|state5|string|false|none|none|
|state6|string|false|none|none|
|state7|string|false|none|none|
|tagname|string|false|none|none|
|textarylen|integer|false|none|none|
|textlen|integer|false|none|text|
|value|string|false|none|none|

<h2 id="tocS_model.ConstUpdate">model.ConstUpdate</h2>
<!-- backwards compatibility -->
<a id="schemamodel.constupdate"></a>
<a id="schema_model.ConstUpdate"></a>
<a id="tocSmodel.constupdate"></a>
<a id="tocsmodel.constupdate"></a>

```json
{
  "analen": 0,
  "area": 0,
  "createtimestamp": "string",
  "dataType": "string",
  "description": "string",
  "displaydec": 0,
  "displayint": 0,
  "level": 0,
  "modifytimestamp": "string",
  "oritagname": "string",
  "projid": 0,
  "spanhi": 0,
  "spanlo": 0,
  "state0": "string",
  "state1": "string",
  "state2": "string",
  "state3": "string",
  "state4": "string",
  "state5": "string",
  "state6": "string",
  "state7": "string",
  "tagname": "string",
  "textarylen": 0,
  "textlen": 0,
  "value": "string"
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|analen|integer|false|none|none|
|area|integer|false|none|none|
|createtimestamp|string|false|none|none|
|dataType|string|false|none|none|
|description|string|false|none|none|
|displaydec|integer|false|none|none|
|displayint|integer|false|none|analog|
|level|integer|false|none|none|
|modifytimestamp|string|false|none|none|
|oritagname|string|false|none|none|
|projid|integer|false|none|none|
|spanhi|number|false|none|none|
|spanlo|number|false|none|none|
|state0|string|false|none|discrete|
|state1|string|false|none|none|
|state2|string|false|none|none|
|state3|string|false|none|none|
|state4|string|false|none|none|
|state5|string|false|none|none|
|state6|string|false|none|none|
|state7|string|false|none|none|
|tagname|string|false|none|none|
|textarylen|integer|false|none|none|
|textlen|integer|false|none|text|
|value|string|false|none|none|

<h2 id="tocS_model.ContainJS">model.ContainJS</h2>
<!-- backwards compatibility -->
<a id="schemamodel.containjs"></a>
<a id="schema_model.ContainJS"></a>
<a id="tocSmodel.containjs"></a>
<a id="tocsmodel.containjs"></a>

```json
{
  "jsids": [
    0
  ],
  "pathid": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|jsids|[integer]|false|none|none|
|pathid|integer|false|none|none|

<h2 id="tocS_model.CopyObject">model.CopyObject</h2>
<!-- backwards compatibility -->
<a id="schemamodel.copyobject"></a>
<a id="schema_model.CopyObject"></a>
<a id="tocSmodel.copyobject"></a>
<a id="tocsmodel.copyobject"></a>

```json
{
  "newobjectid": "string",
  "newobjectname": "string",
  "newparentpathid": 0,
  "newpathid": 0,
  "objectid": "string",
  "objectname": "string",
  "objecttype": 0,
  "pathid": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|newobjectid|string|false|none|none|
|newobjectname|string|false|none|none|
|newparentpathid|integer|false|none|none|
|newpathid|integer|false|none|none|
|objectid|string|false|none|none|
|objectname|string|false|none|none|
|objecttype|integer|false|none|none|
|pathid|integer|false|none|none|

<h2 id="tocS_model.CopyObject1">model.CopyObject1</h2>
<!-- backwards compatibility -->
<a id="schemamodel.copyobject1"></a>
<a id="schema_model.CopyObject1"></a>
<a id="tocSmodel.copyobject1"></a>
<a id="tocsmodel.copyobject1"></a>

```json
{
  "fileexist": true,
  "fullpathtext": "string",
  "newfullpathtext": "string",
  "newobjectid": "string",
  "newobjectname": "string",
  "newparentpathid": 0,
  "newpathid": 0,
  "objectid": "string",
  "objectname": "string",
  "objecttype": 0,
  "parentpathid": 0,
  "pathid": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|fileexist|boolean|false|none|none|
|fullpathtext|string|false|none|none|
|newfullpathtext|string|false|none|none|
|newobjectid|string|false|none|none|
|newobjectname|string|false|none|none|
|newparentpathid|integer|false|none|none|
|newpathid|integer|false|none|none|
|objectid|string|false|none|none|
|objectname|string|false|none|none|
|objecttype|integer|false|none|none|
|parentpathid|integer|false|none|none|
|pathid|integer|false|none|none|

<h2 id="tocS_model.CreateDisplayAPIResObj">model.CreateDisplayAPIResObj</h2>
<!-- backwards compatibility -->
<a id="schemamodel.createdisplayapiresobj"></a>
<a id="schema_model.CreateDisplayAPIResObj"></a>
<a id="tocSmodel.createdisplayapiresobj"></a>
<a id="tocsmodel.createdisplayapiresobj"></a>

```json
{
  "displayid": 0,
  "name": "string",
  "pathid": 0,
  "phoneversion": 0,
  "tabletversion": 0,
  "version": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|displayid|integer|false|none|none|
|name|string|false|none|none|
|pathid|integer|false|none|none|
|phoneversion|integer|false|none|none|
|tabletversion|integer|false|none|none|
|version|integer|false|none|none|

<h2 id="tocS_model.CreateDisplayRes">model.CreateDisplayRes</h2>
<!-- backwards compatibility -->
<a id="schemamodel.createdisplayres"></a>
<a id="schema_model.CreateDisplayRes"></a>
<a id="tocSmodel.createdisplayres"></a>
<a id="tocsmodel.createdisplayres"></a>

```json
{
  "createupdateresult": {
    "displayid": 0,
    "name": "string",
    "pathid": 0,
    "phoneversion": 0,
    "tabletversion": 0,
    "version": 0
  }
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|createupdateresult|[model.CreateDisplayAPIResObj](#schemamodel.createdisplayapiresobj)|false|none|none|

<h2 id="tocS_model.CreateJSReq">model.CreateJSReq</h2>
<!-- backwards compatibility -->
<a id="schemamodel.createjsreq"></a>
<a id="schema_model.CreateJSReq"></a>
<a id="tocSmodel.createjsreq"></a>
<a id="tocsmodel.createjsreq"></a>

```json
{
  "id": 0,
  "jscontent": "string",
  "name": "string"
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|id|integer|false|none|none|
|jscontent|string|false|none|none|
|name|string|false|none|none|

<h2 id="tocS_model.CreateJSRes">model.CreateJSRes</h2>
<!-- backwards compatibility -->
<a id="schemamodel.createjsres"></a>
<a id="schema_model.CreateJSRes"></a>
<a id="tocSmodel.createjsres"></a>
<a id="tocsmodel.createjsres"></a>

```json
{
  "id": 0,
  "name": "string",
  "pathid": 0,
  "version": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|id|integer|false|none|none|
|name|string|false|none|none|
|pathid|integer|false|none|none|
|version|integer|false|none|none|

<h2 id="tocS_model.DBDefine">model.DBDefine</h2>
<!-- backwards compatibility -->
<a id="schemamodel.dbdefine"></a>
<a id="schema_model.DBDefine"></a>
<a id="tocSmodel.dbdefine"></a>
<a id="tocsmodel.dbdefine"></a>

```json
{
  "dbtype": "string",
  "descript": "string",
  "name": "string"
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|dbtype|string|false|none|none|
|descript|string|false|none|none|
|name|string|false|none|none|

<h2 id="tocS_model.DBDefineBody">model.DBDefineBody</h2>
<!-- backwards compatibility -->
<a id="schemamodel.dbdefinebody"></a>
<a id="schema_model.DBDefineBody"></a>
<a id="tocSmodel.dbdefinebody"></a>
<a id="tocsmodel.dbdefinebody"></a>

```json
{
  "item": "string",
  "value": "string"
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|item|string|false|none|none|
|value|string|false|none|none|

<h2 id="tocS_model.DMVariableExchange">model.DMVariableExchange</h2>
<!-- backwards compatibility -->
<a id="schemamodel.dmvariableexchange"></a>
<a id="schema_model.DMVariableExchange"></a>
<a id="tocSmodel.dmvariableexchange"></a>
<a id="tocsmodel.dmvariableexchange"></a>

```json
{
  "dmid": "string",
  "name": "string",
  "no": 0,
  "projid": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|dmid|string|false|none|none|
|name|string|false|none|none|
|no|integer|false|none|none|
|projid|integer|false|none|none|

<h2 id="tocS_model.DataModelID">model.DataModelID</h2>
<!-- backwards compatibility -->
<a id="schemamodel.datamodelid"></a>
<a id="schema_model.DataModelID"></a>
<a id="tocSmodel.datamodelid"></a>
<a id="tocsmodel.datamodelid"></a>

```json
{
  "id": "string",
  "projid": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|id|string|false|none|none|
|projid|integer|false|none|none|

<h2 id="tocS_model.DataModelStruct">model.DataModelStruct</h2>
<!-- backwards compatibility -->
<a id="schemamodel.datamodelstruct"></a>
<a id="schema_model.DataModelStruct"></a>
<a id="tocSmodel.datamodelstruct"></a>
<a id="tocsmodel.datamodelstruct"></a>

```json
{
  "content": "string",
  "id": "string",
  "importpw": "string",
  "name": "string",
  "password": "string",
  "projid": 0,
  "thumbnail": [
    0
  ],
  "version": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|content|string|false|none|none|
|id|string|false|none|none|
|importpw|string|false|none|none|
|name|string|false|none|none|
|password|string|false|none|none|
|projid|integer|false|none|none|
|thumbnail|[integer]|false|none|none|
|version|integer|false|none|none|

<h2 id="tocS_model.DataModelVariImport">model.DataModelVariImport</h2>
<!-- backwards compatibility -->
<a id="schemamodel.datamodelvariimport"></a>
<a id="schema_model.DataModelVariImport"></a>
<a id="tocSmodel.datamodelvariimport"></a>
<a id="tocsmodel.datamodelvariimport"></a>

```json
{
  "dmid": "string",
  "projid": 0,
  "variables": [
    {
      "area": 0,
      "consttype": "string",
      "decpl": 0,
      "dmid": "string",
      "event": true,
      "info": {},
      "interval": 0,
      "label": "string",
      "level": 0,
      "name": "string",
      "projid": 0,
      "regex": "string",
      "sequence": 0,
      "spanhi": 0,
      "spanlo": 0,
      "type": "string",
      "value": "string",
      "version": "string"
    }
  ]
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|dmid|string|false|none|none|
|projid|integer|false|none|none|
|variables|[[model.DataModelVariStruct](#schemamodel.datamodelvaristruct)]|false|none|none|

<h2 id="tocS_model.DataModelVariStruct">model.DataModelVariStruct</h2>
<!-- backwards compatibility -->
<a id="schemamodel.datamodelvaristruct"></a>
<a id="schema_model.DataModelVariStruct"></a>
<a id="tocSmodel.datamodelvaristruct"></a>
<a id="tocsmodel.datamodelvaristruct"></a>

```json
{
  "area": 0,
  "consttype": "string",
  "decpl": 0,
  "dmid": "string",
  "event": true,
  "info": {},
  "interval": 0,
  "label": "string",
  "level": 0,
  "name": "string",
  "projid": 0,
  "regex": "string",
  "sequence": 0,
  "spanhi": 0,
  "spanlo": 0,
  "type": "string",
  "value": "string",
  "version": "string"
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|area|integer|false|none|none|
|consttype|string|false|none|none|
|decpl|integer|false|none|none|
|dmid|string|false|none|none|
|event|boolean|false|none|none|
|info|object|false|none|none|
|interval|integer|false|none|none|
|label|string|false|none|none|
|level|integer|false|none|none|
|name|string|false|none|none|
|projid|integer|false|none|none|
|regex|string|false|none|none|
|sequence|integer|false|none|none|
|spanhi|number|false|none|none|
|spanlo|number|false|none|none|
|type|string|false|none|none|
|value|string|false|none|none|
|version|string|false|none|none|

<h2 id="tocS_model.DataModelVariUpdate">model.DataModelVariUpdate</h2>
<!-- backwards compatibility -->
<a id="schemamodel.datamodelvariupdate"></a>
<a id="schema_model.DataModelVariUpdate"></a>
<a id="tocSmodel.datamodelvariupdate"></a>
<a id="tocsmodel.datamodelvariupdate"></a>

```json
{
  "area": 0,
  "consttype": "string",
  "decpl": 0,
  "dmid": "string",
  "event": true,
  "info": {},
  "interval": 0,
  "label": "string",
  "level": 0,
  "name": "string",
  "oriname": "string",
  "projid": 0,
  "regex": "string",
  "spanhi": 0,
  "spanlo": 0,
  "type": "string",
  "value": "string",
  "version": "string"
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|area|integer|false|none|none|
|consttype|string|false|none|none|
|decpl|integer|false|none|none|
|dmid|string|false|none|none|
|event|boolean|false|none|none|
|info|object|false|none|none|
|interval|integer|false|none|none|
|label|string|false|none|none|
|level|integer|false|none|none|
|name|string|false|none|none|
|oriname|string|false|none|none|
|projid|integer|false|none|none|
|regex|string|false|none|none|
|spanhi|number|false|none|none|
|spanlo|number|false|none|none|
|type|string|false|none|none|
|value|string|false|none|none|
|version|string|false|none|none|

<h2 id="tocS_model.DataModelVariableDelete">model.DataModelVariableDelete</h2>
<!-- backwards compatibility -->
<a id="schemamodel.datamodelvariabledelete"></a>
<a id="schema_model.DataModelVariableDelete"></a>
<a id="tocSmodel.datamodelvariabledelete"></a>
<a id="tocsmodel.datamodelvariabledelete"></a>

```json
{
  "variables": [
    {
      "dmid": "string",
      "name": "string",
      "projid": 0
    }
  ]
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|variables|[object]|false|none|none|
|» dmid|string|false|none|none|
|» name|string|false|none|none|
|» projid|integer|false|none|none|

<h2 id="tocS_model.DataModeldmID">model.DataModeldmID</h2>
<!-- backwards compatibility -->
<a id="schemamodel.datamodeldmid"></a>
<a id="schema_model.DataModeldmID"></a>
<a id="tocSmodel.datamodeldmid"></a>
<a id="tocsmodel.datamodeldmid"></a>

```json
{
  "dmid": "string",
  "projid": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|dmid|string|false|none|none|
|projid|integer|false|none|none|

<h2 id="tocS_model.DataSource">model.DataSource</h2>
<!-- backwards compatibility -->
<a id="schemamodel.datasource"></a>
<a id="schema_model.DataSource"></a>
<a id="tocSmodel.datasource"></a>
<a id="tocsmodel.datasource"></a>

```json
{
  "dbtype": "string",
  "descript": "string",
  "name": "string",
  "sourcetype": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|dbtype|string|false|none|none|
|descript|string|false|none|none|
|name|string|false|none|none|
|sourcetype|integer|false|none|none|

<h2 id="tocS_model.DataSourceBody">model.DataSourceBody</h2>
<!-- backwards compatibility -->
<a id="schemamodel.datasourcebody"></a>
<a id="schema_model.DataSourceBody"></a>
<a id="tocSmodel.datasourcebody"></a>
<a id="tocsmodel.datasourcebody"></a>

```json
{
  "item": "string",
  "sourcetype": 0,
  "value": "string"
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|item|string|false|none|none|
|sourcetype|integer|false|none|none|
|value|string|false|none|none|

<h2 id="tocS_model.DataSourceHead">model.DataSourceHead</h2>
<!-- backwards compatibility -->
<a id="schemamodel.datasourcehead"></a>
<a id="schema_model.DataSourceHead"></a>
<a id="tocSmodel.datasourcehead"></a>
<a id="tocsmodel.datasourcehead"></a>

```json
{
  "dbtype": "string",
  "descript": "string",
  "info": [
    {
      "item": "string",
      "sourcetype": 0,
      "value": "string"
    }
  ],
  "name": "string",
  "sourcetype": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|dbtype|string|false|none|none|
|descript|string|false|none|none|
|info|[[model.DataSourceBody](#schemamodel.datasourcebody)]|false|none|none|
|name|string|false|none|none|
|sourcetype|integer|false|none|none|

<h2 id="tocS_model.DeleteAudioReq">model.DeleteAudioReq</h2>
<!-- backwards compatibility -->
<a id="schemamodel.deleteaudioreq"></a>
<a id="schema_model.DeleteAudioReq"></a>
<a id="tocSmodel.deleteaudioreq"></a>
<a id="tocsmodel.deleteaudioreq"></a>

```json
{
  "audioids": [
    0
  ]
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|audioids|[integer]|false|none|none|

<h2 id="tocS_model.DeleteDisplayReq">model.DeleteDisplayReq</h2>
<!-- backwards compatibility -->
<a id="schemamodel.deletedisplayreq"></a>
<a id="schema_model.DeleteDisplayReq"></a>
<a id="tocSmodel.deletedisplayreq"></a>
<a id="tocsmodel.deletedisplayreq"></a>

```json
{
  "displayids": [
    0
  ],
  "mobileids": [
    {
      "id": 0,
      "type": "string"
    }
  ]
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|displayids|[integer]|false|none|none|
|mobileids|[object]|false|none|none|
|» id|integer|false|none|none|
|» type|string|false|none|none|

<h2 id="tocS_model.DeleteFolderReq">model.DeleteFolderReq</h2>
<!-- backwards compatibility -->
<a id="schemamodel.deletefolderreq"></a>
<a id="schema_model.DeleteFolderReq"></a>
<a id="tocSmodel.deletefolderreq"></a>
<a id="tocsmodel.deletefolderreq"></a>

```json
{
  "pathids": [
    0
  ]
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|pathids|[integer]|false|none|none|

<h2 id="tocS_model.DeleteJSReq">model.DeleteJSReq</h2>
<!-- backwards compatibility -->
<a id="schemamodel.deletejsreq"></a>
<a id="schema_model.DeleteJSReq"></a>
<a id="tocSmodel.deletejsreq"></a>
<a id="tocsmodel.deletejsreq"></a>

```json
{
  "jsids": [
    0
  ]
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|jsids|[integer]|false|none|none|

<h2 id="tocS_model.Display">model.Display</h2>
<!-- backwards compatibility -->
<a id="schemamodel.display"></a>
<a id="schema_model.Display"></a>
<a id="tocSmodel.display"></a>
<a id="tocsmodel.display"></a>

```json
{
  "fullpathtext": "string",
  "id": 0,
  "name": "string",
  "thumbnail": "string"
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|fullpathtext|string|false|none|Path       string `db:"displaypath" json:"path"`|
|id|integer|false|none|none|
|name|string|false|none|none|
|thumbnail|string|false|none|none|

<h2 id="tocS_model.DisplayDetail">model.DisplayDetail</h2>
<!-- backwards compatibility -->
<a id="schemamodel.displaydetail"></a>
<a id="schema_model.DisplayDetail"></a>
<a id="tocSmodel.displaydetail"></a>
<a id="tocsmodel.displaydetail"></a>

```json
{
  "content": "string",
  "fullpath": "string",
  "fullpathtext": "string",
  "id": 0,
  "name": "string",
  "pathid": 0,
  "thumbnail": "string",
  "version": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|content|string|false|none|none|
|fullpath|string|false|none|none|
|fullpathtext|string|false|none|none|
|id|integer|false|none|none|
|name|string|false|none|none|
|pathid|integer|false|none|none|
|thumbnail|string|false|none|none|
|version|integer|false|none|none|

<h2 id="tocS_model.DisplayDetailMobileVer">model.DisplayDetailMobileVer</h2>
<!-- backwards compatibility -->
<a id="schemamodel.displaydetailmobilever"></a>
<a id="schema_model.DisplayDetailMobileVer"></a>
<a id="tocSmodel.displaydetailmobilever"></a>
<a id="tocsmodel.displaydetailmobilever"></a>

```json
{
  "content": "string",
  "fullpath": "string",
  "fullpathtext": "string",
  "id": 0,
  "name": "string",
  "pathid": 0,
  "phoneversion": 0,
  "tabletversion": 0,
  "thumbnail": "string",
  "version": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|content|string|false|none|none|
|fullpath|string|false|none|none|
|fullpathtext|string|false|none|none|
|id|integer|false|none|none|
|name|string|false|none|none|
|pathid|integer|false|none|none|
|phoneversion|integer|false|none|none|
|tabletversion|integer|false|none|none|
|thumbnail|string|false|none|none|
|version|integer|false|none|none|

<h2 id="tocS_model.DisplayVersionInfo">model.DisplayVersionInfo</h2>
<!-- backwards compatibility -->
<a id="schemamodel.displayversioninfo"></a>
<a id="schema_model.DisplayVersionInfo"></a>
<a id="tocSmodel.displayversioninfo"></a>
<a id="tocsmodel.displayversioninfo"></a>

```json
{
  "id": 0,
  "phoneversion": 0,
  "tabletversion": 0,
  "version": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|id|integer|false|none|none|
|phoneversion|integer|false|none|none|
|tabletversion|integer|false|none|none|
|version|integer|false|none|none|

<h2 id="tocS_model.DisplayVersionRes">model.DisplayVersionRes</h2>
<!-- backwards compatibility -->
<a id="schemamodel.displayversionres"></a>
<a id="schema_model.DisplayVersionRes"></a>
<a id="tocSmodel.displayversionres"></a>
<a id="tocsmodel.displayversionres"></a>

```json
{
  "phoneversion": 0,
  "tabletversion": 0,
  "version": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|phoneversion|integer|false|none|none|
|tabletversion|integer|false|none|none|
|version|integer|false|none|none|

<h2 id="tocS_model.FCMDeviceProjectStruct">model.FCMDeviceProjectStruct</h2>
<!-- backwards compatibility -->
<a id="schemamodel.fcmdeviceprojectstruct"></a>
<a id="schema_model.FCMDeviceProjectStruct"></a>
<a id="tocSmodel.fcmdeviceprojectstruct"></a>
<a id="tocsmodel.fcmdeviceprojectstruct"></a>

```json
{
  "alarmLevel": 0,
  "deviceID": "string",
  "projectID": 0,
  "projectName": "string",
  "projectToken": "string"
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|alarmLevel|integer|false|none|none|
|deviceID|string|false|none|none|
|projectID|integer|false|none|none|
|projectName|string|false|none|none|
|projectToken|string|false|none|none|

<h2 id="tocS_model.FCMDeviceProjects">model.FCMDeviceProjects</h2>
<!-- backwards compatibility -->
<a id="schemamodel.fcmdeviceprojects"></a>
<a id="schema_model.FCMDeviceProjects"></a>
<a id="tocSmodel.fcmdeviceprojects"></a>
<a id="tocsmodel.fcmdeviceprojects"></a>

```json
{
  "continueMinute": 0,
  "dataSource": 0,
  "deviceID": "string",
  "language": "string",
  "lastUpdate": 0,
  "platform": "string",
  "projectList": [
    {
      "alarmLevel": 0,
      "deviceID": "string",
      "projectID": 0,
      "projectName": "string",
      "projectToken": "string"
    }
  ],
  "token": "string"
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|continueMinute|integer|false|none|none|
|dataSource|integer|false|none|none|
|deviceID|string|false|none|unique id of a mobile device|
|language|string|false|none|none|
|lastUpdate|integer|false|none|none|
|platform|string|false|none|none|
|projectList|[[model.FCMDeviceProjectStruct](#schemamodel.fcmdeviceprojectstruct)]|false|none|none|
|token|string|false|none|none|

<h2 id="tocS_model.FileCacheMeta">model.FileCacheMeta</h2>
<!-- backwards compatibility -->
<a id="schemamodel.filecachemeta"></a>
<a id="schema_model.FileCacheMeta"></a>
<a id="tocSmodel.filecachemeta"></a>
<a id="tocsmodel.filecachemeta"></a>

```json
{
  "filename": "string",
  "objecttype": 0,
  "parentpathid": 0,
  "projid": 0,
  "salt": "string",
  "ver": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|filename|string|false|none|none|
|objecttype|integer|false|none|none|
|parentpathid|integer|false|none|none|
|projid|integer|false|none|none|
|salt|string|false|none|none|
|ver|integer|false|none|none|

<h2 id="tocS_model.FileIDsReq">model.FileIDsReq</h2>
<!-- backwards compatibility -->
<a id="schemamodel.fileidsreq"></a>
<a id="schema_model.FileIDsReq"></a>
<a id="tocSmodel.fileidsreq"></a>
<a id="tocsmodel.fileidsreq"></a>

```json
{
  "fileids": [
    null
  ],
  "projID": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|fileids|[any]|false|none|none|
|projID|integer|false|none|none|

<h2 id="tocS_model.FileStruct">model.FileStruct</h2>
<!-- backwards compatibility -->
<a id="schemamodel.filestruct"></a>
<a id="schema_model.FileStruct"></a>
<a id="tocSmodel.filestruct"></a>
<a id="tocsmodel.filestruct"></a>

```json
{
  "filetype": "string",
  "fullpathtext": "string",
  "id": "string",
  "name": "string",
  "thumbnail": "string"
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|filetype|string|false|none|none|
|fullpathtext|string|false|none|none|
|id|string|false|none|none|
|name|string|false|none|none|
|thumbnail|string|false|none|none|

<h2 id="tocS_model.GUser">model.GUser</h2>
<!-- backwards compatibility -->
<a id="schemamodel.guser"></a>
<a id="schema_model.GUser"></a>
<a id="tocSmodel.guser"></a>
<a id="tocsmodel.guser"></a>

```json
{
  "id": 0,
  "usertype": "string"
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|id|integer|false|none|none|
|usertype|string|false|none|none|

<h2 id="tocS_model.GUserIDList">model.GUserIDList</h2>
<!-- backwards compatibility -->
<a id="schemamodel.guseridlist"></a>
<a id="schema_model.GUserIDList"></a>
<a id="tocSmodel.guseridlist"></a>
<a id="tocsmodel.guseridlist"></a>

```json
{
  "projid": 0,
  "userids": [
    0
  ]
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|projid|integer|false|none|none|
|userids|[integer]|false|none|none|

<h2 id="tocS_model.GUserProj">model.GUserProj</h2>
<!-- backwards compatibility -->
<a id="schemamodel.guserproj"></a>
<a id="schema_model.GUserProj"></a>
<a id="tocSmodel.guserproj"></a>
<a id="tocsmodel.guserproj"></a>

```json
{
  "enable": true,
  "id": 0,
  "name": "string",
  "projid": 0,
  "usertype": "string"
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|enable|boolean|false|none|none|
|id|integer|false|none|none|
|name|string|false|none|none|
|projid|integer|false|none|none|
|usertype|string|false|none|none|

<h2 id="tocS_model.GUserProjList">model.GUserProjList</h2>
<!-- backwards compatibility -->
<a id="schemamodel.guserprojlist"></a>
<a id="schema_model.GUserProjList"></a>
<a id="tocSmodel.guserprojlist"></a>
<a id="tocsmodel.guserprojlist"></a>

```json
{
  "graphicusers": [
    {
      "id": 0,
      "usertype": "string"
    }
  ],
  "projid": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|graphicusers|[[model.GUser](#schemamodel.guser)]|false|none|none|
|projid|integer|false|none|none|

<h2 id="tocS_model.GetNextLevelbyidRes">model.GetNextLevelbyidRes</h2>
<!-- backwards compatibility -->
<a id="schemamodel.getnextlevelbyidres"></a>
<a id="schema_model.GetNextLevelbyidRes"></a>
<a id="tocSmodel.getnextlevelbyidres"></a>
<a id="tocsmodel.getnextlevelbyidres"></a>

```json
{
  "objectlist": [
    {
      "displayid": 0,
      "fullpath": "string",
      "fullpathtext": "string",
      "id": 0,
      "objectid": "string",
      "objectname": "string",
      "objecttype": 0,
      "parentpathid": 0,
      "projid": 0
    }
  ]
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|objectlist|[[model.NextLevelObj](#schemamodel.nextlevelobj)]|false|none|none|

<h2 id="tocS_model.GroupDetail">model.GroupDetail</h2>
<!-- backwards compatibility -->
<a id="schemamodel.groupdetail"></a>
<a id="schema_model.GroupDetail"></a>
<a id="tocSmodel.groupdetail"></a>
<a id="tocsmodel.groupdetail"></a>

```json
{
  "id": 0,
  "projid": 0,
  "type": "string"
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|id|integer|false|none|none|
|projid|integer|false|none|none|
|type|string|false|none|none|

<h2 id="tocS_model.JSDetailRes">model.JSDetailRes</h2>
<!-- backwards compatibility -->
<a id="schemamodel.jsdetailres"></a>
<a id="schema_model.JSDetailRes"></a>
<a id="tocSmodel.jsdetailres"></a>
<a id="tocsmodel.jsdetailres"></a>

```json
{
  "fullpath": "string",
  "id": 0,
  "jscontent": "string",
  "name": "string",
  "path": "string",
  "version": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|fullpath|string|false|none|none|
|id|integer|false|none|none|
|jscontent|string|false|none|none|
|name|string|false|none|none|
|path|string|false|none|none|
|version|integer|false|none|none|

<h2 id="tocS_model.JSListObject">model.JSListObject</h2>
<!-- backwards compatibility -->
<a id="schemamodel.jslistobject"></a>
<a id="schema_model.JSListObject"></a>
<a id="tocSmodel.jslistobject"></a>
<a id="tocsmodel.jslistobject"></a>

```json
{
  "fullpath": "string",
  "fullpathtext": "string",
  "id": 0,
  "name": "string",
  "path": "string",
  "version": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|fullpath|string|false|none|none|
|fullpathtext|string|false|none|none|
|id|integer|false|none|none|
|name|string|false|none|none|
|path|string|false|none|none|
|version|integer|false|none|none|

<h2 id="tocS_model.JSPathList">model.JSPathList</h2>
<!-- backwards compatibility -->
<a id="schemamodel.jspathlist"></a>
<a id="schema_model.JSPathList"></a>
<a id="tocSmodel.jspathlist"></a>
<a id="tocsmodel.jspathlist"></a>

```json
{
  "fullpathtext": "string",
  "id": 0,
  "name": "string"
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|fullpathtext|string|false|none|none|
|id|integer|false|none|none|
|name|string|false|none|none|

<h2 id="tocS_model.JSVersionObj">model.JSVersionObj</h2>
<!-- backwards compatibility -->
<a id="schemamodel.jsversionobj"></a>
<a id="schema_model.JSVersionObj"></a>
<a id="tocSmodel.jsversionobj"></a>
<a id="tocsmodel.jsversionobj"></a>

```json
{
  "id": 0,
  "version": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|id|integer|false|none|none|
|version|integer|false|none|none|

<h2 id="tocS_model.LimitPath">model.LimitPath</h2>
<!-- backwards compatibility -->
<a id="schemamodel.limitpath"></a>
<a id="schema_model.LimitPath"></a>
<a id="tocSmodel.limitpath"></a>
<a id="tocsmodel.limitpath"></a>

```json
{
  "extensions": [
    "string"
  ],
  "objectIds": [
    "string"
  ],
  "objectType": 0,
  "pathIds": [
    0
  ]
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|extensions|[string]|false|none|none|
|objectIds|[string]|false|none|none|
|objectType|integer|false|none|none|
|pathIds|[integer]|false|none|none|

<h2 id="tocS_model.MQTTBrokerSetting">model.MQTTBrokerSetting</h2>
<!-- backwards compatibility -->
<a id="schemamodel.mqttbrokersetting"></a>
<a id="schema_model.MQTTBrokerSetting"></a>
<a id="tocSmodel.mqttbrokersetting"></a>
<a id="tocsmodel.mqttbrokersetting"></a>

```json
{
  "host": "string",
  "keepalive": 0,
  "password": "string",
  "tcpport": 0,
  "tlsport": 0,
  "username": "string",
  "websocketport": 0,
  "websockettlsport": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|host|string|false|none|none|
|keepalive|integer|false|none|none|
|password|string|false|none|none|
|tcpport|integer|false|none|none|
|tlsport|integer|false|none|none|
|username|string|false|none|none|
|websocketport|integer|false|none|none|
|websockettlsport|integer|false|none|none|

<h2 id="tocS_model.MoveObjectReq">model.MoveObjectReq</h2>
<!-- backwards compatibility -->
<a id="schemamodel.moveobjectreq"></a>
<a id="schema_model.MoveObjectReq"></a>
<a id="tocSmodel.moveobjectreq"></a>
<a id="tocsmodel.moveobjectreq"></a>

```json
{
  "pathid": 0,
  "targetpathid": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|pathid|integer|false|none|none|
|targetpathid|integer|false|none|none|

<h2 id="tocS_model.NextLevelObj">model.NextLevelObj</h2>
<!-- backwards compatibility -->
<a id="schemamodel.nextlevelobj"></a>
<a id="schema_model.NextLevelObj"></a>
<a id="tocSmodel.nextlevelobj"></a>
<a id="tocsmodel.nextlevelobj"></a>

```json
{
  "displayid": 0,
  "fullpath": "string",
  "fullpathtext": "string",
  "id": 0,
  "objectid": "string",
  "objectname": "string",
  "objecttype": 0,
  "parentpathid": 0,
  "projid": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|displayid|integer|false|none|none|
|fullpath|string|false|none|none|
|fullpathtext|string|false|none|none|
|id|integer|false|none|none|
|objectid|string|false|none|none|
|objectname|string|false|none|none|
|objecttype|integer|false|none|none|
|parentpathid|integer|false|none|none|
|projid|integer|false|none|none|

<h2 id="tocS_model.ObjectExport">model.ObjectExport</h2>
<!-- backwards compatibility -->
<a id="schemamodel.objectexport"></a>
<a id="schema_model.ObjectExport"></a>
<a id="tocSmodel.objectexport"></a>
<a id="tocsmodel.objectexport"></a>

```json
{
  "globalvaris": [
    "string"
  ],
  "importpw": "string",
  "pathids": [
    0
  ],
  "projid": 0,
  "recordids": [
    0
  ]
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|globalvaris|[string]|false|none|none|
|importpw|string|false|none|none|
|pathids|[integer]|false|none|none|
|projid|integer|false|none|none|
|recordids|[integer]|false|none|none|

<h2 id="tocS_model.PluginFileDrift">model.PluginFileDrift</h2>
<!-- backwards compatibility -->
<a id="schemamodel.pluginfiledrift"></a>
<a id="schema_model.PluginFileDrift"></a>
<a id="tocSmodel.pluginfiledrift"></a>
<a id="tocsmodel.pluginfiledrift"></a>

```json
{
  "id": 0,
  "name": "string",
  "type": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|id|integer|false|none|none|
|name|string|false|none|none|
|type|integer|false|none|none|

<h2 id="tocS_model.ProjIDList">model.ProjIDList</h2>
<!-- backwards compatibility -->
<a id="schemamodel.projidlist"></a>
<a id="schema_model.ProjIDList"></a>
<a id="tocSmodel.projidlist"></a>
<a id="tocsmodel.projidlist"></a>

```json
{
  "projids": [
    0
  ]
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|projids|[integer]|false|none|none|

<h2 id="tocS_model.QuerySearchUnderPath">model.QuerySearchUnderPath</h2>
<!-- backwards compatibility -->
<a id="schemamodel.querysearchunderpath"></a>
<a id="schema_model.QuerySearchUnderPath"></a>
<a id="tocSmodel.querysearchunderpath"></a>
<a id="tocsmodel.querysearchunderpath"></a>

```json
{
  "limit": [
    {
      "extensions": [
        "string"
      ],
      "objectIds": [
        "string"
      ],
      "objectType": 0,
      "pathIds": [
        0
      ]
    }
  ],
  "searchName": "string"
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|limit|[[model.LimitPath](#schemamodel.limitpath)]|false|none|none|
|searchName|string|false|none|none|

<h2 id="tocS_model.ReNameReq">model.ReNameReq</h2>
<!-- backwards compatibility -->
<a id="schemamodel.renamereq"></a>
<a id="schema_model.ReNameReq"></a>
<a id="tocSmodel.renamereq"></a>
<a id="tocsmodel.renamereq"></a>

```json
{
  "name": "string",
  "pathid": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|name|string|false|none|none|
|pathid|integer|false|none|none|

<h2 id="tocS_model.RecipeChange">model.RecipeChange</h2>
<!-- backwards compatibility -->
<a id="schemamodel.recipechange"></a>
<a id="schema_model.RecipeChange"></a>
<a id="tocSmodel.recipechange"></a>
<a id="tocsmodel.recipechange"></a>

```json
{
  "name": "string",
  "projid": 0,
  "recipevalue": [
    {
      "item": "string",
      "name": "string",
      "projid": 0,
      "seqnbr": 0,
      "value": "string"
    }
  ]
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|name|string|false|none|none|
|projid|integer|false|none|none|
|recipevalue|[[model.RecipeValue](#schemamodel.recipevalue)]|false|none|none|

<h2 id="tocS_model.RecipeDelete">model.RecipeDelete</h2>
<!-- backwards compatibility -->
<a id="schemamodel.recipedelete"></a>
<a id="schema_model.RecipeDelete"></a>
<a id="tocSmodel.recipedelete"></a>
<a id="tocsmodel.recipedelete"></a>

```json
{
  "recipe": [
    {
      "name": "string",
      "projid": 0
    }
  ]
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|recipe|[[model.RecipeID](#schemamodel.recipeid)]|false|none|none|

<h2 id="tocS_model.RecipeID">model.RecipeID</h2>
<!-- backwards compatibility -->
<a id="schemamodel.recipeid"></a>
<a id="schema_model.RecipeID"></a>
<a id="tocSmodel.recipeid"></a>
<a id="tocsmodel.recipeid"></a>

```json
{
  "name": "string",
  "projid": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|name|string|false|none|none|
|projid|integer|false|none|none|

<h2 id="tocS_model.RecipeListStruct">model.RecipeListStruct</h2>
<!-- backwards compatibility -->
<a id="schemamodel.recipeliststruct"></a>
<a id="schema_model.RecipeListStruct"></a>
<a id="tocSmodel.recipeliststruct"></a>
<a id="tocsmodel.recipeliststruct"></a>

```json
{
  "description": "string",
  "dwarea": 0,
  "dwlevel": 0,
  "editarea": 0,
  "editlevel": 0,
  "name": "string",
  "projid": 0,
  "readonly": true
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|description|string|false|none|none|
|dwarea|integer|false|none|none|
|dwlevel|integer|false|none|none|
|editarea|integer|false|none|none|
|editlevel|integer|false|none|none|
|name|string|false|none|none|
|projid|integer|false|none|none|
|readonly|boolean|false|none|none|

<h2 id="tocS_model.RecipeStruct">model.RecipeStruct</h2>
<!-- backwards compatibility -->
<a id="schemamodel.recipestruct"></a>
<a id="schema_model.RecipeStruct"></a>
<a id="tocSmodel.recipestruct"></a>
<a id="tocsmodel.recipestruct"></a>

```json
{
  "description": "string",
  "dwarea": 0,
  "dwlevel": 0,
  "editarea": 0,
  "editlevel": 0,
  "name": "string",
  "oriname": "string",
  "projid": 0,
  "readonly": true,
  "recipeunit": {
    "property1": [
      {
        "name": "string",
        "projid": 0,
        "seqnbr": 0,
        "tagname": "string",
        "unit": "string"
      }
    ],
    "property2": [
      {
        "name": "string",
        "projid": 0,
        "seqnbr": 0,
        "tagname": "string",
        "unit": "string"
      }
    ]
  },
  "recipevalue": [
    {
      "item": "string",
      "name": "string",
      "projid": 0,
      "seqnbr": 0,
      "value": "string"
    }
  ],
  "tolerance": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|description|string|false|none|none|
|dwarea|integer|false|none|none|
|dwlevel|integer|false|none|none|
|editarea|integer|false|none|none|
|editlevel|integer|false|none|none|
|name|string|false|none|none|
|oriname|string|false|none|none|
|projid|integer|false|none|none|
|readonly|boolean|false|none|none|
|recipeunit|object|false|none|none|
|» **additionalProperties**|[[model.RecipeUnit](#schemamodel.recipeunit)]|false|none|none|
|recipevalue|[[model.RecipeValue](#schemamodel.recipevalue)]|false|none|none|
|tolerance|number|false|none|none|

<h2 id="tocS_model.RecipeUnit">model.RecipeUnit</h2>
<!-- backwards compatibility -->
<a id="schemamodel.recipeunit"></a>
<a id="schema_model.RecipeUnit"></a>
<a id="tocSmodel.recipeunit"></a>
<a id="tocsmodel.recipeunit"></a>

```json
{
  "name": "string",
  "projid": 0,
  "seqnbr": 0,
  "tagname": "string",
  "unit": "string"
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|name|string|false|none|none|
|projid|integer|false|none|none|
|seqnbr|integer|false|none|none|
|tagname|string|false|none|none|
|unit|string|false|none|none|

<h2 id="tocS_model.RecipeValue">model.RecipeValue</h2>
<!-- backwards compatibility -->
<a id="schemamodel.recipevalue"></a>
<a id="schema_model.RecipeValue"></a>
<a id="tocSmodel.recipevalue"></a>
<a id="tocsmodel.recipevalue"></a>

```json
{
  "item": "string",
  "name": "string",
  "projid": 0,
  "seqnbr": 0,
  "value": "string"
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|item|string|false|none|none|
|name|string|false|none|none|
|projid|integer|false|none|none|
|seqnbr|integer|false|none|none|
|value|string|false|none|none|

<h2 id="tocS_model.RecordDelete">model.RecordDelete</h2>
<!-- backwards compatibility -->
<a id="schemamodel.recorddelete"></a>
<a id="schema_model.RecordDelete"></a>
<a id="tocSmodel.recorddelete"></a>
<a id="tocsmodel.recorddelete"></a>

```json
{
  "ids": [
    0
  ]
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|ids|[integer]|false|none|none|

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

<h2 id="tocS_model.SecurityDetail">model.SecurityDetail</h2>
<!-- backwards compatibility -->
<a id="schemamodel.securitydetail"></a>
<a id="schema_model.SecurityDetail"></a>
<a id="tocSmodel.securitydetail"></a>
<a id="tocsmodel.securitydetail"></a>

```json
{
  "area": [
    0
  ],
  "descript": "string",
  "id": 0,
  "name": "string",
  "projid": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|area|[integer]|false|none|none|
|descript|string|false|none|none|
|id|integer|false|none|none|
|name|string|false|none|none|
|projid|integer|false|none|none|

<h2 id="tocS_model.TagConfigBody">model.TagConfigBody</h2>
<!-- backwards compatibility -->
<a id="schemamodel.tagconfigbody"></a>
<a id="schema_model.TagConfigBody"></a>
<a id="tocSmodel.tagconfigbody"></a>
<a id="tocsmodel.tagconfigbody"></a>

```json
{
  "displaydec": 0,
  "displayint": 0,
  "projid": 0,
  "spanhi": 0,
  "spanlo": 0,
  "type": "string"
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|displaydec|integer|false|none|none|
|displayint|integer|false|none|none|
|projid|integer|false|none|none|
|spanhi|number|false|none|none|
|spanlo|number|false|none|none|
|type|string|false|none|none|

<h2 id="tocS_model.TagDelete">model.TagDelete</h2>
<!-- backwards compatibility -->
<a id="schemamodel.tagdelete"></a>
<a id="schema_model.TagDelete"></a>
<a id="tocSmodel.tagdelete"></a>
<a id="tocsmodel.tagdelete"></a>

```json
{
  "connectname": "string",
  "projid": 0,
  "tagnames": [
    "string"
  ]
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|connectname|string|false|none|none|
|projid|integer|false|none|none|
|tagnames|[string]|false|none|none|

<h2 id="tocS_model.TagDetail">model.TagDetail</h2>
<!-- backwards compatibility -->
<a id="schemamodel.tagdetail"></a>
<a id="schema_model.TagDetail"></a>
<a id="tocSmodel.tagdetail"></a>
<a id="tocsmodel.tagdetail"></a>

```json
{
  "connectname": "string",
  "createtimestamp": "string",
  "dataType": "string",
  "description": "string",
  "displaydec": 0,
  "displayint": 0,
  "modifytimestamp": "string",
  "nodeid": "string",
  "projid": 0,
  "spanhi": 0,
  "spanlo": 0,
  "tagname": "string"
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|connectname|string|false|none|none|
|createtimestamp|string|false|none|none|
|dataType|string|false|none|none|
|description|string|false|none|none|
|displaydec|integer|false|none|none|
|displayint|integer|false|none|none|
|modifytimestamp|string|false|none|none|
|nodeid|string|false|none|none|
|projid|integer|false|none|none|
|spanhi|number|false|none|none|
|spanlo|number|false|none|none|
|tagname|string|false|none|none|

<h2 id="tocS_model.TagListFilter">model.TagListFilter</h2>
<!-- backwards compatibility -->
<a id="schemamodel.taglistfilter"></a>
<a id="schema_model.TagListFilter"></a>
<a id="tocSmodel.taglistfilter"></a>
<a id="tocsmodel.taglistfilter"></a>

```json
{
  "connname": "string",
  "getfromserver": true
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|connname|string|false|none|none|
|getfromserver|boolean|false|none|none|

<h2 id="tocS_model.TagMultiInsert">model.TagMultiInsert</h2>
<!-- backwards compatibility -->
<a id="schemamodel.tagmultiinsert"></a>
<a id="schema_model.TagMultiInsert"></a>
<a id="tocSmodel.tagmultiinsert"></a>
<a id="tocsmodel.tagmultiinsert"></a>

```json
{
  "tags": [
    {
      "connectname": "string",
      "createtimestamp": "string",
      "dataType": "string",
      "description": "string",
      "displaydec": 0,
      "displayint": 0,
      "modifytimestamp": "string",
      "nodeid": "string",
      "projid": 0,
      "spanhi": 0,
      "spanlo": 0,
      "tagname": "string"
    }
  ]
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|tags|[[model.TagDetail](#schemamodel.tagdetail)]|false|none|none|

<h2 id="tocS_model.TagPage">model.TagPage</h2>
<!-- backwards compatibility -->
<a id="schemamodel.tagpage"></a>
<a id="schema_model.TagPage"></a>
<a id="tocSmodel.tagpage"></a>
<a id="tocsmodel.tagpage"></a>

```json
{
  "CurrentPage": 0,
  "TagName": "string",
  "TagType": [
    "string"
  ],
  "pageSize": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|CurrentPage|integer|false|none|none|
|TagName|string|false|none|none|
|TagType|[string]|false|none|none|
|pageSize|integer|false|none|none|

<h2 id="tocS_model.TagPath">model.TagPath</h2>
<!-- backwards compatibility -->
<a id="schemamodel.tagpath"></a>
<a id="schema_model.TagPath"></a>
<a id="tocSmodel.tagpath"></a>
<a id="tocsmodel.tagpath"></a>

```json
{
  "connname": "string",
  "nodeid": "string"
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|connname|string|false|none|none|
|nodeid|string|false|none|none|

<h2 id="tocS_model.TemplateDetail">model.TemplateDetail</h2>
<!-- backwards compatibility -->
<a id="schemamodel.templatedetail"></a>
<a id="schema_model.TemplateDetail"></a>
<a id="tocSmodel.templatedetail"></a>
<a id="tocsmodel.templatedetail"></a>

```json
{
  "content": "string",
  "id": 0,
  "name": "string",
  "projid": 0,
  "version": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|content|string|false|none|none|
|id|integer|false|none|none|
|name|string|false|none|none|
|projid|integer|false|none|none|
|version|integer|false|none|none|

<h2 id="tocS_model.TemplateIDName">model.TemplateIDName</h2>
<!-- backwards compatibility -->
<a id="schemamodel.templateidname"></a>
<a id="schema_model.TemplateIDName"></a>
<a id="tocSmodel.templateidname"></a>
<a id="tocsmodel.templateidname"></a>

```json
{
  "id": 0,
  "name": "string"
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|id|integer|false|none|none|
|name|string|false|none|none|

<h2 id="tocS_model.Tenant">model.Tenant</h2>
<!-- backwards compatibility -->
<a id="schemamodel.tenant"></a>
<a id="schema_model.Tenant"></a>
<a id="tocSmodel.tenant"></a>
<a id="tocsmodel.tenant"></a>

```json
{
  "__typename": "string",
  "defaultpage": "string",
  "depth": 0,
  "description": "string",
  "deviceOnBiRootOrgId": "string",
  "id": "string",
  "name": "string",
  "pathid": 0,
  "projid": 0,
  "usertype": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|__typename|string|false|none|none|
|defaultpage|string|false|none|none|
|depth|integer|false|none|none|
|description|string|false|none|none|
|deviceOnBiRootOrgId|string|false|none|none|
|id|string|false|none|none|
|name|string|false|none|none|
|pathid|integer|false|none|none|
|projid|integer|false|none|none|
|usertype|integer|false|none|none|

<h2 id="tocS_model.TtsAudio">model.TtsAudio</h2>
<!-- backwards compatibility -->
<a id="schemamodel.ttsaudio"></a>
<a id="schema_model.TtsAudio"></a>
<a id="tocSmodel.ttsaudio"></a>
<a id="tocsmodel.ttsaudio"></a>

```json
{
  "model": "string",
  "name": "string",
  "parentpathid": 0,
  "text": "string"
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|model|string|false|none|none|
|name|string|false|none|none|
|parentpathid|integer|false|none|none|
|text|string|false|none|none|

<h2 id="tocS_model.UploadAudioRes">model.UploadAudioRes</h2>
<!-- backwards compatibility -->
<a id="schemamodel.uploadaudiores"></a>
<a id="schema_model.UploadAudioRes"></a>
<a id="tocSmodel.uploadaudiores"></a>
<a id="tocsmodel.uploadaudiores"></a>

```json
{
  "id": 0,
  "name": "string",
  "pathid": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|id|integer|false|none|none|
|name|string|false|none|none|
|pathid|integer|false|none|none|

<h2 id="tocS_model.UploadImageRes">model.UploadImageRes</h2>
<!-- backwards compatibility -->
<a id="schemamodel.uploadimageres"></a>
<a id="schema_model.UploadImageRes"></a>
<a id="tocSmodel.uploadimageres"></a>
<a id="tocsmodel.uploadimageres"></a>

```json
{
  "id": 0,
  "name": "string",
  "pathid": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|id|integer|false|none|none|
|name|string|false|none|none|
|pathid|integer|false|none|none|

<h2 id="tocS_model.UserAccess">model.UserAccess</h2>
<!-- backwards compatibility -->
<a id="schemamodel.useraccess"></a>
<a id="schema_model.UserAccess"></a>
<a id="tocSmodel.useraccess"></a>
<a id="tocsmodel.useraccess"></a>

```json
{
  "ack": true,
  "change": true,
  "displayid": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|ack|boolean|false|none|none|
|change|boolean|false|none|none|
|displayid|integer|false|none|none|

<h2 id="tocS_model.UserDelete">model.UserDelete</h2>
<!-- backwards compatibility -->
<a id="schemamodel.userdelete"></a>
<a id="schema_model.UserDelete"></a>
<a id="tocSmodel.userdelete"></a>
<a id="tocsmodel.userdelete"></a>

```json
{
  "name": "string",
  "projid": 0,
  "thirdsource": true
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|name|string|false|none|none|
|projid|integer|false|none|none|
|thirdsource|boolean|false|none|none|

<h2 id="tocS_model.UserEnable">model.UserEnable</h2>
<!-- backwards compatibility -->
<a id="schemamodel.userenable"></a>
<a id="schema_model.UserEnable"></a>
<a id="tocSmodel.userenable"></a>
<a id="tocsmodel.userenable"></a>

```json
{
  "enable": true,
  "userid": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|enable|boolean|false|none|none|
|userid|integer|false|none|none|

<h2 id="tocS_model.UserFunc">model.UserFunc</h2>
<!-- backwards compatibility -->
<a id="schemamodel.userfunc"></a>
<a id="schema_model.UserFunc"></a>
<a id="tocSmodel.userfunc"></a>
<a id="tocsmodel.userfunc"></a>

```json
{
  "edit": true,
  "function": "string",
  "id": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|edit|boolean|false|none|none|
|function|string|false|none|none|
|id|integer|false|none|none|

<h2 id="tocS_model.UserIDList">model.UserIDList</h2>
<!-- backwards compatibility -->
<a id="schemamodel.useridlist"></a>
<a id="schema_model.UserIDList"></a>
<a id="tocSmodel.useridlist"></a>
<a id="tocsmodel.useridlist"></a>

```json
{
  "userids": [
    0
  ]
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|userids|[integer]|false|none|none|

<h2 id="tocS_model.UserList">model.UserList</h2>
<!-- backwards compatibility -->
<a id="schemamodel.userlist"></a>
<a id="schema_model.UserList"></a>
<a id="tocSmodel.userlist"></a>
<a id="tocsmodel.userlist"></a>

```json
{
  "homename": "string",
  "modifytime": "string",
  "name": "string",
  "usertype": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|homename|string|false|none|none|
|modifytime|string|false|none|none|
|name|string|false|none|none|
|usertype|integer|false|none|none|

<h2 id="tocS_model.UserProj">model.UserProj</h2>
<!-- backwards compatibility -->
<a id="schemamodel.userproj"></a>
<a id="schema_model.UserProj"></a>
<a id="tocSmodel.userproj"></a>
<a id="tocsmodel.userproj"></a>

```json
{
  "projid": 0,
  "usertype": "string"
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|projid|integer|false|none|none|
|usertype|string|false|none|none|

<h2 id="tocS_model.UserProjNamePass">model.UserProjNamePass</h2>
<!-- backwards compatibility -->
<a id="schemamodel.userprojnamepass"></a>
<a id="schema_model.UserProjNamePass"></a>
<a id="tocSmodel.userprojnamepass"></a>
<a id="tocsmodel.userprojnamepass"></a>

```json
{
  "name": "string",
  "password": "string",
  "projid": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|name|string|false|none|none|
|password|string|false|none|none|
|projid|integer|false|none|none|

<h2 id="tocS_model.UserUpdate">model.UserUpdate</h2>
<!-- backwards compatibility -->
<a id="schemamodel.userupdate"></a>
<a id="schema_model.UserUpdate"></a>
<a id="tocSmodel.userupdate"></a>
<a id="tocsmodel.userupdate"></a>

```json
{
  "access": [
    {
      "ack": true,
      "change": true,
      "displayid": 0
    }
  ],
  "ack": true,
  "area": [
    0
  ],
  "change": true,
  "home": 0,
  "name": "string",
  "password": "string",
  "projid": 0,
  "sourceuserid": "string",
  "thirdsource": true,
  "updatepw": true,
  "usertype": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|access|[[model.UserAccess](#schemamodel.useraccess)]|false|none|none|
|ack|boolean|false|none|none|
|area|[integer]|false|none|none|
|change|boolean|false|none|none|
|home|integer|false|none|none|
|name|string|false|none|none|
|password|string|false|none|none|
|projid|integer|false|none|none|
|sourceuserid|string|false|none|none|
|thirdsource|boolean|false|none|none|
|updatepw|boolean|false|none|none|
|usertype|integer|false|none|none|

<h2 id="tocS_model.UserUpdatePW">model.UserUpdatePW</h2>
<!-- backwards compatibility -->
<a id="schemamodel.userupdatepw"></a>
<a id="schema_model.UserUpdatePW"></a>
<a id="tocSmodel.userupdatepw"></a>
<a id="tocsmodel.userupdatepw"></a>

```json
{
  "newpassword": "string",
  "oldpassword": "string"
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|newpassword|string|false|none|none|
|oldpassword|string|false|none|none|

<h2 id="tocS_model.VariImport">model.VariImport</h2>
<!-- backwards compatibility -->
<a id="schemamodel.variimport"></a>
<a id="schema_model.VariImport"></a>
<a id="tocSmodel.variimport"></a>
<a id="tocsmodel.variimport"></a>

```json
{
  "displayid": 0,
  "projid": 0,
  "variables": [
    {
      "area": 0,
      "consttype": "string",
      "decpl": 0,
      "displayid": 0,
      "displayname": "string",
      "event": true,
      "info": {},
      "interval": 0,
      "label": "string",
      "level": 0,
      "name": "string",
      "projid": 0,
      "regex": "string",
      "sequence": 0,
      "spanhi": 0,
      "spanlo": 0,
      "type": "string",
      "value": "string"
    }
  ]
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|displayid|integer|false|none|none|
|projid|integer|false|none|none|
|variables|[[model.VariableStruct](#schemamodel.variablestruct)]|false|none|none|

<h2 id="tocS_model.VariableDelete">model.VariableDelete</h2>
<!-- backwards compatibility -->
<a id="schemamodel.variabledelete"></a>
<a id="schema_model.VariableDelete"></a>
<a id="tocSmodel.variabledelete"></a>
<a id="tocsmodel.variabledelete"></a>

```json
{
  "variables": [
    {
      "displayid": 0,
      "name": "string",
      "projid": 0
    }
  ]
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|variables|[object]|false|none|none|
|» displayid|integer|false|none|none|
|» name|string|false|none|none|
|» projid|integer|false|none|none|

<h2 id="tocS_model.VariableExchange">model.VariableExchange</h2>
<!-- backwards compatibility -->
<a id="schemamodel.variableexchange"></a>
<a id="schema_model.VariableExchange"></a>
<a id="tocSmodel.variableexchange"></a>
<a id="tocsmodel.variableexchange"></a>

```json
{
  "displayid": 0,
  "name": "string",
  "no": 0,
  "projid": 0
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|displayid|integer|false|none|none|
|name|string|false|none|none|
|no|integer|false|none|none|
|projid|integer|false|none|none|

<h2 id="tocS_model.VariableStruct">model.VariableStruct</h2>
<!-- backwards compatibility -->
<a id="schemamodel.variablestruct"></a>
<a id="schema_model.VariableStruct"></a>
<a id="tocSmodel.variablestruct"></a>
<a id="tocsmodel.variablestruct"></a>

```json
{
  "area": 0,
  "consttype": "string",
  "decpl": 0,
  "displayid": 0,
  "displayname": "string",
  "event": true,
  "info": {},
  "interval": 0,
  "label": "string",
  "level": 0,
  "name": "string",
  "projid": 0,
  "regex": "string",
  "sequence": 0,
  "spanhi": 0,
  "spanlo": 0,
  "type": "string",
  "value": "string"
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|area|integer|false|none|none|
|consttype|string|false|none|none|
|decpl|integer|false|none|none|
|displayid|integer|false|none|none|
|displayname|string|false|none|none|
|event|boolean|false|none|none|
|info|object|false|none|none|
|interval|integer|false|none|none|
|label|string|false|none|none|
|level|integer|false|none|none|
|name|string|false|none|none|
|projid|integer|false|none|none|
|regex|string|false|none|none|
|sequence|integer|false|none|none|
|spanhi|number|false|none|none|
|spanlo|number|false|none|none|
|type|string|false|none|none|
|value|string|false|none|none|

<h2 id="tocS_model.VariableUpdate">model.VariableUpdate</h2>
<!-- backwards compatibility -->
<a id="schemamodel.variableupdate"></a>
<a id="schema_model.VariableUpdate"></a>
<a id="tocSmodel.variableupdate"></a>
<a id="tocsmodel.variableupdate"></a>

```json
{
  "area": 0,
  "consttype": "string",
  "decpl": 0,
  "displayid": 0,
  "event": true,
  "info": {},
  "interval": 0,
  "label": "string",
  "level": 0,
  "name": "string",
  "oriname": "string",
  "projid": 0,
  "regex": "string",
  "spanhi": 0,
  "spanlo": 0,
  "type": "string",
  "value": "string"
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|area|integer|false|none|none|
|consttype|string|false|none|none|
|decpl|integer|false|none|none|
|displayid|integer|false|none|none|
|event|boolean|false|none|none|
|info|object|false|none|none|
|interval|integer|false|none|none|
|label|string|false|none|none|
|level|integer|false|none|none|
|name|string|false|none|none|
|oriname|string|false|none|none|
|projid|integer|false|none|none|
|regex|string|false|none|none|
|spanhi|number|false|none|none|
|spanlo|number|false|none|none|
|type|string|false|none|none|
|value|string|false|none|none|

<h2 id="tocS_service.ProductInfo">service.ProductInfo</h2>
<!-- backwards compatibility -->
<a id="schemaservice.productinfo"></a>
<a id="schema_service.ProductInfo"></a>
<a id="tocSservice.productinfo"></a>
<a id="tocsservice.productinfo"></a>

```json
{
  "dataSource": "string",
  "serialNumber": "string",
  "validUntil": "string",
  "version": "string"
}

```

### Properties

|Name|Type|Required|Restrictions|Description|
|---|---|---|---|---|
|dataSource|string|false|none|none|
|serialNumber|string|false|none|none|
|validUntil|string|false|none|none|
|version|string|false|none|none|

