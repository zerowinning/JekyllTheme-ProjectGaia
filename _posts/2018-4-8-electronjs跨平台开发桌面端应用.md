
## Electron介绍

简单来说，Electron就是可以让你用Javascript、HTML、CSS来编写运行于Windows、macOS、Linux系统之上的桌面应用的库。本文的目的是通过使用Electron开发一个完整但简单的小应用：记事本，来体验一下这个神器的开发过程。本文犹如`Hello World`一样的存在，是个入门级笔记，但如果你之前从未接触过Electron，而又对它有兴趣，某想信这会是一篇值得一看的入门教程。
　　PS：这篇文章是基于Windows的开发过程，未对macOS、Linux作测试。

## 开发环境安装

### 安装Node.js

点击 [这里](https://nodejs.org/en/) 进入官网下载、安装。

### 安装cnpm

由于众所周知的原因，你需要一个`cnpm`代替`npm`，[这里](https://npm.taobao.org/) 是官网。安装命令（打开系统的cmd.exe来执行命令）：

```
npm install -g cnpm --registry=https://registry.npm.taobao.org

```

### 安装Electron

```
cnpm install -g electron

```

### 快速启动，看看 Electron 是如何运转的

```
# 克隆示例项目的仓库
$ git clone https://github.com/electron/electron-quick-start

# 进入这个仓库
$ cd electron-quick-start

# 安装依赖并运行
$ npm install && npm start
```

### 安装Electron-forge

这是一个类似于傻瓜开发包的Electron工具整合项目。具体介绍点击 [这里](https://github.com/electron-userland/electron-forge)。

```
cnpm install -g electron-forge

```

## 新建项目

1.  假设项目要放到`H:\Electron`目录下，项目名为`notepad`（字母全部小写，多个单词之间可以用“-”连接）。
2.  打开`cmd.exe`，一路cd到`H:\Electron`。（也可以在`Electron`文件夹下，按住`Shift`键并右键单击空白处，选择`在此处打开命令窗口`来启动`cmd.exe`。）
3.  执行下面的命令来生成名为`notepad`的项目文件夹，同时安装项目所需要的模块、依赖项等。

```
electron-forge init notepad

```

1.  cd到`notepad`目录下，执行下面的命令来启动app（也可以简单的用`npm start`来运行）。

```
electron-forge start

```

![](../img/2018-4-8/1.png)


1.  这样就可以看到基本的app界面了。

![](../img/2018-4-8/2.png)



## 模板文件

1.  这里某使用**Visual Studio Code**来开发app。
2.  将`notepad`文件夹整个拖到VS Code中打开（或者点菜单`文件-打开文件夹`选择`notepad`文件夹打开项目），可以看一下项目的目录结构：`node_modules`文件夹下是各种模块、类库，`src`下是app的源代码文件，`package.json`是描述包的文件。

![](../img/2018-4-8/3.png)



3.  看一下`package.json`，注意这里默认已经将主进程入口文件配置为`index.js`（而不是`main.js`）。

![](../img/2018-4-8/4.png)


    为避免后面混乱，某还是将这里的`src/index.js`改成`src/main.js`，同时也要将文件`index.js`改名为`main.js`。

![](../img/2018-4-8/5.png)



4.  看一下`main.js`，这是app主进程的入口，在这里创建了`mainWindow`浏览器窗口，使用`mainWindow.loadURL(\`file://${__dirname}/index.html`)`来加载`index.html`主页；使用`mainWindow.webContents.openDevTools()`来打开开发者工具用于调试（这个操作通常在发布app时删除）。然后是app的事件处理：

*   `ready`: 当Electron完成初始化后触发，这里初始化后就会去创建浏览器窗口并加载主页面。
*   `window-all-closed`: 当所有浏览器窗口被关闭后触发，一般此时就退出应用了。
*   `activate`: 当app激活时触发，一般针对macOS要需要处理。

1.  看一眼`index.html`，这是主页面，除了显示`Well hey there!!!`的信息外，没什么具体内容。
2.  于是，现在整个app只有二个源码文件：`main.js`和`index.html`。`main.js`是主进程入口，`index.html`是一个web页面，它需要使用一个浏览器窗口（`BrowserWindow`）来加载和显示，作为应用的UI，它处在一个独立的渲染进程中。app启动时执行`main.js`中的代码创建窗口，加载页面等。主进程与渲染进程之间不能直接互相访问，需要通过`ipcMain`和`ipcRenderer`进行IPC通信（Inter-process communication），或者使用`remote`模块在渲染进程中使用主进程中的资源（反过来，在主进程中使用`webContents.executeJavascript`方法可以访问渲染进程）。

## Notepad App功能设计

这里将实现一个类似于Windows的**记事本**的App。这个App具备以下功能：

1.  **主菜单：**包括`File`, `Edit`, `View`, `Help`四个主菜单。重点是`File`菜单下的三个子菜单：`New`（新建文件）、`Open`（打开文件）、`Save`（保存文件），这三个菜单需要自定义点击事件，其它的菜单基本使用内建的方法处理，所以没什么难度。
2.  **文本框：**用于文本编辑。这也是这个App上的唯一一个组件，它的宽和高自动平铺满整个窗口大小。当修改了文本框中的文字后，会在App标题栏上最右侧添加一个`*`号以表示文档尚未保存。
3.  **加载和保存文本：**可以打开本地文本文件，支持`.txt`, `.js`, `.html`, `.md`等文本文件；可以将文本内容保存为本地文本文件。在**打开**或**新建**文件前，如果当前文档尚未保存，会提示用户先保存文档。
4.  **退出程序：**退出窗口或程序时，会检测当前文档是否需要保存，如果尚未保存，提示用户保存。
5.  **右键菜单：**支持右键菜单，可以通过菜单右键执行一些基本的操作，如：复制、粘贴等。
    下面是这个记事本App的演示效果，源码下载点击 [这里](https://github.com/XMandarava/Demo-Electron-Notepad)。

  ![](../img/2018-4-8/6.gif)

## Notepad App功能细节

由于主进程与渲染进程不能直接互相访问，所以部分细节有必要先考虑清楚。

1.  **主菜单：**因为菜单只存在于主进程中，所以在执行某些涉及页面（渲染进程）的菜单命令时，比如`Open`（打开文件）命令，就需要与渲染进程进行通信，这可以使用`ipcMain`和`ipcRenderer`来实现。
2.  **右键菜单、对话框：**所谓右键菜单其实和主菜单并无分别，只是显示方式不同。由于菜单、对话框等都只存在于主进程中，要在渲染进程中使用它们，就需要向主进程发送进程间消息，为简化操作，Electron提供了一个`remote`模块，可以在渲染进程中调用主进程的对象和方法，而无需显式地发送进程间消息，所以这一部分可以由它来实现。PS：对于从主进程访问渲染进程（反向操作），可以使用`webContents.executeJavascript`方法。
3.  **退出时保存检测：**用户点击窗口的`关闭`按钮，或者点击`Exit`菜单就会关闭窗口退出程序。在退出时，有必要检查文档是否需要保存，如果尚未保存就提示用户保存。要实现这一效果，首先，在主进程监测到用户关闭窗口时，向渲染进程发送一个特定的消息表明窗口准备关闭，渲染进程获得该消息后查看文档是否需要保存，如果需要就弹窗提示用户保存，用户保存或取消保存后，渲染进程再向主进程发送一个消息表明可以关闭程序了，主进程获得该消息后关闭窗口退出程序。这个过程也由`ipcMain`和`ipcRenderer`来实现。

## Notepad App的实现

整个App功能比较简单，最终实现后也只用到了三个主要文件，包括：`main.js`，`index.html`，`index.js`。

### main.js

这是主进程的入口，在这里创建App窗口，生成菜单，载入页面等。下面是该文件的完整源码，二个`//-------`之间是某根据功能需要添加的代码，其余是模板自动生成的代码。

```
import { app, BrowserWindow } from 'electron';
//-----------------------------------------------------------------
import { Menu, MenuItem, dialog, ipcMain } from 'electron';
import { appMenuTemplate } from './appmenu.js';
//是否可以安全退出
let safeExit = false;
//-----------------------------------------------------------------

// Keep a global reference of the window object, if you don't, the window will
// be closed automatically when the JavaScript object is garbage collected.
let mainWindow;

const createWindow = () => {
  // Create the browser window.
  mainWindow = new BrowserWindow({
    width: 800,
    height: 600,
  });

  // and load the index.html of the app.
  mainWindow.loadURL(`file://${__dirname}/index.html`);

  // Open the DevTools.
  //mainWindow.webContents.openDevTools();

  //-----------------------------------------------------------------
  //增加主菜单（在开发测试时会有一个默认菜单，但打包后这个菜单是没有的，需要自己增加）
  const menu=Menu.buildFromTemplate(appMenuTemplate); //从模板创建主菜单
  //在File菜单下添加名为New的子菜单
  menu.items[0].submenu.append(new MenuItem({ //menu.items获取是的主菜单一级菜单的菜单数组，menu.items[0]在这里就是第1个File菜单对象，在其子菜单submenu中添加新的子菜单
    label: "New",
    click(){
      mainWindow.webContents.send('action', 'new'); //点击后向主页渲染进程发送“新建文件”的命令
    },
    accelerator: 'CmdOrCtrl+N' //快捷键：Ctrl+N
  }));
  //在New菜单后面添加名为Open的同级菜单
  menu.items[0].submenu.append(new MenuItem({
    label: "Open",
    click(){
      mainWindow.webContents.send('action', 'open'); //点击后向主页渲染进程发送“打开文件”的命令
    },
    accelerator: 'CmdOrCtrl+O' //快捷键：Ctrl+O
  })); 
  //再添加一个名为Save的同级菜单
  menu.items[0].submenu.append(new MenuItem({
    label: "Save",
    click(){
      mainWindow.webContents.send('action', 'save'); //点击后向主页渲染进程发送“保存文件”的命令
    },
    accelerator: 'CmdOrCtrl+S' //快捷键：Ctrl+S
  }));
  //添加一个分隔符
  menu.items[0].submenu.append(new MenuItem({
    type: 'separator'
  }));
  //再添加一个名为Exit的同级菜单
  menu.items[0].submenu.append(new MenuItem({
    role: 'quit'
  }));
  Menu.setApplicationMenu(menu); //注意：这个代码要放到菜单添加完成之后，否则会造成新增菜单的快捷键无效

  mainWindow.on('close', (e) => {
    if(!safeExit){
      e.preventDefault();
      mainWindow.webContents.send('action', 'exiting');
    }
  });
  //-----------------------------------------------------------------

  // Emitted when the window is closed.
  mainWindow.on('closed', () => {
    // Dereference the window object, usually you would store windows
    // in an array if your app supports multi windows, this is the time
    // when you should delete the corresponding element.
    mainWindow = null;
  });
};

// This method will be called when Electron has finished
// initialization and is ready to create browser windows.
// Some APIs can only be used after this event occurs.
app.on('ready', createWindow);

// Quit when all windows are closed.
app.on('window-all-closed', () => {
  // On OS X it is common for applications and their menu bar
  // to stay active until the user quits explicitly with Cmd + Q
  if (process.platform !== 'darwin') {
    app.quit();
  }
});

app.on('activate', () => {
  // On OS X it's common to re-create a window in the app when the
  // dock icon is clicked and there are no other windows open.
  if (mainWindow === null) {
    createWindow();
  }
});

// In this file you can include the rest of your app's specific main process
// code. You can also put them in separate files and import them here.

//-----------------------------------------------------------------
//监听与渲染进程的通信
ipcMain.on('reqaction', (event, arg) => {
  switch(arg){
    case 'exit':
      //做点其它操作：比如记录窗口大小、位置等，下次启动时自动使用这些设置；不过因为这里（主进程）无法访问localStorage，这些数据需要使用其它的方式来保存和加载，这里就不作演示了。这里推荐一个相关的工具类库，可以使用它在主进程中保存加载配置数据：https://github.com/sindresorhus/electron-store
      //...
      safeExit=true;
      app.quit();//退出程序
      break;
  }
});
//-----------------------------------------------------------------

```

首先，`app.on('ready', createWindow)`也就是当Electron完成初始化后，就调用`createWindow`方法来创建浏览器窗口`mainWindow`（与主进程只能有1个不同，可以根据需要适时创建更多个浏览器窗口，这些窗口由主进程负责创建和管理，每个浏览器窗口使用一个独立的渲染进程；本文只需使用一个浏览器窗口，即`mainWindow`）。同时，使用`Menu.buildFromTemplate(appMenuTemplate)`通过一个菜单模板来创建app应用主菜单，模板代码存放在`appmenu.js`文件中，这个模板的写法可以参考官方的 [Electron API Demos](https://link.jianshu.com?t=https://github.com/electron/electron-api-demos)
中`Customize Menus`的例子。模板的第一个菜单是`File`菜单，它的子菜单被设计成空的，在这里使用`menu.items[0].submenu.append`方法向这个`File`菜单添加四个子菜单，分别是：`New`（新建文档），`Open`（打开文档），`Save`（保存文档），`Exit`（退出程序）。其中，前三个菜单在点击后都会向渲染进程发送信息，通知渲染进程执行相关处理。如对于`New`菜单，使用`mainWindow.webContents.send('action', 'new')`的方式，通知渲染进程要新建一个文档。渲染进程会使用`ipcRenderer.on`方法来执行监听，监听到消息后就会执行相应处理（这部分在`index.js`中实现）。最后使用`Menu.setApplicationMenu(menu)`将主菜单安装到浏览器窗体中（所有窗体会共享主菜单）。

### index.html

这是App的文本编辑页面。这个页面很简单，整个页面就只有一个`TextArea`控件（id为`txtEditor`），平铺满整个窗口。该页面使用`require('./index.js')`载入`index.js`。

```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Notepad</title>
  <style type="text/css">
    body,html{
        margin:0px;
        height:100%;
    }

    #txtEditor{
        width:100%;
        height:99.535%;
        padding:0px;
        margin:0px;
        border:0px;
        font-size: 18px;
    }
  </style>
  </head>
  <body>
  <textarea id="txtEditor"></textarea>
