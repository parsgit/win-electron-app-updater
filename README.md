# win-electron-app-updater

### add this code to main.js


```
//file system
var fs = require('fs');

// for download update file
const DownloadManager = require("electron-download-manager");
  DownloadManager.register({
      downloadFolder: "resources"
  });

// for run update app
const exec = require('child_process').exec;
```

### Add these codes to the createWindow() function
```
  global.appver = app.getVersion();
  global.appname= app.getName();

```


and sample function for update
add to main.js file

```
global.downloadUpdateFile=function(uploadFileUrl,getObject) {
  fs.writeFile("up.info",getObject, function(err) {
    if(err) {
        return console.log(err);
    }

    DownloadManager.download({
      url: uploadFileUrl
    }, function (error, info) {
      if (error) {
        console.log(error);
        return;
      }

     console.log("update start");
     
      exec('elUpdater.exe', (e, stdout, stderr)=> {
      });

      setTimeout(function() {
        app.quit()
      }, 900)
    });
});

}
```

add code to **index.html** for check the update 

```
var electron = require('electron')
var remote = electron.remote;
  
  
$.get('http://localhost/update/up.info?v='+new Date().getTime())
  .done(function(get) {
    json = decodeURIComponent(escape(window.atob( get )));
    json = JSON.parse(json);
    var appver = remote.getGlobal('appver');
    var lastver   = json.ver;

    if(appver != lastver){
      remote.getGlobal('downloadUpdateFile')(json.url,get);
    }
    else{
      console.log('app is update :) ');
    }
    console.log(json);
  })
```


up.info is a Json file with a base64 structure
# params : 
```
{"name":"app name","ver":"1.0.1","url":"http://localhost/update/update.asar","updateFileName":"update.asar","mainFileName":"app.asar","path":"resources\\","updateFileHash":"7c781d0bf6a37a936c7224054d99d6e0","active":true,"exeRun":"electron-quick-start.exe"}
```

> url : update file link

> updateFileHash : use elHashFileUpdate.exe app for get hash update file


**change json to base64**  (You can use base64encode.org)
```
eyJuYW1lIjoiYXBwIG5hbWUiLCJ2ZXIiOiIxLjAuMSIsInVybCI6Imh0dHA6Ly9sb2NhbGhvc3QvdXBkYXRlL3VwZGF0ZS5hc2FyIiwidXBkYXRlRmlsZU5hbWUiOiJ1cGRhdGUuYXNhciIsIm1haW5GaWxlTmFtZSI6ImFwcC5hc2FyIiwicGF0aCI6InJlc291cmNlc1xcIiwidXBkYXRlRmlsZUhhc2giOiI3Yzc4MWQwYmY2YTM3YTkzNmM3MjI0MDU0ZDk5ZDZlMCIsImFjdGl2ZSI6InRydWUiLCJleGVSdW4iOiJlbGVjdHJvbi1xdWljay1zdGFydC5leGUifQ==
```
and save in server sample http://localhost/update/up.info

download and add this file to app location

[download elUpdater.exe](https://github.com/parsgit/win-electron-app-updater/releases)
