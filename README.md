# filesync

Based on fruition-partners/filesync.

## Overview

filesync synchronizes local file changes to mapped records in ServiceNow instances.

This enables ServiceNow developers to use their favorite integrated development environments (IDEs) and text editors like WebStorm and Sublime for editing JavaScript, HTML, Jelly and other code - without wasting time and interrupting development workflow copying and pasting code into a browser.

When a file changes within a configured root (project) folder, the parent folder and file name are used to identify the table and record to be updated.

Adding an empty file, or clearing and saving an existing file, refreshes the local file with the latest instance version, syncing *from* the instance *to* the local file. This is an easy way to populate your project folder with existing scripts related to your project.

## Dependencies

filesync requires [node.js](http://nodejs.org/) and [npm](https://www.npmjs.org/) to be installed on your local system.

## Installation

1. Clone this repository to a suitable location on your filesystem: `git clone git@github.com:danalstadt/servicenow-filesync.git`

2. From the servicenow-filesync directory (that you just created), run `npm install` to install project dependencies.

3. Ensure the [JSON Web Service plugin](http://wiki.servicenow.com/index.php?title=JSON_Web_Service) is activated for your instance.

4. Copy the included example configuration `app.config.default.json` to `app.config.json`.

5. Create a local folder structure for your project / source files. Project folders will be mapped to root folders in the `app.config.json` config file.

6. Edit `app.config.json`.

    * Review the `app.config.json settings` section below for guidance.
    * Configure at least one root (project) folder to host, user, pass mapping. user and pass will be encoded and replaced by an auth key at runtime.
    * Note: You must restart filesync any time you update app.config.json.

## Usage

With installation and configuration completed, you can start filesync by executing the included executable: `./sync`. This watches for file changes.  As you make changes to mapped files, you'll see messages logged showing the sync processing.

Verify everything works by adding an empty file corresponding to an instance record.

Adding the empty file will cause filesync to sync the content from ServiceNow to the file. Any changes to this local file will now be synced to the mapped instance.

The basic workflow is to initially create a script on ServiceNow (script include, business rule, ui script, etc.), then add an empty file of the same name (and mapped extension) to a mapped local folder.

filesync does not currently support creating new records in ServiceNow by simply adding local files since there are additional fields that may need to be populated beyond mapped script fields. So, always start by creating a new record on the instance, then add the empty local file and start editing your script.

## app.config.json settings

*Comments are included below for documentation purposes but are not valid in JSON files. You can validate JSON at <http://www.jslint.com/>*

    {
        // maps a root (project) folder to an instance
        "roots": {
            "c:\\dev\\project_a": {                 // full path to root folder
                                                    // on Windows, ensure that backslashes are doubled
                                                    //    since backslash is an escape character in JSON
                                                    // on Mac, remove 'c:' and use forward slashes as in
                                                    //    "/path/to/project_a"
                "host": "demo001.service-now.com",  // instance host name
                "user": "admin",                    // instance credentials
                "pass": "admin"                     //    encoded to auth key and re-saved at runtime
            },
            "c:\\dev\\project_b": {                 // add additional root mappings as needed
                "host": "demo002.service-now.com",
                "auth": "YWRtaW46YWRtaW4="          // example of encoded user/pass
            }
        },
        // maps a subfolder of a root folder to a table on the configured instance
        "folders": {
            "script_includes": {                    // folder with files to sync
                "table": "sys_script_include",      // table with records to sync to
                "key": "name",                      // field to match with filename to ID unique record
                "fields": {                         // file contents are synced to a field based on filename suffix
                    "js": "script"                  //   files ending in .js will sync to script field
                }
            },
            "business_rules": {
                "table": "sys_script",
                "key": "name",
                "fields": {
                    "js": "script"
                }
            },
            "ui_pages": {
                "table": "sys_ui_page",
                "key": "name",
                "fields": {                         // multiple fields for the same record can be mapped to multiple
                    "xhtml": "html",                //   files by using different filename suffixes
                    "client.js": "client_script",   //   for ui pages, you might have three separate files:
                    "server.js": "processing_script" //    mypage.xhtml, mypage.client.js, mypage.server.js
                }                                   //   to store the all script associated with the page
            }
            ...
        },
        "debug": false                              // set to true to enable debug logging
    }
