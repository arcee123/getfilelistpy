getfilelistpy
====

[![MIT License](http://img.shields.io/badge/license-MIT-blue.svg?style=flat)](LICENCE)

<a name="TOP"></a>
# Overview
This is a python library to retrieve the file list with the folder tree from the specific folder of Google Drive.

# Description
When I create applications for using Google Drive, I often retrieve a file list from a folder in the application. So far, I had created the script for retrieving a file list from a folder for each application. Recently, I thought that if there is the script for retrieving the file list with the folder tree from the folder of Google Drive as a library, it will be useful for me and other users. So I created this.

## Features
- This library retrieves all files from a folder in Google Drive.
- All files include the folder structure in Google Drive.
- Only folder tree can be also retrieved.

# Install
~~~
$ pip install getfilelistpy
~~~

You can also check this library at [https://pypi.org/project/getfilelistpy/](https://pypi.org/project/getfilelistpy/).

# Method
| Method | Explanation |
|:---|:---|
| GetFolderTree(object) | Retrieve only folder structure from a folder |
| GetFileList(object) | Retrieve file list with folder structure from a folder |

# Usage
There are 3 patterns for using this library.

## 1. Use API key
This is a sample script using API key. When you want to retrieve the API key, please do the following flow.

1. Login to Google.
2. Access to [https://console.cloud.google.com/?hl=en](https://console.cloud.google.com/?hl=en).
3. Click select project at the right side of "Google Cloud Platform" of upper left of window.
4. Click "NEW PROJECT"
    1. Input "Project Name".
    2. Click "CREATE".
    3. Open the created project.
    4. Click "Enable APIs and get credentials like keys".
    5. Click "Library" at left side.
    6. Input "Drive API" in "Search for APIs & Services".
    7. Click "Google Drive API".
    8. Click "ENABLE".
    9. Back to [https://console.cloud.google.com/?hl=en](https://console.cloud.google.com/?hl=en).
    10. Click "Enable APIs and get credentials like keys".
    11. Click "Credentials" at left side.
    12. Click "Create credentials" and select API key.
    13. Copy the API key. You can use this API key.

### Sample script
~~~python
from getfilelistpy import getfilelist

resource = {
    "api_key": "#####",
    "id": "### Folder ID ###",
    "fields": "files(name,id)",
}
res = getfilelist.GetFileList(resource)  # or r = getfilelist.GetFolderTree(resource)
print(res)
~~~

### Note
- **When you want to retrieve the file list from the folder using API key, the folder is required to be shared.**
- You can modify the property of ``fields``. When this is not used, the default fields are used.

## 2. Use OAuth2
Document of OAuth2 is [here](https://developers.google.com/identity/protocols/OAuth2).

### Sample script
In this sample script, the process of OAuth2 uses [the quickstart of Google](https://developers.google.com/drive/api/v3/quickstart/python). Please check this.

~~~python
from httplib2 import Http
from oauth2client import file, client, tools
from getfilelistpy import getfilelist

SCOPES = 'https://www.googleapis.com/auth/drive.metadata.readonly'

store = file.Storage('token.json')
creds = store.get()
if not creds or creds.invalid:
    flow = client.flow_from_clientsecrets('client_secret.json', SCOPES)
    creds = tools.run_flow(flow, store)

resource = {
    "oauth2": creds.authorize(Http()),
    "id": "### Folder ID ###",
    "fields": "files(name,id)",
}
res = getfilelist.GetFileList(resource)  # or r = getfilelist.GetFolderTree(resource)
print(res)
~~~

### Note
- Here, as a sample, the script of the authorization uses the script of [quickstart](https://developers.google.com/drive/api/v3/quickstart/python).
- You can modify the property of ``fields``. When this is not used, the default fields are used.


## 3. Use Service account
Document of Service account is [here](https://developers.google.com/identity/protocols/OAuth2ServiceAccount).

### Sample script
~~~python
from google.oauth2 import service_account
from getfilelistpy import getfilelist

SCOPES = ['https://www.googleapis.com/auth/drive']
SERVICE_ACCOUNT_FILE = './so12-c78fff17f4a7.json'
credentials = service_account.Credentials.from_service_account_file(SERVICE_ACCOUNT_FILE, scopes=SCOPES)

resource = {
    "service_account": credentials,
    "id": "### Folder ID ###",
    "fields": "files(name,id)",
}
res = getfilelist.GetFileList(resource)  # or r = getfilelist.GetFolderTree(resource)
print(res)
~~~

### Note
- You can modify the property of ``fields``. When this is not used, the default fields are used.


# Values
![](images/downloadFolder_sample.png)

As a sample, when the values are retrieved from above structure, the results of ``GetFolderTree()`` becomes as follows.

## Values retrieved by GetFolderTree()
~~~python
res = getfilelist.GetFolderTree(resource)
print(res)
~~~

~~~json
{
  "id": [
    ["folderIdOfsampleFolder1"],
    ["folderIdOfsampleFolder1","folderIdOfsampleFolder_2a"],
    ["folderIdOfsampleFolder1","folderIdOfsampleFolder_2b"],
    ["folderIdOfsampleFolder1","folderIdOfsampleFolder_2a","folderIdOfsampleFolder_2a_3a"],
    ["folderIdOfsampleFolder1","folderIdOfsampleFolder_2b","folderIdOfsampleFolder_2b_3a"],
    ["folderIdOfsampleFolder1","folderIdOfsampleFolder_2b","folderIdOfsampleFolder_2b_3b"],
    ["folderIdOfsampleFolder1","folderIdOfsampleFolder_2b","folderIdOfsampleFolder_2b_3b","folderIdOfsampleFolder_2b_3b_4a"]
  ],
  "names": [
    "sampleFolder1",
    "sampleFolder_2a",
    "sampleFolder_2b",
    "sampleFolder_2a_3a",
    "sampleFolder_2b_3a",
    "sampleFolder_2b_3b",
    "sampleFolder_2b_3b_4a"
  ],
  "folders": [
    "folderIdOfsampleFolder1",
    "folderIdOfsampleFolder_2a",
    "folderIdOfsampleFolder_2b",
    "folderIdOfsampleFolder_2a_3a",
    "folderIdOfsampleFolder_2b_3a",
    "folderIdOfsampleFolder_2b_3b",
    "folderIdOfsampleFolder_2b_3b_4a"
  ]
}
~~~

## Values retrieved by Do()
~~~python
res = getfilelist.GetFileList(resource)
print(res)
~~~

~~~json
{
  "searchedFolder": {
    "id": "###",
    "name": "sampleFolder1",
    "mimeType": "application/vnd.google-apps.folder",
    "parents": ["###"],
    "createdTime": "2000-01-01T01:23:45.000Z",
    "modifiedTime": "2000-01-01T01:23:45.000Z",
    "webViewLink": "https://drive.google.com/drive/folders/###",
    "owners": [{"displayName": "###","permissionId": "###","emailAddress": "###"}],
    "shared": true
  },
  "folderTree": {
    "id": [
      ["folderIdOfsampleFolder1"],
      ["folderIdOfsampleFolder1","folderIdOfsampleFolder_2a"],
      ["folderIdOfsampleFolder1","folderIdOfsampleFolder_2b"],
      ["folderIdOfsampleFolder1","folderIdOfsampleFolder_2a","folderIdOfsampleFolder_2a_3a"],
      ["folderIdOfsampleFolder1","folderIdOfsampleFolder_2b","folderIdOfsampleFolder_2b_3a"],
      ["folderIdOfsampleFolder1","folderIdOfsampleFolder_2b","folderIdOfsampleFolder_2b_3b"],
      ["folderIdOfsampleFolder1","folderIdOfsampleFolder_2b","folderIdOfsampleFolder_2b_3b","folderIdOfsampleFolder_2b_3b_4a"]
    ],
    "names": [
      "sampleFolder1",
      "sampleFolder_2a",
      "sampleFolder_2b",
      "sampleFolder_2a_3a",
      "sampleFolder_2b_3a",
      "sampleFolder_2b_3b",
      "sampleFolder_2b_3b_4a"
    ],
    "folders": [
      "folderIdOfsampleFolder1",
      "folderIdOfsampleFolder_2a",
      "folderIdOfsampleFolder_2b",
      "folderIdOfsampleFolder_2a_3a",
      "folderIdOfsampleFolder_2b_3a",
      "folderIdOfsampleFolder_2b_3b",
      "folderIdOfsampleFolder_2b_3b_4a"
    ]
  },
  "fileList": [
    {
      "folderTree": ["folderIdOfsampleFolder1"],
      "files": [
        {
          "name": "Spreadsheet1",
          "mimeType": "application/vnd.google-apps.spreadsheet"
        }
      ]
    },
    {
      "folderTree": ["folderIdOfsampleFolder1","folderIdOfsampleFolder_2a"],
      "files": [
        {
          "name": "Spreadsheet2",
          "mimeType": "application/vnd.google-apps.spreadsheet"
        }
      ]
    },
    {
      "folderTree": ["folderIdOfsampleFolder1","folderIdOfsampleFolder_2b"],
      "files": [
        {
          "name": "Spreadsheet4",
          "mimeType": "application/vnd.google-apps.spreadsheet"
        }
      ]
    },
    {
      "folderTree": ["folderIdOfsampleFolder1","folderIdOfsampleFolder_2a","folderIdOfsampleFolder_2a_3a"],
      "files": null
    },
    {
      "folderTree": ["folderIdOfsampleFolder1","folderIdOfsampleFolder_2b","folderIdOfsampleFolder_2b_3a"],
      "files": [
        {
          "name": "Spreadsheet3",
          "mimeType": "application/vnd.google-apps.spreadsheet"
        }
      ]
    },
    {
      "folderTree": ["folderIdOfsampleFolder1","folderIdOfsampleFolder_2b","folderIdOfsampleFolder_2b_3b"],
      "files": null
    },
    {
      "folderTree": ["folderIdOfsampleFolder1","folderIdOfsampleFolder_2b","folderIdOfsampleFolder_2b_3b","folderIdOfsampleFolder_2b_3b_4a"],
      "files": [
        {
          "name": "Document1",
          "mimeType": "application/vnd.google-apps.document"
        },
        {
          "name": "image1.png",
          "mimeType": "image/png"
        },
        {
          "name": "Slides1",
          "mimeType": "application/vnd.google-apps.presentation"
        },
        {
          "name": "Spreadsheet5",
          "mimeType": "application/vnd.google-apps.spreadsheet"
        },
        {
          "name": "StandaloneProject1",
          "mimeType": "application/vnd.google-apps.script"
        },
        {
          "name": "Test1.txt",
          "mimeType": "text/plain"
        }
      ]
    }
  ],
  "totalNumberOfFiles": 10,
  "totalNumberOfFolders": 7
}
~~~

-----

<a name="Licence"></a>
# Licence
[MIT](LICENCE)

<a name="Author"></a>
# Author
[Tanaike](https://tanaikech.github.io/about/)

If you have any questions and commissions for me, feel free to tell me.

<a name="Update_History"></a>
# Update History
* v1.0.0 (November 17, 2018)

    1. Initial release.


[TOP](#TOP)
