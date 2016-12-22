\[AI CC2015/2017\] \[Windows 10\] Extension Panel
---
Reboot extension panel.
===

* **Purpose**: debugging extension panel 
* **Reloads levels**: html/css/js and jsx
* **To reboot the CEP level** requires a reboot the Illustrator

Проблема с перезагрузкой панели

Перезагрузка панели с помощью метода CSInterface.closeExtension() приводит к разным результатам в разных версиях СС.

CC/CC2014
внутреннее содержимое панели исчезает
сама панель остаётся на месте - не пропадает
сворачивание и повторное разворачивание панели приводит к загрузке содержимого панели

СС2015/CC2017
внутреннее содержимое панели исчезает
сама панель тоже исчезает (было бы похоже на нажатие кнопки закрытия, если бы не следующие пункты)
итак, открываем панель из меню Window > Extensions > Наша Панель
панель появляется, но без содержимого, то есть пустая
сворачивание и повторное разворачивание панели не приводит к загрузке содержимого панели
содержимое появляется только после перезагрузки Иллюстратора

Есть обходной путь (через жопу называется):
пткрыть панель из другой панели (например, из модального диалога) вызовом CSInterface.requestOpenExtension(extensionId, params)


Итак, для того, чтобы перезагрузить (во время отладки) extension panel в Illustrator CC2017 (eng) (на некоторых компьютерах в Illustrator СС2015 тоже) в операционной системе Windows10x64(rus) без перезагрузки самого Illustrator необходимо создавать дополнительную панель (например ModalDialog).

Примерная структура файлов, в которые необходимо внести изменения:

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


manufest.xml (фрагмент)

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


index.html (фрагмент)

    ...
     <input type="button" id="btn_reload" class="hostButton" value="Reload"/>
    ...


dialog.html (целиком)

    <!doctype html><html><head>  <meta charset="utf-8"></head>
    <body><script src="js/libs/CSInterface.js"></script>
    <script src="js/themeManager.js"></script>
    <script src="js/dialog.js"></script></body></html>


main.js (фрагмент)

    ...
    $("#btn_reload").click(function() {
      new CSInterface().requestOpenExtension('com.wk.reload.dialog');
      new CSInterface().closeExtension();
    });
    ...


dialog.js (целиком)

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


Кто-то столкнулся с подобной проблемой?

Есть очевидное простое решение?



