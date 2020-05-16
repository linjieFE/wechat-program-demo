### 如何学习小程序

#### 项目如何生成 `tree.md`，
- 使用tree生成目录树
    + 安装tree-node-cli模块包
    ``` bash
    cnpm install -g tree-node-cli
    ```
    + 常用命令

    ``` bash
    $ tree --help
    Usage: tree [options]
    
    Options:
    -V, --version             output the version number
    -a, --all-files           All files, include hidden files, are printed.
    --dirs-first              List directories before files.
    -d, --dirs-only           只显示文件夹
    -I, --exclude [patterns]  用于过滤不想要显示的文件或者文件夹。比如你想要过滤项目中的vendor文件 
                                夹，可以使用tree -I “vendor”；
    -L, --max-depth <n>       显示项目的层级。n表示层级数。比如想要显示项目三层结构，用tree -l 3；
    -r, --reverse             Sort the output in reverse alphabetic order.
    -F, --trailing-slash      Append a '/' for directories.
    -h, --help                output usage information
    tree > tree.md 将项目结构输出到tree.md这个文件。
    ```

    + 1.如果我们要显示某个项目下3层的所有文件结构，同时又过滤node_modules文件夹，最后输出到tree.md，在自己根目录下运行下面命令：
    ``` bash
    tree -L 3 -I "node_modules" > tree.md
    ```
    
```
 wechat-program-demo  
 ├── README.md  
 └── tree.md
```
