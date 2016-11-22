# ueditor-extension
UEditor扩展，UEditor上传图片到项目外部目录-java


原料

1、ueditor源码，包括ueditor.jar的源码
2、ueditor上传文件路径配置官方文档(http://fex.baidu.com/ueditor/#server-path)
需求

查阅官方文档，上传的文件存放路径根据项目根路径配置，若要使文件存放路径配置到项目外，使用相对路径就行
然而对于有些童鞋来说，使用相对路径../../../..会带来很多不确定性，项目迁移或文件服务器变动都需对相对路径进行重新调整，若在window server中跨盘符显然是无法满足的
实操

开源的好处就是自己能动手，对源码进行些扩展以达到自己想要的效果。
学习源码参考：ueditor文件上传研究（http://asialee.iteye.com/blog/2100187），感谢前人提供肩膀。
代码详解上面的文章已经很清楚，这里尽量简洁，后面附上github地址。
源码结构：
源码结构
源码修改记录：（详见源码，在ueditor.1.5.0.jar中搜索zrk就能搜索到所有修改的代码）
本人修改记录
config.json配置
config.json中添加如下属性 （一定要添加此属性）：

    "physicsPath":"d:/resource", 

physicsPath ：属性配置文件存放的路径，不同系统环境如“d:/resource”或“/home/resource”，若需使用ueditor默认配置，physicsPath值置为“”（空串）即可。
使用：

仅仅完成上面的配置是不够的，在使用中会图片是上传后是无法预览的，既然文件放在项目路径外部，想通过当前项目来访问这些图片不是好的方式；
既然有将图片存放外部的需求，文件必然是使用单独的文件服务来访问，就需要在返回的路径前附加文件服务访问路径；好在config.json中“imageUrlPrefix”（访问路径前缀）可以满足这个要求（通常在文件存放项目路径下时我们设置“imageUrlPrefix”当前项目名，使用当前项目路径访问文件，文件放存外部后这样就不可行）；
配置说明

“imageUrlPrefix”设置为文件服务访问路径：

"imageUrlPrefix": "http://127.0.0.1:8080/resource", /* 图片访问路径前缀 */

这时config.json是这样：

{
    /* 上传图片配置项 */
    "physicsPath":"d:/home/resource",
    "imageActionName": "uploadimage", /* 执行上传图片的action名称 */
    "imageFieldName": "upfile", /* 提交的图片表单名称 */
    "imageMaxSize": 2048000, /* 上传大小限制，单位B */
    "imageAllowFiles": [".png", ".jpg", ".jpeg", ".gif", ".bmp"], /* 上传图片格式显示 */
    "imageCompressEnable": true, /* 是否压缩图片,默认是true */
    "imageCompressBorder": 1600, /* 图片压缩最长边限制 */
    "imageInsertAlign": "none", /* 插入的图片浮动方式 */
    "imageUrlPrefix": "http://127.0.0.1:8080/resource", /* 图片访问路径前缀 */
    "imagePathFormat": "/ueditor/jsp/upload/image/{yyyy}{mm}{dd}/{time}{rand:6}", /* 上传保存路径,可以自定义保存路径和文件名格式 */
    /*此处省略80行，请参考百度ueditor中的config.json， 涂鸦、多图上传、视频上传、列出目录的“文件访问路径前缀”同“imageUrlPrefix”*/
 }

这里”imageUrlPrefix”的”http://127.0.0.1:8080/resource“是文件服务器访问路径,完整的config.json中的涂鸦、多图上传、视频上传、列出目录等都只需要修改相应的前缀。

physicsPath不为空串时:

    文件存放路径：physicsPath + imagePathFormat
    返回给浏览器路径：imageUrlPrefix + imagePathFormat

physicsPath为空串时:

    文件存放路径：项目根路径 + imagePathFormat
    返回给浏览器路径：imageUrlPrefix + imagePathFormat
    *此时imageUrlPrefix 应设置为项目名

这样就达到了对路径的扩展，是否需要将文件存放外部就由“physicsPath”来决定了。