</body>
  <script>
    require('./index.js');
  </script>
</html>

```

### index.js

所有主页面`index.html`涉及到的页面处理、与主进程交互等的操作都会放到该js文件中。该文件完整代码：

```
import { ipcRenderer, remote } from 'electron';
const { Menu, MenuItem, dialog } = remote;

let currentFile = null; //当前文档保存的路径
let isSaved = true;     //当前文档是否已保存
let txtEditor = document.getElementById('txtEditor'); //获得TextArea文本框的引用

document.title = "Notepad - Untitled"; //设置文档标题，影响窗口标题栏名称

//给文本框增加右键菜单
const contextMenuTemplate=[
    { role: 'undo' },       //Undo菜单项
    { role: 'redo' },       //Redo菜单项
    { type: 'separator' },  //分隔线
    { role: 'cut' },        //Cut菜单项
    { role: 'copy' },       //Copy菜单项
    { role: 'paste' },      //Paste菜单项
    { role: 'delete' },     //Delete菜单项
    { type: 'separator' },  //分隔线
    { role: 'selectall' }   //Select All菜单项
];
const contextMenu=Menu.buildFromTemplate(contextMenuTemplate);
txtEditor.addEventListener('contextmenu', (e)=>{
    e.preventDefault();
    contextMenu.popup(remote.getCurrentWindow());
});

