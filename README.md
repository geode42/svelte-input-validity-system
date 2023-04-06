### Context
I had a bunch of custom input components in a Svelte thing I was working on, and needed a way to tell if they were all valid.
I could've assigned the value of each input to its own variable, but that'd be annoying, plus it would've reserved names pretty quickly, which is something I try to avoid.
So instead, I created this system.

### Explanation
This system needs to ask every element whether it's valid or not. To do that, every element that connects to the system gives it an "amIValid" function, which does what the name implies. These functions are placed inside of the aptly named "amIValidFunctions" array. When a component's validity changes, it runs the "validationUpdate" function which runs the amIValid functions in that array, and sets "everythingIsValid" accordingly

And yeah, that's pretty much it. Hopefully this explanation made sense, you can look at the code if you want to understand it better.

### For each component that supports this system
```typescript
import { onDestroy } from 'svelte'

/* -------------------------------------------------------------------------- */
/*                                 Validation                                 */
/* -------------------------------------------------------------------------- */
// Update this variable when necessary
export let validity = false

export let validationConnectFunction: (amIValid: () => boolean) => void = () => {},
           validationDisconnectFunction: (amIValid: () => boolean) => void = () => {},
           validationUpdate: () => void = () => {}

// Return the value of validity
const amIValid = () => validity

// Connect when this script runs, which apparently happens every time the component's added to the DOM
validationConnectFunction(amIValid)
// Disconnect when element is removed from DOM
onDestroy(() => validationDisconnectFunction(amIValid))
// Run when validity changes
$: (validity => validationUpdate())(validity)
/* -------------------------------------------------------------------------- */
/*                               End validation                               */
/* -------------------------------------------------------------------------- */
```



### In the main file (i.e. app.svelte)
```typescript
/* -------------------------------------------------------------------------- */
/*                                 Validation                                 */
/* -------------------------------------------------------------------------- */
let everythingIsValid = false

let amIValidFunctions: Array<() => boolean> = []

// A component should call this function when its validity changes
function validationUpdate() {everythingIsValid = !amIValidFunctions.map(i => i()).some(i => !i)}

// A component should call this when it wants to be connected to the validation system
function validationConnectFunction(amIValidFunction: () => boolean) {
  amIValidFunctions.push(amIValidFunction)
  validationUpdate()
}

// A component should call this when it wants to be disconnected from the validation system
function validationDisconnectFunction(amIValidFunction: () => boolean) {
  amIValidFunctions = amIValidFunctions.filter(i => i != amIValidFunction)
  validationUpdate()
}

// Example usage: <COMPONENT {...validationProps} />
const validationProps = {
	validationConnectFunction: validationConnectFunction,
	validationDisconnectFunction: validationDisconnectFunction,
	validationUpdate: validationUpdate
}
/* -------------------------------------------------------------------------- */
/*                               End validation                               */
/* -------------------------------------------------------------------------- */
```
