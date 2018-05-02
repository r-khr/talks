# Angular 2 Style Guide

1.  Using Angular/Cli
2.  Why Typescript matters
3.  Logic/Presentation components
4.  What is redux?
5.  NG-RX
6.  Rxjs Observables

### 1. Using Angular/Cli

* Why is it important to use Angular Cli for your build process? How does `.angular-cli.json` help manage application rules?

  Angular/Cli allows for easy build process and project management of Angular 2. The cli json provides rules for testing, styles, importing external scripts and environment variables.

- What are environment variables?

  Angular environment variables are configurations that allow for conditional logic depending on the build processes. When configured with `angular/cli` it is possible to build application with a set conditions that can be picked up by the front end. Ex. Calling `api-endpoints vs. mock json files`.

- How to setup and use environment variables?

  Environment variables require modication to the files within `app/environments` folder. Depending on the file extention you can provide a different combination of conditions.

  Creating a file `environment.prod.ts` with conditions.

  ```js
  export const environment: IEnvironment = {
    isProduction: true,
    isLocal: false,
  };
  ```

  Within `.angular-cli.json`:

  ```json
  {
    "project": {
      "name": "rbc-wealth-management"
    },
    "apps": [
      {
        ...
        "environmentSource": "environments/environment.ts",
        "environments": {
          "prod": "environments/environment.prod.ts",
          "mock": "environments/environment.mock.ts",
          "dev": "environments/environment.dev.ts",
          "qa": "environments/environment.qa.ts",
        },
        ...
      }
    ]
  }
  ```

  Within angular code:

  ```js
  import { environment } from '../environments';

  if (environment.isProduction) {
    callApi();
  } else {
    callMockJson();
  }
  ```

### 2. Why Typescript matters?

* When typescript matters?
  Typescript provide significant utility for large applications.

* Why use interfaces to describe your data shapes?
* Why use enums to describe your types?
* Why/How to use abstract classes?

### 3. Logic/Presentation Components

* Dumb vs. Smart. Why does it matter to create a distinction of concerns?
* Why are dumb components important?
* Why are smart components important?

### 4. What is Redux?

* What is redux?
  Redux makes it easy to manage the state of your application.

* How does redux work?
* Working with redux in an Angular 2 application?

### 5. NG-RX

* What is ng-rx?
* What are ng-rx/effects?

### 6. Rxjs Observables

* What are Observables?
* Why do observables matter?
* How to use observables with redux?