//监控文本框内容是否改变
txtEditor.oninput=(e)=>{
    if(isSaved) document.title += " *";
    isSaved=false;
};

//监听与主进程的通信
ipcRenderer.on('action', (event, arg) => {
    switch(arg){        
    case 'new': //新建文件
        askSaveIfNeed();
        currentFile=null;
        txtEditor.value='';   
        document.title = "Notepad - Untitled";
        //remote.getCurrentWindow().setTitle("Notepad - Untitled *");
        isSaved=true;
        break;
    case 'open': //打开文件
        askSaveIfNeed();
        const files = remote.dialog.showOpenDialog(remote.getCurrentWindow(), {
            filters: [
                { name: "Text Files", extensions: ['txt', 'js', 'html', 'md'] }, 
                { name: 'All Files', extensions: ['*'] } ],
            properties: ['openFile']
        });
        if(files){
            currentFile=files[0];
            const txtRead=readText(currentFile);
            txtEditor.value=txtRead;
            document.title = "Notepad - " + currentFile;
            isSaved=true;
        }
        break;
    case 'save': //保存文件
        saveCurrentDoc();
        break;
    case 'exiting':
        askSaveIfNeed();
        ipcRenderer.sendSync('reqaction', 'exit');
        break;
    }
});

//读取文本文件
function readText(file){
    const fs = require('fs');
    return fs.readFileSync(file, 'utf8');
}
//保存文本内容到文件
function saveText(text, file){
    const fs = require('fs');
    fs.writeFileSync(file, text);
}

