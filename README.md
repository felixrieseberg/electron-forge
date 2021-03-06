Electron Forge
--------------
[![Linux/macOS Build Status](https://travis-ci.org/electron-userland/electron-forge.svg?branch=master)](https://travis-ci.org/electron-userland/electron-forge)
[![Windows Build status](https://ci.appveyor.com/api/projects/status/79ae80nek1eucyy3?svg=true)](https://ci.appveyor.com/project/electron-userland/electron-forge)
[![Commitizen friendly](https://img.shields.io/badge/commitizen-friendly-brightgreen.svg)](http://commitizen.github.io/cz-cli/)
[![npm version](https://badge.fury.io/js/electron-forge.svg)](https://www.npmjs.com/package/electron-forge)
[![npm](https://img.shields.io/npm/dt/electron-forge.svg?maxAge=2592000)](https://www.npmjs.com/package/electron-forge)
[![license](https://img.shields.io/github/license/electron-userland/electron-forge.svg)](https://github.com/electron-userland/electron-forge/blob/master/LICENSE)
![status](https://img.shields.io/badge/Status-%20Ready%20for%20Awesome-red.svg)

A complete tool for building modern Electron applications.

Electron Forge unifies the existing (and well maintained) build tools for
Electron development into a simple, easy to use package so that anyone can
jump right in to Electron development.

# Getting Started

**Note**: Electron Forge requires Node 6 or above, plus git installed.

```bash
npm install -g electron-forge
electron-forge init my-new-app
cd my-new-app
electron-forge start
```

# Project Goals

1. Starting with Electron should be as simple as a single command.
2. Developers shouldn't have to worry about `babel`, `browserify`, `webpack`,
etc.  Everything should "just work" for them out of the box.
3. Everything from creating the project to packaging the project for release
should be handled by one dependency in a standard way while still offering users
maximum choice and freedom.

With these goals in mind, under the hood this project uses
[`electron-compile`](https://github.com/electron/electron-compile): a tool
that lets you use modern and futuristic languages inside Electron without
worrying about transpiling or build tooling.

# Usage

## Starting a new Project

```bash
npm install -g electron-forge
electron-forge init my-new-project
```

This command will generate a brand new project folder and install all your NPM
dependencies so you will be all set to go.  By default we will also install the
`airbnb` linting modules.  If you want to follow the `standard` linting rules
instead, use the `--lintstyle=standard` argument.

## Importing an existing Project

```bash
electron-forge import existing-project-directory
```

Given an existing Electron project, this command will attempt to interactively
navigate through the process of importing it to the Electron Forge format, so
the commands listed below can be used. This includes being prompted to remove
existing Electron build tools in favor of Electron Forge equivalents.

## Launching your Project

```bash
electron-forge start
```

Any arguments after "start" will be passed through to your application when
it's launched.

## Packaging your Project

```bash
electron-forge package
```

Yes, it really is that simple.  If you want to specify platform / arch, use the
`--platform=<platform>` and `--arch=<arch>` arguments.

## Generating a distributable for your Project

```bash
electron-forge make
```

This will generate platform specific distributables (installers, distribution
packages, etc.) for you. Note that you can only generate distributables for your
current platform.

## Linting your Project

```bash
electron-forge lint
```

## Publishing your Project

```bash
electron-forge publish
```

This will `make` your project and publish any generated artifacts.  By default it will publish to GitHub but you can change the publish target with `--target=YourTarget`.

# Config

Once you have generated a project, your `package.json` file will have some
default `forge` configuration.  Below is the reference structure for this
config object:

```javascript
{
  "make_targets": {
    "win32": ["squirrel"], // An array of win32 make targets
    "darwin": ["zip", "dmg"], // An array of darwin make targets
    "linux": ["deb", "rpm", "flatpak"] // An array of linux make targets
  },
  "electronPackagerConfig": {},
  "electronWinstallerConfig": {},
  "electronInstallerDMG": {},
  "electronInstallerFlatpak": {},
  "electronInstallerDebian": {},
  "electronInstallerRedhat": {}
}
```

## Possible `make` targets

| Target Name | Available Platforms | Description | Configurable Options | Default? | Requirements |
|-------------|---------------------|-------------|----------------------|----------|--------------|
| `zip`       | All                 | Zips your packaged application | None | Yes | `zip` on Darwin/Linux |
| `squirrel`  | Windows             | Generates an installer and `.nupkg` files for Squirrel.Windows | [`electronWinstallerConfig`](https://github.com/electron/windows-installer#usage) | Yes |  |
| `appx`      | Windows             | Generates a Windows Store package | [`windowsStoreConfig`](https://github.com/felixrieseberg/electron-windows-store#programmatic-usage) | No |  |
| `dmg`       | Darwin              | Generates a DMG file | [`electronInstallerDMG`](https://github.com/mongodb-js/electron-installer-dmg#api) | No |  |
| `deb`       | Linux               | Generates a Debian package | [`electronInstallerDebian`](https://github.com/unindented/electron-installer-debian#options) | Yes | [`fakeroot` and `dpkg`](https://github.com/unindented/electron-installer-debian#requirements) |
| `rpm`       | Linux               | Generates an RPM package | [`electronInstallerRedhat`](https://github.com/unindented/electron-installer-redhat#options) | Yes | [`rpm`](https://github.com/unindented/electron-installer-redhatn#requirements) |
| `flatpak`   | Linux               | Generates a [Flatpak](http://flatpak.org/) file | [`electronInstallerFlatpak`](https://github.com/endlessm/electron-installer-flatpak#options) | No | [`flatpak-builder`](https://github.com/endlessm/electron-installer-flatpak#requirements) |

## Configuring `package`

You can set `electronPackagerConfig` with **any** of the options from
[Electron Packager](https://github.com/electron-userland/electron-packager/blob/master/docs/api.md).

**NOTE:** You can also set your `forge` config property of your package.json to point to a JS file that exports the config object:

```js
{
  ...
  "config": {
    "forge": "./forge.config.js"
  }
  ...
}
```

**NOTE:** If you use the JSON object then the `afterCopy` and `afterExtract` options are mapped to `require`
calls internally, so provide a path to a file that exports your hooks and they will still run.  If you use
the JS file method mentioned above then you can use functions normally.

## Possible `publish` targets

| Target Name | Description | Required Config |
|-------------|-------------|-----------------|
| github      | Makes a new release for the current version (if required) and uploads the make artifacts as release assets | `process.env.GITHUB_TOKEN` - A personal access token with access to your releases <br />`forge.github_repository.owner` - The owner of the GitHub repository<br />`forge.github_repository.name` - The name of the GitHub repository <br />`forge.github_repository.draft` - Create the release as a draft, defaults to `true` <br />`forge.github_repository.prerelease` - Identify the release as a prerelease, defaults to `false` |

## Custom `make` and `publish` targets

You can make your own custom targets for the `make` and `publish` targets.  If you publish them as `electron-forge-publisher-{name}` or `electron-forge-maker-{name}` you can then just specify `{name}` as your make / publish target.  The API for each is documented below.

### API for `make` targets

You must export a Function that returns a Promise.  Your function will be called with the following parameters.

* `appDir` - The directory containing the packaged application
* `appName` - The productName of the application
* `targetArch` - The target architecture of the make command
* `forgeConfig` - An object representing the users forgeConfig
* `packageJSON` - An object representing the users package.json file

Your promise must resolve with an array of the artifacts you generated.

### API for `publish` targets

You must export a Function that returns a Promise.  Your function will be called with the following parameters.

* artifactPaths - An array of absolute paths to artifacts to publish
* packageJSON - An object representing the users package.json file
* forgeConfig - An object representing the users forgeConfig
* authToken - The value of `--auth-token`
* tag - The value of `--tag`

You should use `ora` to indicate your publish progress.
