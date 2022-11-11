## devDependencies和dependencies区别

使用 `npm install` 安装的包有两种，devDependencies 和 dependencies。

1. devDependencies 用于本地环境开发时候所需要的依赖包。
2. dependencies 用户发布环境，生产上所需要的依赖包。

在安装命令上有所不同：

- 后面部分为 `–save -dev` 的情况会使得下载的插件放在 package.json 文件的 devDpendencies 对象里面。
- 后面部分为 `–save` 的情况会使得下载的插件放在 package.json 文件的 dependencies 对象里面。

**区别**

- devDependencies 下的依赖包，只是我们在本地或开发坏境下运行代码所依赖的，若发到线上，其实就不需要 devDependencies 下的所有依赖包(比如各种loader，babel全家桶及各种webpack的插件等)。只用于开发环境，不用于生产环境，因此不需要打包。
- dependencies 是我们线上（生产坏境）下所要依赖的包，比如 vue，我们线上时必须要使用的，所以要放在 dependencies 下。dependencies 依赖的包不仅开发环境能使用，生产环境也能使用。