//保存当前文档
function saveCurrentDoc(){
    if(!currentFile){
        const file = remote.dialog.showSaveDialog(remote.getCurrentWindow(), {
            filters: [
                { name: "Text Files", extensions: ['txt', 'js', 'html', 'md'] }, 
                { name: 'All Files', extensions: ['*'] } ]
        });
        if(file) currentFile=file;
    }
    if(currentFile){
        const txtSave=txtEditor.value;
        saveText(txtSave, currentFile);
        isSaved=true;
        document.title = "Notepad - " + currentFile;
    }
}

//如果需要保存，弹出保存对话框询问用户是否保存当前文档
function askSaveIfNeed(){
    if(isSaved) return;
    const response=dialog.showMessageBox(remote.getCurrentWindow(), {
        message: 'Do you want to save the current document?',
        type: 'question',
        buttons: [ 'Yes', 'No' ]
    });
    if(response==0) saveCurrentDoc(); //点击Yes按钮后保存当前文档
}

```

首先，前面说了，在渲染进程中不能直接访问菜单，对话框等，它们只存在于主进程中，但可以通过`remote`来使用这些资源。

```
import { remote } from 'electron';
const { Menu, MenuItem, dialog } = remote;

```

然后，`const contextMenu=Menu.buildFromTemplate(contextMenuTemplate)`即使用`contextMenuTemplate`模板来创建编辑器的右键菜单（虽然创建过程在渲染进程中进行，但实际上使用`remote`来创建的菜单、对话框等，仍然只存在于主进程内），由于这里涉及到的菜单都只需要使用系统的内建功能，不需要自定义，所以这里比较简单。使用`txtEditor.addEventListener('contextmenu')`来监听右键菜单请求，使用`contextMenu.popup(remote.getCurrentWindow())`来弹出右键菜单。
　　`txtEditor.oninput`用于监控文本框内容变化，如果有改变，则将文档标记为尚未保存，并在标题栏最右侧显示一个`*`号作为提示。
　　PS：在Win7上如果没有启用Aero效果，使用`document.title = xxx`或`remote.getCurrentWindow().setTitle(xxx)`都看不到程序标题栏的标题变化，只当你比如缩放一下窗口后这个修改才会被刷新。
　　`ipcRenderer.on`用于监听由主进程发来的消息。前面说过，主进程使用`mainWindow.webContents.send('action', 'new')`的方式向渲染进程发送特定消息，渲染进程监听到消息后，根据消息内容做出相应处理。比如，这里，当主进程发来`new`的消息后，渲染进程就开始着手新建一个文档，在新建前会使用`askSaveIfNeed`方法检测文档是否需要保存，并提示用户保存；对于`open`的消息就会调用`remote.dialog.showOpenDialog`来显示一个**文件打开对话框**，由用户选择要打开的文档然后加载文本数据；而对于`save`消息就会对当前文档进行保存操作。

### 退出时保存检测的实现过程

正如前面在App功能细节中讨论的一样，在关闭程序前，友好的做法是检测文档是否需要保存，如果尚未保存，通知用户保存。要实现这一功能，需要在主进程和渲染进程间进行相互通信，以获得窗体关闭和文档保存的确认，实现安全退出。

#### 主进程端

首先在`main.js`中，使用`mainWindow.on('close')`来监控`mainWindow`窗口的关闭。

```
mainWindow.on('close', (e) => {
    if(!safeExit){
      e.preventDefault();
      mainWindow.webContents.send('action', 'exiting');
    }
  });

