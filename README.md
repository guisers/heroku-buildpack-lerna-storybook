# Heroku Lerna Storybook Buildpack

Based on:
- [heroku-buildpack-storybook](https://github.com/Root-App/heroku-buildpack-storybook)
- [heroku-buildpack-monorepo](https://github.com/lstoll/heroku-buildpack-monorepo)

After `lerna` is installed by Heroku's NodeJS buildpack, this buildpack runs `lerna bootstrap`, `lerna run build`, and `lerna run build-storybook` for the target project, then takes the generated static Storybook files and moves them into a `build` folder in the root for use by Heroku's static buildpack.

## Usage

### Requirements
Designed for a lerna monorepo with the following project structure:
```
|-- root/
    |-- project0/
        |-- .storybook/
            |-- config.ts
            |-- webpack.config.js
        |-- package.json
        |-- static.json
    |-- project1/
        --- package.json
    |-- package.json
    |-- lerna.json
```

### Steps
To deploy the storybook for `project0` in the above example, we need to:
1. Make sure the root `package.json` includes `lerna` as a dev dependency.
2. Set the config var `YARN_PRODUCTION` to `false` so that `lerna` does not get pruned away in the NodeJS buildpack step.
3. Set the config var `APP_BASE` in Heroku to `project0`.
4. Make sure the the `package.json` for `project0` has a `build-storybook` script defined, eg.
```
"scripts": {
  "build-storybook": "build-storybook"
}
```
5. Add this buildpack **after** [heroku-buildpack-nodejs](https://github.com/heroku/heroku-buildpack-nodejs) and **before** [heroku-buildpack-static](https://github.com/heroku/heroku-buildpack-static).

### Troubleshooting
If this buildpack is constantly crashing on the `build-storybook` step, try increasing memory for Node in your `build-storybook` script, eg.
```
"scripts": {
  "build-storybook": "NODE_OPTIONS=--max_old_space_size=4096 build-storybook"
}
```