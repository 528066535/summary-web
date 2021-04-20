
    nmp相关
    
## npm 依赖管理中被忽略的那些细节

#### 前言

提起 npm，大家第一个想到的应该就是 npm install 了，但是 npm install 之后生成的 node_modules 大家有观察过吗？
package-lock.json 文件的作用大家知道吗？除了 dependencies 和 devDependencies，
其他的依赖有什么作用呢？接下来，本文将针对 npm 中的你可能忽略的细节和大家分享一些经验。

### npm 安装机制

A 和 B 同时依赖 C，C 这个包会被安装在哪里呢？C 的版本相同和版本不同时安装会有什么差异呢？package.json 中包的前后顺序对于安装时有什么影响吗？
这些问题平时大家可能没有注意过，今天我们就来一起研究一下吧。

#### A 和 B 同时依赖 C，这个包会被安装在哪里呢？

假如有 A 和 B 两个包，两个包都依赖 C 这个包，npm 2 会依次递归安装 A 和 B 两个包及其子依赖包到 node_modules 中。执行完毕后，我们会看到 
./node_modules 这层目录只含有这两个子目录：

```
node_modules/
├─┬ A
│ ├── C
├─┬ B
│ └── C
```

如果使用 npm 3 来进行安装的话，./node_modules 下的目录将会包含三个子目录：

```$xslt
node_modules/
├─┬ A
├─┬ B
├─┬ C
```

虽然目前最新的 npm 版本是 npm 6，但 npm 2 到 npm 3 的版本变更中实现了目录打平，与其他版本相比差别较大。因此，让我们具体看下这两个版本的差异。



npm 2 在安装依赖包时，采用简单的递归安装方法。执行 npm install 后，npm 根据 dependencies 和 devDependencies 属性中指定的包来确定第一层依赖，
npm 2 会根据第一层依赖的子依赖，递归安装各个包到子依赖的 node_modules 中，直到子依赖不再依赖其他模块。执行完毕后，
我们会看到 ./node_modules 这层目录中包含有我们 package.json 文件中所有的依赖包，而这些依赖包的子依赖包都安装在了自己的 node_modules 中。


但是这样的层级结构也有较为明显的缺陷，当我的 A，B，C 三个包中有相同的依赖 D 时，执行 npm install 后，D 会被重复下载三次，而随着我们的项目越来越复杂，
node_modules 中的依赖树也会越来越复杂，像 D 这样的包也会越来越多，造成了大量的冗余；在 windows 系统中，甚至会因为目录的层级太深导致文件的路径过长，触发文件路径不能超过 280 个字符的错误；



为了解决以上问题，npm 3 的 node_modules 目录改成了更为扁平状的层级结构，尽量把依赖以及依赖的依赖平铺在 node_modules 文件夹下共享使用。
