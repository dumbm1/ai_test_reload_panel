\[AI CC2015/2017\] \[Windows 10\] Extension Panel
---
Reload extension panel (layers html/css/js + jsx)
===

* **Purpose**: debugging extension panel 
* **Reloads levels**: html/css/js and jsx
* **To reboot the CEP level** requires a reboot the Illustrator

Why (clarification)?

Trying to reboot panel with `SCInterface.closeExtension` method couses the problem

The panel closes. So you must run it from the menu again.
_Window > Extensions > Your extension panel_
Panel opens up but **completely blank!**
That can not be used until the next restart of Illustrator.

Workaround algorithm: 
 - open new ModalDialog extension `CSInterface.requestOpenExtension(ModalDialogExtensionID)`
 - close Panel extension `CSInterface.closeExtension()`
 - run Panel extension from ModalDialog `CSInterface.requestOpenExtension(ExtensionPanelID)`
 - close ModalDialog  `CSInterface.closeExtension()`

Extension file structure (only the files where you need to insert the code below):

    ai_test_reload_panel/
      CSXS/
        manifest.xml
      js/
        libs/
          CSInterface.js
        main.js
        dialog.js
        themeManager.js
      index.html
      dialog.html


manifest.xml (file fragment)

    ...
    <ExtensionList>
      ...
      <Extension Id="com.wk.reload.dialog" Version="1.0"/>
      ...
    </ExtensionList>
    ...
    <DispatchInfoList>
    ...
      <Extension Id="com.wk.reload.dialog">
        <DispatchInfo>
          <Resources>
            <MainPath>./dialog.html</MainPath>
            <CEFCommandLine>
            </CEFCommandLine>
          </Resources>
          <Lifecycle>
            <AutoVisible>true</AutoVisible>
          </Lifecycle>
          <UI>
            <Type>ModalDialog</Type>
            <Menu>Reload Modal Dialog</Menu>
            <Geometry>
              <Size>
                <Height>300</Height>
                <Width>200</Width>
              </Size>
            </Geometry>
          </UI>
        </DispatchInfo>
      </Extension>
    ...
    <DispatchInfoList>


index.html (file fragment)

    ...
     <input type="button" id="btn_reload" class="hostButton" value="Reload"/>
    ...


dialog.html (the entire file)

    <!doctype html><html><head>  <meta charset="utf-8"></head>
    <body><script src="js/libs/CSInterface.js"></script>
    <script src="js/themeManager.js"></script>
    <script src="js/dialog.js"></script></body></html>


main.js (file fragment)

    ...
    $("#btn_reload").click(function() {
      new CSInterface().requestOpenExtension('com.wk.reload.dialog');
      new CSInterface().closeExtension();
    });
    ...


dialog.js (the entire file)

    (function() {
      'use strict';
      var csInterface = new CSInterface();
      init();
      function init() {
        themeManager.init();
        if (csInterface.isWindowVisible()) {
          new CSInterface().requestOpenExtension('com.wk.reload.panel');
          new CSInterface().closeExtension();
        }
      }
    }());
    