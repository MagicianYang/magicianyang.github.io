---
layout: post
section-type: post
title: 网页加载特殊字体
category: tech
tags: [ 'django','css','javascript' ]
---

如果不改变字体直接加载的话，使用十分简单，例子如下

```css
<style>
@font-face {
    font-family: 'YourWebFontName';
    src: url('YourWebFontName.eot'); /* IE9 Compat Modes */
    src: url('YourWebFontName.eot?#iefix') format('embedded-opentype'), /* IE6-IE8 */
             url('YourWebFontName.woff') format('woff'), /* Modern Browsers */
             url('YourWebFontName.ttf')  format('truetype'), /* Safari, Android, iOS */
             url('YourWebFontName.svg#YourWebFontName') format('svg'); /* Legacy iOS */
}

div.testFont {
	font-family:YourWebFontName;
}
</style>
```

之后所需字体就会展现在下面这条

```html
<div class="testFont">test</div>
```

如果需要动态变更字体的话，则需要使用JavaScript作为辅助，[参考此条](http://stackoverflow.com/questions/11355147/font-face-changing-via-javascript)

```html
<div class="group">
	<label for="font">Font:</label>
    <select id="font" name="font" class="easyui-combobox">
    	<option value="-1">默认</option>
        {% for f in all_font %}
        	<option value="{{ f['id'] }}">{{ f['name'] }}</option>
        {% endfor %}
    </select>
</div>
<div class="group">
    <label for="font_size">Font Size:</label>
         <input id="font_size" name="font_size" class="easyui-numberspinner" value="22"
                       data-options="required:true,min:10,max:60"/>
</div>
<div id="btn_font" class="easyui-linkbutton btn">字体预览</div>
```

```
<script type="text/javascript">
    function drawCtrl($scope) {
        return true;
    }

    $(function () {
		$('#btn_font').click(function () {
            var font_info = $('#font').combobox('getValue');
            var font_size = $('#font_size').numberspinner('getValue');
            $.post('show/font',
                    {
                        'val': font_info
                    },
                    function (data) {
                        if (data.rt) {
                            console.log(data.font);
                            if (!data.font) {
                                var newStyle = document.createElement('style');
                                newStyle.appendChild(document.createTextNode("\
                                    #tp_text {\
                                        font-size: " + font_size + "px;\
                                    }\
                                    "));
                                document.head.appendChild(newStyle);
                            } else {
                                var newnewStyle = document.createElement('style');
                                newnewStyle.appendChild(document.createTextNode("\
                                    @font-face {\
                                        font-family: myFirstFont;\
                                        src: url('" + data.font + "') format('truetype');\
                                    }\
                                    #tp_text {\
                                        font-family: myFirstFont;\
                                        font-size: " + font_size + "px;\
                                    }\
                                    "));
                                document.head.appendChild(newnewStyle);
                            }
                        } else {
                            $.messager.alert('Error', '该字体不存在');
                        }
                    }, "json");
            });
    });
</script>
```