```

这里`safeExit`开关用于标记渲染进程是否已经向主进程反馈它已经完成所有操作了。如果尚未反馈，则使用`e.preventDefault()`阻止窗口关闭，并使用`mainWindow.webContents.send('action', 'exiting')`向渲染进程发送一个`exiting`消息，告诉渲染进程：嘿，我要关掉窗口了，你赶紧看看还要什么没做完的，做完后通知我。
　　既然主进程要等渲染进程的反馈，就需要监听渲染进程发回的消息，所以主进程使用`ipcMain.on`来执行监听。如果渲染进程发送一个`exit`消息过来，就表示可以安全退出了。

```
ipcMain.on('reqaction', (event, arg) => {
  switch(arg){
    case 'exit':
      safeExit=true;
      app.quit();
      break;
  }
});

```

#### 渲染进程端

在渲染进程这边的`index.js`中，在`ipcRenderer.on`监听方法中，还有一个消息处理，是针对主进程发来的`exiting`消息的，当获知主进程准备关闭窗口，渲染进程就先去检查文档是否保存过了，如果尚未保存就通知用户保存，用户保存或取消保存后，使用`ipcRenderer.sendSync('reqaction', 'exit')`来向主进程发送一个`exit`消息，表示：我要做的都做完了，你想退就退吧。

```
case 'exiting':
        askSaveIfNeed();
        ipcRenderer.sendSync('reqaction', 'exit');
        break;

```

主进程监听到这个消息后，将`safeExit`标记为`true`，表示已经得到渲染进程的确认，然后就可以使用`app.quit()`安全退出了。当然，在退出前，可以再执行一些其它操作（比如保存参数配置等）。

## 编译打包

1.  键入以下命令进行编译打包：

```
npm run make

```

该命令会将文件打包到当前项目目录下的`out`文件夹下。打包后发现，源码直接暴露在`[app项目目录]\out\notepad-win32-x64\resources\app\src`目录下。

1.  修改`package.json`，在`electronPackagerConfig`部分添加`"asar": true`。

```
"electronPackagerConfig": {
        "asar": true
      }

```

重新打包后源码文件会被打包进`app.asar`文件中（该文件仍然在`src`目录下）。

1.  可以直接运行打包后的`notepad.exe`启动程序。

by Mandarava（鳗驼螺） 2017.07.12
