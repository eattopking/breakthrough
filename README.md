# Turborepo starter

This is an official pnpm starter turborepo.

## What's inside?

This turborepo uses [pnpm](https://pnpm.io) as a package manager. It includes the following packages/apps:

### Apps and Packages

- `docs`: a [Next.js](https://nextjs.org/) app
- `web`: another [Next.js](https://nextjs.org/) app
- `ui`: a stub React component library shared by both `web` and `docs` applications
- `eslint-config-custom`: `eslint` configurations (includes `eslint-config-next` and `eslint-config-prettier`)
- `tsconfig`: `tsconfig.json`s used throughout the monorepo

Each package/app is 100% [TypeScript](https://www.typescriptlang.org/).

### Utilities

This turborepo has some additional tools already setup for you:

- [TypeScript](https://www.typescriptlang.org/) for static type checking
- [ESLint](https://eslint.org/) for code linting
- [Prettier](https://prettier.io) for code formatting

### Build

To build all apps and packages, run the following command:

```
cd my-turborepo
pnpm run build
```

### Develop

To develop all apps and packages, run the following command:

```
cd my-turborepo
pnpm run dev
```

### Remote Caching

Turborepo can use a technique known as [Remote Caching](https://turbo.build/repo/docs/core-concepts/remote-caching) to share cache artifacts across machines, enabling you to share build caches with your team and CI/CD pipelines.

By default, Turborepo will cache locally. To enable Remote Caching you will need an account with Vercel. If you don't have an account you can [create one](https://vercel.com/signup), then enter the following commands:

```
cd my-turborepo
pnpm dlx turbo login
```

This will authenticate the Turborepo CLI with your [Vercel account](https://vercel.com/docs/concepts/personal-accounts/overview).

Next, you can link your Turborepo to your Remote Cache by running the following command from the root of your turborepo:

```
pnpm dlx turbo link
```

## Useful Links

Learn more about the power of Turborepo:

- [Tasks](https://turbo.build/repo/docs/core-concepts/monorepos/running-tasks)
- [Caching](https://turbo.build/repo/docs/core-concepts/caching)
- [Remote Caching](https://turbo.build/repo/docs/core-concepts/remote-caching)
- [Filtering](https://turbo.build/repo/docs/core-concepts/monorepos/filtering)
- [Configuration Options](https://turbo.build/repo/docs/reference/configuration)
- [CLI Usage](https://turbo.build/repo/docs/reference/command-line-reference)


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



