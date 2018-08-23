# Auto Save Architecture

**Example of an auto-save problem?**

Imagine building an application that required auto saving capabilities akin to google sheets. When the user updates a value the web application saves the most recent entry into the database.

**How to efficiently address this problem?**

When a user stops typing into an input field for a set duration we send the value to the database.

## Why?

**Explain why?**

## Requirement

Save row from a form data table when user stops making modification. Each row is a unique entry in the database and it's validity must be checked before submitting to API. It should be possible to delete and add rows to the data table.

## Technical Breakdown to Achieve Requirement

1.  Track each form data table entry as one piece of data
2.  Each entry (referred to as DataEntry) has UI required properties to properly support auto save functionality
    - **guid**: a user interface ID used by the web application to track table row entries when saving occurs
    - **isValid**: a boolean state to track validity of table row entries **maybe use a selector instead of a value on the data**
    - **isSynced**: a boolean state to track if a row's data has been saved to the Data Base
    - **isSavingInProgress**: a boolean state to track if a row's data is currently being saved (set to true between request and response for a particular row)
3.  Track new data table row entries created by UI and match database IDs for these entries on their initial save request.

## How we determine if a row requires saving

The conditions to determine if a row (DataEntry) requires saving.

| Condition                    | Explanation                                                 |
| ---------------------------- | ----------------------------------------------------------- |
| isValid === true             | DataEntry passes validation specific to the entry type      |
| isSynced === false           | DataEntry data has been modified since it's last save       |
| isSavingInProgress === false | DataEntry does **not** have any outstanding saving requests |

A memoize selector is used to aggregate all rows that fit the above conditions (with some extra properties depending on section). Subscribing to this selector allows us to dispatch an action which triggers the save request for this data.

## Life Cycle of a DataEntry

**Add Diagram!**

1.  Load Data on Page Load
2.  Initial Validation of Data
3.  Data is ready to be interacted with
4.  User makes modifications
5.  Auto Save is triggered for DataEntries that meet conditions
6.  Auto Save Request Sequence
7.  Return to Step 3.

### 1. Load Data on Page Load

On application start a request is sent to fetch all data table row entries.

**Explain the issue first and then explain the solution, change the order.**
When this data is received the UI should provide a guid to each row so that we can universally track existing rows with an ID from the database and new rows created by the UI which haven't been saved yet.

```ts
// This is a sample DataEntry object created by Step 1.
{
  ...DataEntry,
  guid: '39a0689e-a44b-ab92-94fb-194408f30e0e'
}
```

### 2. Initial Validation of Data

Once all data has been received in _Step 1_ we would like to cross validate all entries to check for their initial validity. This would provide some indicators to the user if anything data has issues when first loading the application.

```ts
// This is a sample DataEntry object created by Step 2.
{
  ...DataEntry,
  isValid: true/false
}
```

### 3. Data is ready to be interacted with

This is when the application is waiting for the user to make modifications. No auto-save requests triggered at this point.

### 4. User makes modifications

When a user makes modifications to a DataEntry the update is reflected in Redux. This updates the state of a DataEntry to be `isSynced === false` along with its validity.

```ts
// This is a sample DataEntry object created by Step 4.
{
  ...updateDataEntry,
  isValid: true/false, // Depending on validity
  isSynced: false
}
```

### 5. Auto Save is triggered for DataEntries that meet conditions

After a user has made modification and no changes have been made for a set amount of time we trigger an auto save action.

Explain below

```ts
// Example of a selector filtering dataEntries that require saving
const dataEntriesRequiringSavingSelector = createSelector(
  dataEntriesSelector, // another memoize selector with grabs all data rows
  dataEntries =>
    // Filtered entries that meet auto save requirements
    dataEntries.filter(
      item => !item.isSynced && item.isValid && !item.isSavingInProgress,
    ),
);
```

Explain below

