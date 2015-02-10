#Upload - SamsonPHP 

Generic asynchronous file uploader module

Developed by [SamsonOS](http://samsonos.com/)

        
# SamsonPHP Upload module

[![Latest Stable Version](https://poser.pugx.org/samsonos/upload/v/stable.svg)](https://packagist.org/packages/samsonos/upload) 
[![Build Status](https://scrutinizer-ci.com/g/samsonos/upload/badges/build.png?b=master)](https://scrutinizer-ci.com/g/samsonos/upload/badges/build.png?b=master)
[![Code Coverage](https://scrutinizer-ci.com/g/samsonos/upload/badges/coverage.png?b=master)](https://scrutinizer-ci.com/g/samsonos/upload/?branch=master)
[![Total Downloads](https://poser.pugx.org/samsonos/upload/downloads.svg)](https://packagist.org/packages/samsonos/upload)
[![Scrutinizer Code Quality](https://scrutinizer-ci.com/g/samsonos/upload/badges/quality-score.png?b=master)](https://scrutinizer-ci.com/g/samsonos/upload/?branch=master)
[![Stories in Ready](https://badge.waffle.io/samsonos/upload.png?label=ready&title=Ready)](https://waffle.io/samsonos/upload)

##Configuration

Configuration for Upload module is not necessary, you can use it if you want to create unique file name or upload dir name for uploading files.
If you are not use configuration your upload catalog will be dir 'upload' on your server and file name will be random generated

Thanks to [SamsonPHP module/service configuration](https://github.com/samsonphp/config) you can create simple config class:

```php
class UploadConfig extends \samson\core\Config
{
    // Callback function for creating upload dir
    public $uploadDirHandler = 'uploadDirHandler';

    // Callback function for creating file name
    public $fileNameHandler = 'uploadNameHandler;
}
```

## Create async file uploading

The main feature of current module is asynchronous file uploading.
Look at the example.

### Example of HTML code
First of all you need to create html container with your file input:

```html
<p>
    <input type="hidden" class="__action" value="<?php url_base('catalog/upload'); ?>">
    <input type="hidden" class="__file_size" value="50000000">
    <input class="__example_upload" type="file" name="uploadFile">
</p>
```

Input with class "__action" defines handler of your uploading. It can be controller method in your bundle or simple controller function. It always must have class "__action".
Input with class "__file_size" defines max size of uploading file. It always must have class "__file_size".
Input with class "__example_upload" is input button for choosing file from clients computer. If you are using class name "__upload" for this input, you don't need to create some javascript code in order for everything to work.

### Example of javascript code
If you are using input with class different from "__upload", you need to handle you inputs:
```js
// Cache file input
var file = s('.__example_upload');

// Bind upload event
uploadFileHandler(file, {
    // Handle event before file uploading
    start: function () {
        alert('Uploading started');
    },
    // Handle event after upload finishing
    response: function (response) {
        alert('Uploading finished');
    }
});
```

The second parameter in function ```uploadFileHandler(file, options)``` is not necessary, you can use it for creating something special while file is uploading

### Example of uploading handler
Server action in our HTML example is controller catalog/upload.
Let's create this function:
```php
function catalog_async_upload()
{
    // Create AJAX response array
    $json = array('status' => 0);
    // Create object for uploading file to server
    $upload = new \samson\upload\Upload(array('png','jpg'));

    if ($upload->upload($filePath, $fileName, $realName) {
        $json['status'] = 1;
        // Add full path to uploaded file into AJAX response
        $json['filePath'] = $filePath;
        // Add uploaded file name into AJAX response
        $json['fileName'] = $fileName;
        // Add real file name (user's file name) into AJAX response
        $json['realName'] = $realName;
    }

    return $json;
}
```

To create file upload you need to create class \samson\upload\Upload, constructor of which can have three parameters.
First parameter is array of allowable file extensions for uploading file
Second is array of parameters for your callback functions. They can be used if you are using module configuration.
The third one is configuration class. This is system parameter which default value is m('upload'). Better do not specify it, if you are working with simple upload module.

Method that directly create uploading called ``` upload(& $filePath = '', & $fileName = '', & $realName = '') ```
You can get main file information using parameters of this method.