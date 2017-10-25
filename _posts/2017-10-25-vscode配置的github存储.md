一开始不知道怎么备份VSCODE的配置,傻乎乎的把要用的插件抄下来,还有用户settings拷贝出来.每次换了电脑或者重装系统什么的都要重新备份.虽然来回调整的概率很低,但是突然哪天需要同步设置什么的就很麻烦了~至少我是在初期经常鼓捣这个编辑器,而且办公在家和公司是不同的设备~所以觉得还是很有必要的~

于是我今天要说的就是这个插件Settings Sync

# 安装配置插件
搜索Settings Sync插件安装并重新启动
# 配置github
重启后按快捷键 alt+shift+u 或者命令面板Sync
它会弹出一个窗口对应的是github上面的创建个人gist的页面,如果未登录请先登录github.
 generate new key
 选取名 code-settings-sync
 勾选 gist
生产出一个key

切回到vscode,他会有个输入区,就是存放刚才生成的key

输入刚才生成的key

然后理论上他就开始对你本机的配置进行一个扫描上传了.至此上传工作完成.
 # 恢复配置



接下来我们到另一台电脑上了下载配置.同样的先安装Settings Sync插件,并重新加载.

然后按快捷键alt+shift+d,就应该会弹出一个输入框,请在这里输入之前保存下来的key(GIST ID),回车后将会自动下载之前上传的配置.

那么下载完成后,你这台电脑修改了相关配置再次上传就好了.是不是感觉方便多了~

# 下面是生成的配置文件

***GITHUB GIST: 8ba91fec0be42b69090bd182d3162fb7***


VISUAL STUDIO CODE SETTINGS SYNC 
Version: 2.8.3

Upload Summary

--------------------
GITHUB TOKEN: 7294ec5df5b106de4b1c1cd9535dd95550af6532
GITHUB GIST: 8ba91fec0be42b69090bd182d3162fb7
GITHUB GIST TYPE: Secret

--------------------

If current theme / file icon extension is not installed. Restart will be Required to Apply Theme and File Icon.


Files Upload.

extensions.json
settings.json


EXTENSIONS ADDED :

code-settings-sync - Version :2.8.3
