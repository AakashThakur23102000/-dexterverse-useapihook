# Installation

To install and set up the library, run:

```bash
  npm i useapihook-dexterverse
```
    
# Purpose

`useApiHook` is a custom React/React Native hook for handling API requests with configurable actions based on success or error response codes. It manages the loading state, response data, and error states while allowing you to customize the API logic.

# Steps to Set Up in Your Project

## 1. Create a new file for all API functions in the project.

In this file, all the project APIs will be handled. Do not call the API in other files, as the purpose of this hook is to centralize API calls in one place.

Create a file `ApiFunctions.ts` and add your API functions as shown below.

## 2. Define Your API Functions in `ApiFunctions.ts`

In the `ApiFunctions.ts` file, define all your API functions. Below is an example of how to structure an API function:

```typescript
var mainURL = "any base url"

export const FIRST_API_FUNC = async (apiCallingFunctionQuery: any) => {
    return {
        fullUrl: mainURL+"furtherUrl",
        method: 'GET',
        token: null,
        customHeaders: null,
        successCodeWithAction: null,
        errorCodeWithAction: null
    };
};

export const SECOND_API_FUNC = async (apiCallingFunctionQuery: any) => {
    return {
        fullUrl: mainURL+"furtherUrl",
        method: 'POST',
        token: "Bearer any token",
        customHeaders: { 'Content-Type': 'application/json' },
        successCodeWithAction: [{
            code: 200,
            action: () => {
                console.log("Api hit successfully.")
                apiCallingFunctionQuery.navigation.navigate("DemoPage")
            }
        }],
        errorCodeWithAction: [{
            code: 400,
            action: () => {
                console.log("Status code is 400.")
            },
        }]
    };
};
```

```typescript 
// Type for API response configuration
type ApiFunctionTypes = {
    fullUrl: string;
    method: 'GET' | 'POST' | 'PUT' | 'DELETE';
    token: string | null;
    customHeaders: Record<string, string> | null;
    successCodeWithAction: Array<{ code: number; action: () => void }> | null;
    errorCodeWithAction: Array<{ code: number; action: () => void }> | null;
};
```
## *** Note on Custom Headers

If you're passing custom headers in the `customHeaders` field, **do not include the token** manually. The token will be added automatically by the hook when needed.

### Example:

```typescript
const customHeaders = {
  'Content-Type': 'application/json',
  // Do not add the 'Authorization' header manually. The hook will handle it automatically.
};
```

## 3. Use the API Hook in Your Component

After defining your API functions in `ApiFunctions.ts`, you can now use the `useApiHook` in your components to fetch data.

Below is an example of how to use the hook for making API calls in your component:

```typescript
//import useApiHook from your component and your api function.
import useApiHook from 'useapihook-dexterverse'

import { FIRST_API_FUNC, SECOND_API_FUNC } from './src/api/ApiFunctions';

const MyComponent = ({ navigation }: { navigation: any }) => {

  // First Example
  const {
    apiData: firstApiData,
    loadingState: firstApiLoading,
    refetchingApiFunction: firstRefetchFunction,
    apiError: firstApiError
  } = useApiHook({
    apiCallingFunction: FIRST_API_FUNC,
    apiCallingFunctionQuery: [{ navigation: navigation }],
    apiPayload: [{ name: "Aakash" }],
    runOnTimeOfScreenMount: true,
    initialLoadingState: true,
    apiCustomReturnFunction: (data: any) => {
      return data;
    },
    onErrorReturnFunction: (err: any) => {
      return err;
    },
  });

  // Second Example
  const {
    apiData: secondApiData,
    loadingState: secondApiLoading,
    refetchingApiFunction: secondRefetchFunction,
    apiError: secondApiError
  } = useApiHook({
    apiCallingFunction: SECOND_API_FUNC,
    apiCallingFunctionQuery: [{ navigation: navigation }],
    apiPayload: [{ demo: "Demo" }],
    runOnTimeOfScreenMount: false,
    initialLoadingState: true,
    apiCustomReturnFunction: (data: any) => {
      return data;
    },
    onErrorReturnFunction: (err: any) => {
      return err;
    },
  });

  // Render or handle the API response data
  if (firstApiLoading || secondApiLoading) {
    return <Text>Loading...</Text>;
  }

  if (firstApiError || secondApiError) {
    return <Text>Error: {firstApiError || secondApiError}</Text>;
  }

  return (
    <View>
      <Text>First API Response: {JSON.stringify(firstApiData)}</Text>
      <Text>Second API Response: {JSON.stringify(secondApiData)}</Text>
    </View>
  );
};

export default MyComponent;
```




| Prop Name                       | Description | Code Example |
|----------------------------------|-------------|--------------|
| `apiCallingFunction`             | The API function that will be called inside the hook. This function handles making the actual API request. Example: `FIRST_API_FUNC` or `SECOND_API_FUNC`. | `apiCallingFunction: FIRST_API_FUNC` |
| `apiCallingFunctionQuery`        | A list of query parameters or additional arguments passed to the `apiCallingFunction`. This can be used as a prop named apiCallingFunctionQuery in the Step 2.<br/> ***Note- The bracket is just the field where you put your query object so always wrap it with the brackets. | `apiCallingFunctionQuery: [{ navigation: navigation }]` |
| `apiPayload`                     | The payload that will be sent with the API request. For example, `[{ name: "Aakash" }]` or `[{ demo: "Demo" }]`<br/> ***Note- The bracket is just the field where you put your payload so always wrap it with the brackets.  | `apiPayload: [{ name: "Aakash" }]` |
| `runOnTimeOfScreenMount`         | A boolean flag to specify if the API call should be triggered when the component mounts. If `true`, the API call is made on component mount. | `runOnTimeOfScreenMount: true` |
| `initialLoadingState`            | A boolean flag to indicate the initial state of the API call. If `true`, it shows the loading indicator when the hook is initialized. | `initialLoadingState: true` |
| `apiCustomReturnFunction`        | A custom function to handle the successful API response data. It allows you to modify or process the response before it's returned. Example: `(data: any) => { return data; }`. | `apiCustomReturnFunction: (data: any) => { return data; }` |
| `onErrorReturnFunction`          | A custom function to handle errors. This function will be called if the API request fails. Example: `(err: any) => { return err; }`. | `onErrorReturnFunction: (err: any) => { return err; }` |
| `apiData`                        | The response data from the API call, which is returned once the API request is successful.<br/>***Note - These can be renamed accordingly.<br/> Example: `firstApiData` or `secondApiData`. | `apiData: firstApiData` |
| `loadingState`                   | A boolean indicating whether the API call is still loading or not.<br/>***Note - These can be renamed accordingly.<br/> Example: `firstApiLoading` or `secondApiLoading`. | `loadingState: firstApiLoading` |
| `refetchingApiFunction`          | A function that can be used to re-trigger the API call, usually in case of failure or to refresh or recall the api.<br/>***Note - These can be renamed accordingly.<br/> Example: `firstRefetchFunction` or `secondRefetchFunction`. | `refetchingApiFunction: firstRefetchFunction` |
| `apiError`                       | Stores the error returned from the API request, if any.<br/>***Note - These can be renamed accordingly.<br/> Example: `firstApiError` or `secondApiError`. | `apiError: firstApiError` |
