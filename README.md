# svelte-validity-checking-standard

## Types
```typescript
type amIValidFunctionType = {
    (): boolean
}

type validationRegisterFunctionType = {
    (amIValid: amIValidFunctionType)
}
```

## Component
```svelte
/* -------------------------------------------------------------------------- */
/*                                  Validity                                  */
/* -------------------------------------------------------------------------- */
export let validity = false
export let validationRegisterFunction: validationRegisterFunctionType = (amIValid) => {}
export let validityChangesFunction: CallableFunction = () => {}


validationRegisterFunction(() => {return validity})
$: ((validity) => {validityChangesFunction()})(validity)
/* -------------------------------------------------------------------------- */
/*                                End validity                                */
/* -------------------------------------------------------------------------- */
```



## App
```svelte
/* -------------------------------------------------------------------------- */
/*                                 Validation                                 */
/* -------------------------------------------------------------------------- */
const amIValidFunctions = []

let everythingIsValid = false
function validityChangesFunction() {
  for (const amIValidFunction of amIValidFunctions) {
    if (!amIValidFunction()) {
      everythingIsValid = false
      return
    }
  }
  everythingIsValid = true
}
function validationRegisterFunction(amIValidFunction) {
  amIValidFunctions.push(amIValidFunction)
  validityChangesFunction()
/* -------------------------------------------------------------------------- */
/*                               End Validation                               */
/* -------------------------------------------------------------------------- */
```