```ts
// Amount of milliseconds after which we trigger auto save
const autoSaveDebounce = 500;

function isArrayNotEmpty = (array: any[]) => array.length > 0;

// In the specific data service add a subscription to the above selector piped through the auto save debounce timer
class DataService {
  constructor(private store: Store<IStore>) {
    store
      .select(dataEntriesRequiringSavingSelector)
      .pipe(
        filter(isArrayNotEmpty),
        debounceTime(autoSaveDebounce),
      )
      .subscribe(dataEntriesRequiringSaving => {
        store.dispatch(
          // Use an action to trigger the save of these data entries
          {
            type: 'SAVE_REQUEST_TABLE_ROW_ENTRIES',
            payload: dataEntriesRequiringSaving,
          },
        );
      });
  }
}
```

```ts
// This is a sample DataEntry object created by Step 5.
function formAutoSaveReducer(state: DataEntry[], action) {
  switch (action.type) {
    case 'SAVE_REQUEST_TABLE_ROW_ENTRIES':
      // Update
      return state.map(dataEntry =>
        action.payload.find(update => )
      )
      {
        ...DataEntry,
        isSynced: true, // Since we began saving we want to reset isSynced to true in order to capture any updates that happen during the save request
        isSavingInProgress: true, // Save request is triggered
      };
  }
}
```

We use `SAVE_REQUEST_TABLE_ROW_ENTRIES` in the redux reducer to update `isSynced` and `isSavingInProgress` for the TableRowEntries which have been flagged for update.

// Consolidate the explanation for these keys with the ones at the beginning of the article

- `isSavingInProgress` is important so that no other save requests happen for these particular table row entries while they are being saved.
- `isSynced` is important so that if a row is modified by the user during the saving progress we can track that modification has happen and send another request when the current save request is complete. Looking back you can see that `isSynced` is set to false when a row is modified by the user in **Step 4**.

### 6. Auto Save Request Sequence

Once an action to save dataEntries has been triggered an API call will be trigger to save these in the database. Below is a simplified effect to explain how this works. The effect listens for an action with a type `SAVE_REQUEST_TABLE_ROW_ENTRIES` and calls `SaveTableRowEntriesApiRequest()` with the payload provided in **Step 5**. Once the request is complete this effect returns an action with a type `SAVE_RESPONSE_TABLE_ROW_ENTRIES` and provides both the response from the API and the request from the action that triggered the effect. This is important for the reducer in order to know which rows to update using the **request** and [which ids to overwrite using the **response**.] -> explain this part, why?

```ts
// Data service as shown in step 5 but extended with the effect to handle save request.
class DataService {
  constructor(private actions$: Actions) {
    // Same selector as in step 5 would be here to trigger the bellow effect
  }

  // An Effect which listens to the above dispatched save action
  @Effect()
  saveTableRowEntries$: Observable<Action> = this.actions$
    .ofType('SAVE_REQUEST_TABLE_ROW_ENTRIES')
    .pipe(
      map(action =>
        // How to handle Errors
        SaveTableRowEntriesApiRequest(action.payload) // Api request to save table row entries
          .map(apiResponse => ({
            type: 'SAVE_RESPONSE_TABLE_ROW_ENTRIES',
            payload: {
              response: apiResponse, // we need response to map database IDs to new TableRowEntries
              request: action.payload, // we need request in order to know which isSavingInProgress properties to change to false
            },
          })),
      ),
    );
}
```

When `SAVE_RESPONSE_TABLE_ROW_ENTRIES` action type is dispatched we use `payload.request` to update TableRowEntries as shown below. We change `isSavingInProgress` to false in order to trigger any future updates or if during the saving process more updates have been made we return to **Step 5**. Otherwise if no changes have been made during the saving process we return to **Step 3**.

```ts
// This is a sample DataEntry object created by Step 6.
const request = action.payload.request;
const response = action.payload.response;

dataEntries.map(
  DataEntry =>
    // Does this DataEntry exist in the request payload?
    request.find(savedTableRow => savedTableRow.guid === DataEntry.guid) !==
    undefined
      ? // if yes
        {
          ...DataEntry,
          id: response.find(
            responseTableRow => responseTableRow.guid === DataEntry.guid,
          ).id, // we grab id from api and map it to the object that was being saved
          isSavingInProgress: false, // Save request is completed and we change this value to false
        }
      : // if no
        DataEntry,
);
```
