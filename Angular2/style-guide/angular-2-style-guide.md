# Angular 2 Style Guide

1.  Using Angular/Cli
2.  Typescript
3.  Logic/Presentation components
4.  What is redux?
5.  NG-RX
6.  Rxjs Observables

## 1. Using Angular/Cli

### Why is it important to use Angular Cli for your build process? How does `.angular-cli.json` help manage application rules?

Angular/Cli allows for easy build process and project management of Angular 2. The cli json provides rules for testing, styles, importing external scripts and environment variables.

### What are environment variables?

Angular environment variables are configurations that allow for conditional logic depending on the build processes. When configured with `angular/cli` it is possible to build application with a set conditions that can be picked up by the front end. Ex. Calling `api-endpoints vs. mock json files`.

### How to setup and use environment variables?

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

## 2. Typescript

### Why and when typescript matters?

Typescript provide significant utility for large applications. It creates sanity of mind when working with complex logic. Editors like VSCode provide error checking to tell you if there are mistakes with how you are accessing values if everything is type casted.

Typescript is great when you are working on a complex application. It also provides a lot of value when working with a big team because it creates readable code.

### Why use interfaces to describe your data shapes?

Writing interfaces makes working on complex application much more confortable.
Make sure you always type all your function parameters and return values. This will provide you awesome intellisense in editors like VSCode.

```ts
interface IncomeItem {
  id: number;
  amount: number;
  date: Date;
  description: string;
  isMonthly: boolean;
}

function validateIncomeItems(collection: IncomeItem[]): IncomeItem[] {
  const validatedCollection = collection.map(...);
  return validatedCollection;
}
```

Writing OOP interfaces also creates value.

```ts
interface FinancialItem {
  id: number;
  isValid: boolean;
  isSaved: boolean;
  lastUpdated?: Date;
}

interface IncomeItem extends FinancialItem {
  amount: number;
  date: Date;
  description: string;
  isMonthly: boolean;
}
```

### Why use enums to describe your types?

Specifying types with enums creates consistancy with api variables. Additionally it makes it much easier to make modification to these variables in the future.

```ts
enum incomeEmun {
  employmentIncome = 'Employment_Income',
  rentalIncome = 'Rental_Income',
  taxableIncome = 'Taxable_Income',
  taxFreeIncome = 'Tax_Free_Income',
}

interface IncomeItem extends FinancialItem {
  amount: number;
  date: Date;
  description: string;
  isMonthly: boolean;
  type: incomeEmun;
}
```

### Why/How to use abstract classes?

Abstract classes allow to create reusabled OOP class logic that can be inherited by any class that extends from the abstract class.

```ts
export abstract class TableRowComponent<T extends ITableRowEntry> {
  @Input() data: T;
  @Output() saveDataToStore: EventEmitter<T> = new EventEmitter<T>();
  @Output() saveDataToApi: EventEmitter<T> = new EventEmitter<T>();

  abstract buildFormGroupSkeleton(): FormGroup;
  abstract updateTableRowFormGroup(data: T): void;
  abstract createTableRow(
    updatedValues: any,
    originalData: T,
    tableRowFormGroup: FormGroup,
  ): T;

  deleteItem(item: T) {
    // delete item logic
  }
}

export class GovernmentBenefitFormComponent extends TableRowComponent<
  ICashFlowGovernmentBenefitTableRow
> {
  constructor() {
    super();
  }

  buildFormGroupSkeleton() {
    // return FormGroup
  }

  createTableRow(updatedValues, originalData, tableRowFormGroup) {
    // return tableRow
  }

  updateTableRowFormGroup(data: ICashFlowGovernmentBenefitTableRow) {
    // return tableRow
  }
}
```

## 3. Logic/Presentation Components

### Dumb vs. Smart. Why does it matter to create a distinction of concerns?

### Why are dumb components important?

### Why are smart components important?

## 4. What is Redux?

### What is redux?

Redux makes it easy to manage the state of your application.

### How does redux work?

Redux works by listening to actions which specify how to change current state.

### Working with redux in an Angular 2 application?

## 5. NG-RX

### What is ng-rx?

### What are ng-rx/effects?

## 6. Rxjs Observables

### What are Observables?

### Why do observables matter?

### How to use observables with redux?
