# sparkee	
English | [中文文档](http://geocld.github.io/2022/07/04/sparkee/)

Sparkee is a publish tool for `monorepo`,just like lerna but support `pnpm`, also support singleRepo.

## About

### What can Sparkee do?
There are three commands in sparkee: `sparkee init`, `sparkee info` and `sparkee publish`.

`init` will initialize sparkee workspace.
`run` will run the script of package.
`info` will show package's info of workspace.
`publish` will publish any updated packages and generate changelog automatically.

### What diffrences with lerna?
* Sparkee can publish Single package or customize multiple packages.
* Support `workspace` protocol in `package.json`.
* Use `pnpm <--filter> publish` so you can keep the `workspace` keyword of `dependencies`.
* More friendly output of CLI.
* CLI commands are very very simple!

## Getting Started

### Project structure

Sparkee needs a file structure like this(the same as the lerna project):

```
your-repo/
  package.json
  packages/
    package-1/
      src/
      package.json
    package-2/
      src/
      package.json
```

It will track file changes inside the packages `src` folders and `package.json` files.

### Installation

```
# simple install or install it globally with -g
npm install sparkee --save-dev

# if you install globally, use it `sparkee <command>`
sparkee init
sparkee version
sparkee publish

# OR use npx in root of project
npx sparkee init
npx sparkee publish
npx sparkee version
```

### Usage

#### init

```sh
$ sparkee init
```

Initialize `sparkee workspace`. There will create a `spark.json`, you can make sparkee manage all of packages or customized packages.

> Sparkee assumes the repo has already been initialized with `git init`.

Example output on a new git repo:

Example-1. All packages:

```sh
$ sparkee init
? Do you need sparkee to manage all projects of packages folder? yes
✔ Sparkee init successful.
```

Example-2. Custom packages:

```sh
$ sparkee init
? Do you need sparkee to manage all projects of packages folder? No
? What packages do you want to manage? @geocld/pkg1
✔ Sparkee init successful.
``` 

Example-3. single repo:

```sh
$ sparkee init
? Please select repo type: singleRepo
✔ Sparkee init as singleRepo successful.
```
In singleRepo mode, `spark.json` will be:

```json
{
  "singleRepo": true,
  "moduleManager": "npm"
}
```

`singleRepo` : Must be `true` in single repo.
`moduleManager`: Can be `npm` or `yarn` or `pnpm`.

#### run(supported in v1.1.0)

```sh
$ sparkee run
```

Run the `scripts` of `packages.json`:

Example:

```bash
$ sparkee run
? Please select script: (Use arrow keys)
❯ dev -> rimraf dist && tsc -w
  build -> rimraf dist && tsc
```

#### info

```sh
$ sparkee info <--tree>
```

Print local information of packages.

Example:

```bash
$ sparkee info
> Current monorepo packages:
  · @geocld/pkg1: v1.0.0
  · @geocld/pkg2: v1.0.0
```

Or Print detail dependencies tree of packages:

```bash
$ sparkee info --tree

@geocld/pkg1@1.0.0
╰── @geocld/pkg2@1.0.0

----------

@geocld/pkg2@1.0.0
``` 

#### publish

```bash
$ sparkee publish
```

`publish` is the core of sparkee. When runing, this command does the following things:

- Find updated packages since the last release(must with `git tag`, otherwise compare with first commit).
- Select what packages that you want to publish.
- Update `version` of `package.json` automatically.
- Generate `CHANGELOG.md` automatically in selected packages.
- Run `git Commit`, `git tag` and `git push` automatically.
- Run `pnpm publish`

> sparkee will never publish packages which do not exits in `spark.json`.

The workflow of `sparkee` is as follows:

![workflow](http://geocld.github.io/img/sparkee/workflow.png)

#### publish --force
Sparkee will not publish unmodified packages, if you want to publish unmodified packages, use `--force`:

```bash
sparkee publish --force
```

#### publish --noPublish
If yout want `sparkee` just genetate changelog and create tag, `publish` command run in other place(such as `pipeline`), use `--noPublish` or `--np`:

```bash
sparkee publish --noPublish

or

sparkee publish --np
```

#### Custom changelLog commit message types

`sparkee` use [conventional-changelog](https://github.com/conventional-changelog/conventional-changelog) internally to generate `CHANGELOG.md`,the `commit types` config is :

```js
[
  { type: 'feat', section: 'Features' },
  { type: 'fix', section: 'Bugfixes' },
  { type: 'perf', section: 'Performance' },
  { type: 'refactor', section: 'Refactoring' },
  { type: 'test', section: 'Testing' },
  { type: 'docs', section: 'Documentation' },
  { type: 'build', hidden: true },
  { type: 'style', hidden: true }
]
```

If you want to use custom `commit types` to format changelog, you can config `logPresetTypes` in `spark.json` to override the default config:

```js
// spark.json
{
  "singleRepo": true,
  "moduleManager": "npm",
  "logPresetTypes": [
    { type: "feat", section: "New features" },
    { type: "fix", section: 'Bug fixes' },
    { type: "chore", section: 'gregregre...' },
  ]
}
```
> More configurations refer: [conventional-changelog-config-spec](https://github.com/conventional-changelog/conventional-changelog-config-spec/blob/master/versions/2.2.0/README.md)

## License

MIT © Geocld
