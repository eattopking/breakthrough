### 实现突破的一个项目

### 技术栈
turborepo、pnpm、turbopack、ts



### turbo.json 配置文件
```
{
  "$schema": "https://turborepo.org/schema.json",
  // 管道，就是配置turbo 可以执行的指令
  "pipeline": {
    "build": {
      // 是否缓存执行结果
      "cache": true,
      // 表示要执行对应子包的 scripts命令, ^表示先执行子包所以依赖的包的build
      // scripts命令，在执行子包自己的build命令
      "dependsOn": ["^build"],
      // 设置输出构建结果的目录，空数组表示不输出，默认值是["dist/**"]
      // ** 表示任意层级的目录，*表示任意文件名
      "outputs": [".next/**"],
      // 构建时输出日记的等级， 默认时full全部输出
      "outputMode": "full",
      // 需要监听改变进行构建的内容
      "inputs": ["src/**/*.tsx", "src/**/*.ts", "src/**/*.json","test/**/*.ts", "test/**/*.tsx"]
    },
    "test": {
      "cache": false,
      "dependsOn": ["build"],
      "outputs": ["publich/**"],
      "inputs": ["src/**/*.tsx", "src/**/*.ts", "src/**/*.json","test/**/*.ts", "test/**/*.tsx"]
    },
    "lint": {
      "outputs": []
    },
    "deploy": {
      "dependsOn": ["build", "test", "lint"],
      "outputs": []
    }
  }
}
```

### 过滤执行对应包的命令

turbo run build "--filter=@relaxed/utils"

### cache缓存

每次打包的时候会缓存构建内容，下次大包会跳过缓存的内容， 缓存内容在一个log文件中

本地缓存存在 这里 ./node_modules/.cache/turbo

### 强制覆盖缓存，全部重新执行
turbo run build --force

### 远程缓存

支持缓存共享，一个机器的缓存可以在另一台机器上复用

```
如果要将本地 turborepo 链接到远程缓存，请首先使用 Vercel 帐户对 Turborepo CLI 进行身份验证：

npx turbo login
```

```
接下来，您可以通过运行以下命令将您的 turborepo 链接到远程缓存：

npx turbo link
```

### 常用命令

--filter 筛选

turbo run build --filter=my-pkg

--force 覆盖

turbo run build --force

--no-cache 不缓存结果

turbo run build --no-cache

--only 只会执行对应管道的指令，不会执行管道的依赖的同名指令

turbo run test --only

### pnpm 常用命令

通过 pnpm 创建momorepo项目只需要 创建一个pnpm-workspace.yaml文件

然后里面配置想要存放momorepo项目的目录名，可以配置多个目录名，例如

packages:
  - 'packages/*'
  - 'app/*'

这样app目录和packages目录下都可以创建多个项目共同维护

pnpm -F (package.json 的name) run dev， 执行package.json 的name的项目的script指令

pnpm -C 目录名 ls -a 在指令执行的目录找到对应目录，然后在这个目录下执行ls -a 指令



