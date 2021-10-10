# angular-jest-cypress

## Setup with Angular 12

### Cypress

Follow the instructions here:
https://github.com/cypress-io/cypress/tree/develop/npm/cypress-schematic

Add Cypress by running the following commands:

```bash
ng add @cypress/schematic
```

### Jest

Follow the instructions here:

https://github.com/just-jeb/angular-builders/tree/master/packages/jest

Remove karma & jasmine:

```bash
npm remove jasmine-core @types/jasmine karma karma-chrome-launcher karma-coverage-istanbul-reporter karma-jasmine karma-jasmine-html-reporter
rm ./karma.conf.js ./src/test.ts
```

```bash
npm i -D jest @types/jest @angular-builders/jest
```

In `angular.json` add:

```json
{
  ...
  "projects": {
      ...
      "[your-project]": {
          ...
          "architect": {
                  ...
                  "test": {
                            "builder": "@angular-builders/jest:run",
                            "options": { }
}
```

Add `jest.config.js` to the root of your project directory

```javascript
// jest.config.js
module.exports = {
  preset: "jest-preset-angular",
  setupFilesAfterEnv: ["<rootDir>/setup-jest.ts"],
  testMatch: ["<rootDir>/src/**/*.spec.ts"],
};
```

### Eslint + Prettier

Base Angular schematic:

https://github.com/angular-eslint/angular-eslint

```bash
npm i -D eslint-plugin-import eslint-config-prettier eslint-plugin-prettier prettier
```

Create `.prettierrc` and add:

```json
{
  "singleQuote": true,
  "printWidth": 120,
  "overrides": [
    {
      "files": "*.html",
      "options": {
        "parser": "html"
      }
    },
    {
      "files": "*.component.html",
      "options": {
        "parser": "angular"
      }
    }
  ]
}
```

Create `.prettierignore` file:

```text
/dist
/coverage
```

Register the eslint prettier plugins to eslint config:

```json
{
  "root": true,
  "ignorePatterns": ["projects/**/*"],
  "overrides": [
    {
      "files": ["*.ts"],
      "parserOptions": {
        "project": ["tsconfig.json", "e2e/tsconfig.json"],
        "createDefaultProgram": true
      },
      "plugins": ["prettier"],
      "extends": [
        "plugin:@angular-eslint/recommended",
        "plugin:@angular-eslint/template/process-inline-templates",
        "prettier"
      ],
      "rules": {
        "@angular-eslint/directive-selector": [
          "error",
          {
            "type": "attribute",
            "prefix": "hots",
            "style": "camelCase"
          }
        ],
        "@angular-eslint/component-selector": [
          "error",
          {
            "type": "element",
            "prefix": "hots",
            "style": "kebab-case"
          }
        ],
        "prettier/prettier": "error"
      }
    },
    {
      "files": ["*.html"],
      "plugins": ["prettier"],
      "extends": ["plugin:@angular-eslint/template/recommended", "prettier"],
      "rules": {
        "prettier/prettier": "error"
      }
    }
  ]
}
```

TODO:
Add formatting/linting pre-commit/push hook

## Other resources

Also helpful:
https://medium.com/@rjnay1984/creating-a-modern-angular-app-with-jest-cypress-eslint-and-prettier-75411086872e
https://medium.com/angular-in-depth/angular-cli-ng-test-with-jest-in-3-minutes-v2-1060ddd7908d
