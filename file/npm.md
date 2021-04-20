
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
node_modules 中的依赖树也会越来越复杂，像 D 这样的包也会越来越多，造成了大量的冗余；在 windows 系统中，甚至会因为目录的层级太深导致文件的路径过长，
触发文件路径不能超过 280 个字符的错误；



为了解决以上问题，npm 3 的 node_modules 目录改成了更为扁平状的层级结构，尽量把依赖以及依赖的依赖平铺在 node_modules 文件夹下共享使用。

#### 为什么会出现 package-lock.json 呢？

为什么会有 package-lock.json 文件呢？这个我们就要先从 package.json 文件说起了。

#### package.json 的不足之处

npm install 执行后，会生成一个 node_modules 树，在理想情况下， 希望对于同一个 package.json 总是生成完全相同 node_modules 树。在某些情况下，
确实如此。但在多数情况下，npm 无法做到这一点。有以下两个原因：

1）某些依赖项自上次安装以来，可能已发布了新版本 。比如：A 包在团队中第一个人安装的时候是 1.0.5 版本，package.json 中的配置项为 A: '^1.0.5'；
团队中第二个人把代码拉下来的时候，A 包的版本已经升级成了 1.0.8，根据 package.json 中的 semver-range version 规范，
此时第二个人 npm install 后 A 的版本为 1.0.8；可能会造成因为依赖版本不同而导致的 bug；

2）针对 1）中的问题，可能有的小伙伴会是把 A 的版本号固定为 A: '1.0.5' 不就可以了吗？但是这样的做法其实并没有解决问题， 
比如 A 的某个依赖在第一个人下载的时候是 2.1.3 版本，但是第二个人下载的时候已经升级到了 2.2.5 版本，此时生成的 node_modules 树依旧不完全相同 ，
固定版本只是固定来自身的版本，依赖的版本无法固定。

#### 针对 package.json 不足的解决方法

为了解决上述问题以及 npm 3 的问题，在 npm 5.0 版本后，npm install 后都会自动生成一个 package-lock.json 文件 ，当包中有 package-lock.json 文件时，
npm install 执行时，如果 package.json 和 package-lock.json 中的版本兼容，会根据 package-lock.json 中的版本下载；如果不兼容，将会根据 package.json 的版本，
更新 package-lock.json 中的版本，已保证 package-lock.json 中的版本兼容 package.json。


#### package-lock.json 文件的结构

package-lock.json 文件中的 name、version 与 package.json 中的 name、version 一样，描述了当前包的名字和版本，dependencies 是一个对象，
该对象和 node_modules 中的包结构一一对应，对象的 key 为包的名称，值为包的一些描述信息， 根据 package-lock-json官方文档 ，主要的结构如下：

version ：包版本，即这个包当前安装在 node_modules 中的版本
resolved ：包具体的安装来源
integrity ：包 hash 值，验证已安装的软件包是否被改动过、是否已失效
requires ：对应子依赖的依赖，与子依赖的 package.json 中 dependencies 的依赖项相同
dependencies ：结构和外层的 dependencies 结构相同，存储安装在子依赖 node_modules 中的依赖包

#### package-lock.json 文件的作用

在团队开发中，确保每个团队成员安装的依赖版本是一致的，确定一棵唯一的 node_modules 树；
node_modules 目录本身是不会被提交到代码库的，但是 package-lock.json 可以提交到代码库，如果开发人员想要回溯到某一天的目录状态，
只需要把 package.json 和 package-lock.json 这两个文件回退到那一天即可。

由于 package-lock.json 和 node_modules 中的依赖嵌套完全一致，可以更加清楚的了解树的结构及其变化。

在安装时，npm 会比较 node_modules 已有的包，和 package-lock.json 进行比较，如果重复的话，就跳过安装 ，从而优化了安装的过程。

### 依赖的区别与使用场景

npm 目前支持以下几类依赖包管理包括


dependencies

devDependencies

optionalDependencies 可选择的依赖包

peerDependencies 同等依赖

bundledDependencies 捆绑依赖包


下面我们来看一下这几种依赖的区别以及各自的应用场景：
