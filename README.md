# info about this project

# 主要参考

[前端工程化（editorconfig+ESLint+Prettier+StyleLint+Husky、Commitlint）](https://juejin.cn/post/7287480172424822836?searchId=20240127141554EC9023D0BF0D15A592EE)

# ide项目规范

VSCode 搜索 `EditorConfig for VS Code` 插件并安装

## 集成editorconfig配置

- 作用：使项目代码风格保持一致 
- 步骤：在项目中创建 `.editorconfig` 文件

# git 提交规范

通过 Husky + Lint-staged + Commitlint + Commitizen + cz-git 来配置 Git 提交代码规范

## Husky

**官网：** [husky-get-started](https://typicode.github.io/husky/get-started.html)

核心内容是配置 Husky 的 `pre-commit` 和 `commit-msg` 两个钩子:

**pre-commit**：Husky + Lint-staged 整合实现 Git 提交前代码规范检测/格式化 (前提：ESlint + Prettier + Stylelint 代码统一规范

**commit-msg**: Husky + Commitlint + Commitizen + cz-git 整合实现生成规范化且高度自定义的 Git commit message

### 作用

我的理解是需要husky来配合Commitlint 的使用.

### 安装及初始化

- install

```shell
npm install --save-dev husky
```

- init

```
npx husky init
```

## lint-staged

lint-staged 是一个在git暂存文件上运行linters的工具，当然如果你觉得每次修改一个文件就给所有文件执行一次lint检查不恶心的话，这个工具对你来说就没有什么意义了，请直接关闭即可。

## commitlint

### 什么是commitlint?

commitlint 检查提交信息是否符合常规提交格式。

**官网**:[commitlint](https://github.com/conventional-changelog/commitlint)

### 安装

```shell
# Install commitlint cli and conventional config
npm install --save-dev @commitlint/{config-conventional,cli}
# For Windows:
npm install --save-dev @commitlint/config-conventional @commitlint/cli

# Configure commitlint to use conventional config
echo "module.exports = {extends: ['@commitlint/config-conventional']}" > commitlint.config.js
```

### 添加提交信息校验钩子

执行下面命令生成 `commint-msg` 钩子用于 git 提交信息校验，命令来自：[@commitlint/README.md](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fconventional-changelog%2Fcommitlint)

```shell
npx husky add .husky/commit-msg  'npx --no -- commitlint --edit ${1}'
```

会出现`add command is deprecated`的报错,可以直接在[commit-msg文件](.husky/_/commit-msg)中添加`npx --no -- commitlint --edit`.

## Commitizen & cz-git

**commitizen**: 基于Node.js的 `git commit` 命令行工具，辅助生成标准化规范化的 commit message。--[官方文档](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fcommitizen%2Fcz-cli)

**cz-git**: 一款工程性更强，轻量级，高度自定义，标准输出格式的 commitizen 适配器。--[官方文档](https://link.juejin.cn?target=https%3A%2F%2Fcz-git.qbb.sh%2Fzh%2F)

### 安装

```shell
npm install -D commitizen cz-git
```

### 修改 `package.json` 添加 `config` 指定使用的适配器

```json
{
  "scripts": {

  },
  "config": {
    "commitizen": {
      "path": "node_modules/cz-git"
    }
  }
}
```



### 添加自定义配置(可选，使用默认)

**方式一: (推荐) cz-git 与 [commitlint](https://github.com/conventional-changelog/commitlint) 进行联动给予校验信息**，所以可以编写于 [commitlint](https://github.com/conventional-changelog/commitlint#config) 配置文件之中。

- `commitlint.config.js`

```js
// .commitlintrc.js
/** @type {import('cz-git').UserConfig} */
module.exports = {
  // 继承的规则

  rules: {
    // @see: https://commitlint.js.org/#/reference-rules
    // 提交类型枚举，git提交type必须是以下类型

    "subject-case": [0], // subject大小写不做校验
  },

  prompt: {
    alias: { fd: "docs: fix typos" },
    messages: {
      type: "选择你要提交的类型 :",
      scope: "选择一个提交范围（可选）:",
      customScope: "请输入自定义的提交范围 :",
      subject: "填写简短精炼的变更描述 :\n",
      body: '填写更加详细的变更描述（可选）。使用 "|" 换行 :\n',
      breaking: '列举非兼容性重大的变更（可选）。使用 "|" 换行 :\n',
      footerPrefixesSelect: "选择关联issue前缀(可选):",
      customFooterPrefix: "输入自定义issue前缀 :",
      footer: "列举关联issue (可选) 例如: #31, #I3244 :\n",
      confirmCommit: "是否提交或修改commit ?",
    },
    types: [
      {
        value: "feat",
        name: "feat: ✨ A new feature",
        emoji: ":sparkles:",
      },
      { value: "fix", name: "fix: 🐛 A bug fix", emoji: ":bug:" },
      {
        value: "docs",
        name: "docs: 📝 Documentation only changes",
        emoji: ":memo:",
      },
      {
        value: "style",
        name: "style: 💄 Changes that do not affect the meaning of the code",
        emoji: ":lipstick:",
      },
      {
        value: "refactor",
        name: "refactor: ♻️ A code change that neither fixes a bug nor adds a feature",
        emoji: ":recycle:",
      },
      {
        value: "perf",
        name: "perf: 🚀 A code change that improves performance",
        emoji: ":zap:",
      },
      {
        value: "test",
        name: "test: ✅ Adding missing tests or correcting existing tests",
        emoji: ":white_check_mark:",
      },
      {
        value: "build",
        name: "build: 📦️ Changes that affect the build system or external dependencies",
        emoji: ":package:",
      },
      {
        value: "ci",
        name: "ci: ⚙️ Changes to our CI configuration files and scripts",
        emoji: ":ferris_wheel:",
      },
      {
        value: "chore",
        name: "chore: 🔨 Other changes that don't modify src or test files",
        emoji: ":hammer:",
      },
      {
        value: "revert",
        name: "revert: ⏪️ Reverts a previous commit",
        emoji: ":rewind:",
      },
    ],
    useEmoji: true,
    emojiAlign: "center",
    useAI: false,
    aiNumber: 1,
    themeColorCode: "",
    scopes: [],
    allowCustomScopes: true,
    allowEmptyScopes: true,
    customScopesAlign: "bottom",
    customScopesAlias: "custom",
    emptyScopesAlias: "empty",
    upperCaseSubject: false,
    markBreakingChangeMode: false,
    allowBreakingChanges: ["feat", "fix"],
    breaklineNumber: 100,
    breaklineChar: "|",
    skipQuestions: [],
    issuePrefixes: [
      { value: "closed", name: "closed:   ISSUES has been processed" },
    ],
    customIssuePrefixAlign: "top",
    emptyIssuePrefixAlias: "skip",
    customIssuePrefixAlias: "custom",
    allowCustomIssuePrefix: true,
    allowEmptyIssuePrefix: true,
    confirmColorize: true,
    maxHeaderLength: Infinity,
    maxSubjectLength: Infinity,
    minSubjectLength: 0,
    scopeOverrides: undefined,
    defaultBody: "",
    defaultIssues: "",
    defaultScope: "",
    defaultSubject: "",
  },
};

```

### [全局使用](https://cz-git.qbb.sh/zh/guide/)

**step**:

-  下载全局依赖

```shell
npm install -g cz-git commitizen
npm install -g husky
npm install -g @commitlint/config-conventional @commitlint/cli
```

- 全局配置适配器类型

```shell
echo '{ "path": "cz-git", "$schema": "https://cdn.jsdelivr.net/gh/Zhengqbbb/cz-git@1.8.0/docs/public/schema/cz-git.json" }' > ~/.czrc
```



- 添加自定义配置: 与 [commitlint](https://github.com/conventional-changelog/commitlint) 配合，在 `$HOME` 路径下创建配置文件

在 Unix-like 系统（如 Linux 或 MacOS）中，"$HOME" 通常指的是当前用户的主目录，例如 "/home/username" 或 "/Users/username"。在 Windows 系统中，它通常是 "C:\Users\username"。

所以，你需要在这个目录下创建一个名为 "commitlint.config.cjs" 的文件，并在其中配置 commitlint 的规则。这样，当你在这个系统用户下进行 git 提交操作时，commitlint 就会根据这个配置文件来检查你的提交信息是否符合规则。

创建文件的具体步骤可能会因操作系统和使用的文本编辑器而异，但一般来说，你可以在终端中使用 `touch` 命令创建文件，然后用你的文本编辑器打开它进行编辑。例如：

```bash
cd $HOME
touch commitlint.config.cjs
```

然后，你可以用 VS Code（或你喜欢的任何文本编辑器）打开这个文件进行编辑：

```bash
code commitlint.config.cjs
```

这将在 VS Code 中打开这个文件，你可以在其中输入你的 commitlint 配置。

## 全局安装目录

当你使用 `npm install -g` 命令全局安装一个包时，这个包会被安装到 npm 的全局 `node_modules` 目录中，而不是你的主目录（`${HOME}`）。

`npx` 是一个命令行工具，它可以执行在 npm registry 中的任何包。当你使用 `npx` 运行一个命令时，它会首先在本地的 `node_modules` 目录中查找，然后在全局的 `node_modules` 目录中查找。如果在这两个地方都没有找到，`npx` 会临时下载这个包并执行它。

所以，当你全局安装一个包后，你可以使用 `npx` 来运行它的命令。但是，这个包实际上是被安装在 npm 的全局 `node_modules` 目录中，而不是 `npx` 的目录或者你的主目录。你可以使用 `npm root -g` 命令来查看 npm 的全局 `node_modules` 目录的位置。
