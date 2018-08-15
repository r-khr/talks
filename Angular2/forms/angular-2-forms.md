# Angular 2 Form Architecture Patterns

This article will discuss the following topics:

- Thinking about `submit` vs. `auto-save` forms
- `Submit` forms
- `Auto-save` forms
- Unique considerations to `Auto-save` forms
- Difference between `submit` and `auto-save` forms
- Managing form data
- Managing complex form validation
- Managing api requests on form updates
- Working with NGRX-Forms

## Thinking about `submit` vs. `auto-save` forms

All forms share the same building blocks (input fields) but can have many different implementation requirements. Business logic continuously changes or becomes more clarified which requires architecture to be flexible enough to pivot to requirements in a timely manner.

Working on a large financial calculator has made me aware of the challenges relating to managing data. The system was designed in Angular 5 with NG-RX as the Redux implementation.

### `Submit` forms

- Examples: registration forms, sign-up forms, questionnaires
- Submit type forms aggregate input values and attempt to submit on button press or any other type of trigger
- Any validations are triggered by incorrect input entry which blocks saving of data
- Form state doesn't need to be stored after submitting data

```ts
interface Form {
  name: string;
  age: number;
  address: string;
  citizenship: string;
}
```

### `Auto-save` forms

- Examples: user profiles, financial table forms, forms that link to data visualization
- Auto-save forms continuously keep input state and attempt to submit when any value has been changed and the form is valid
- Any validations are triggered by incorrect input entry which blocks saving of data
- Form state is stored to keep track of changes and validity for continuous data submitting

```ts
interface AutoSave {
  isValid: boolean;
  isPristine: boolean;
}

interface Form extends AutoSave {
  name: string;
  age: number;
  address: string;
  citizenship: string;
}
```

#### Unique considerations to `Auto-save` forms

- Managing form state during post request (important when server responses are slow)
- Validating state during post request
- Managing state when post request succeeds
- Managing state when post request fails

```ts
interface AutoSave {
  isValid: boolean;
  isPristine: boolean;
  isSavingInProgress: boolean;
}

interface Form extends AutoSave {
  name: string;
  age: number;
  address: string;
  citizenship: string;
}
```

### Difference between forms

- Continuous tracking of state and validity
- Tracking state changes to determine when to trigger auto-save

```ts
function formAutoSaveReducer(state, action) {
  switch (action.type) {
    case FormDataUpdate:
      return {
        ...state,
        ...action.payload,
        isPristine: false, // some update happened
      };
    case FormDataPostRequest:
      return {
        ...state,
        isPristine: true,
        isSavingInProgress: true, // save request start
      };
    case FormDataPostReceive:
    case FormDataPostError:
      return {
        ...state,
        isSavingInProgress: false, // save request complete
      };
  }
}

class MyApp {
  constructor(store) {
    const formData$ = store
      .select(FormDataSelector)
      .pipe(
        filter(
          item => item.isValid && !item.isPristine && !item.isSavingInProgress,
        ),
      );

    formData$.subscribe(data => {
      store.dispatch(SaveFormData(data));
    });
  }
}
```

## Managing form data

Angular provides a couple built in patterns for form management

### Template driven forms

### Reactive forms

## Managing complex form validations

## Managing api requests on form updates

## Working with NGRX-Forms
