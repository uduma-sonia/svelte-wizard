# Svelte-wizard

<div align="center">
  <h3 align="center">A flexible and highly customizable stepper component for Svelte, perfect for building multi-step forms with progress indicators.</h3>
  <p align="center"> Tailor each step's style, navigation, and form state management to fit your app’s flow—without sacrificing flexibility or design.</p>

  <p align="center">
  <a href="https://npm.im/svelte-wizard"><img src="https://img.shields.io/npm/v/svelte-wizard.svg?color=brightgreen&style=flat-square" alt="Package version."></a>
  <a href="http://makeapullrequest.com"><img src="https://img.shields.io/badge/PR(s)-welcome-brightgreen.svg?style=flat-square" alt="Make a pull request."></a>
  </p>
</div>

<br/>
<br/>

![GIF of Svelte Stepper in action](https://res.cloudinary.com/dbqgv8zl7/image/upload/v1731450316/record_jrxr2b.gif)

### Interactive [Demo](https://svelte-wizard.netlify.app/)

### [Playground](https://svelte.dev/playground/740a81ce5023497eba5c4ebd7f8ec6ad?version=5.1.16)

<br/>

# Installation

```
$ npm i svelte-wizard
```

<br/>

# Basic Usage

```
<script>
	import { WizardComponent } from 'svelte-wizard';
	import StepOne from './StepOne.svelte';

	let stepsList = [
		{
			step: StepOne,
			title: 'Step one'
		},
		{
			step: StepTwo,
			title: 'Step two'
		},
		{
			step: StepThree,
			title: 'Step three'
		}
	];
</script>

<WizardComponent {stepsList} />
```

<br/>

## With Options

```
<script>
	import {WizardComponent} from 'svelte-wizard'

	let stepsList = [...];

	let options = {
		showTitles: true,
		showCheckIcon: false,
	};
</script>

<WizardComponent stepsList={stepsList} options={options}  />
```

<br/>

## With ClassNames

```
<script>
	import {WizardComponent} from 'svelte-wizard'

	let stepsList = [...];

	let customClassnames = {
		activeTitleClass: 'activeTitleClass'
	};

</script>

   <WizardComponent stepsList={stepsList} customClassnames={customClassnames}  />
```

 <br/>
<br/>

# Props

Svelte Wizard library features a primary component that offers several props for customization, allowing for extensive flexibility and tailored configurations to suit various use cases

<br/>

### StepsList

| Props | Description                                    | Type                                 |
| ----- | ---------------------------------------------- | ------------------------------------ |
| step  | Svelte component to be rendered for each steps | _`__sveltets_2_IsomorphicComponent`_ |
| title | Step title                                     | _`string`_                           |
| icon  | Icon to be rendered.                           | _`__sveltets_2_IsomorphicComponent`_ |

<br/>

### Options

| Props               | Description                                                                                            | Type               | Default |
| ------------------- | ------------------------------------------------------------------------------------------------------ | ------------------ | ------- |
| showTitles          | Display title for each step                                                                            | _`boolean`_        | true    |
| showOneTitle        | Display only the active step title, `showTitles` and `showProgressBar` has to be true for this to work | _`boolean`_        | false   |
| showCheckIcon       | Replaces step numbers with a checkmark icon for completed steps when set to true.                      | _`boolean`_        | false   |
| showStepCount       | Show step numbers to indicate the current position.                                                    | _`boolean`_        | true    |
| clickableNavigation | Allow users to click on step indicators to navigate directly to specific steps                         | _`boolean`_        | false   |
| shouldAnimate       | Enables animated transitions between steps for a smoother visual experience                            | _`boolean`_        | true    |
| showProgressBar     | Display a progress bar with indicators for each step                                                   | _`boolean`_        | true    |
| defaultStep         | Specify the default step to start on                                                                   | _`number (index)`_ | 0       |

<br/>

### customClassnames

| Props                   | Description                                                          | Type        |
| ----------------------- | -------------------------------------------------------------------- | ----------- |
| activeTitleClass        | Set a custom CSS class to the title of the active/completed steps    | _`boolean`_ |
| inactiveTitleClass      | Set a custom CSS class to the title of inactive steps                | _`boolean`_ |
| activeBarItemClass      | Set a custom CSS class to the active/completed step item (dot, line) | _`boolean`_ |
| inactiveBarItemClass    | Set a custom CSS class for the inactive step item (dot, line)        | _`boolean`_ |
| activeStepNumberClass   | Set a custom CSS class to the number of the active/current step      | _`boolean`_ |
| inactiveStepNumberClass | Set a custom CSS class to the number of the inactive step            | _`boolean`_ |

<br/>

# Events

Svelte wizard provides custom events to the parent and child components giving control over navigation and form state management.

### Examples

<b>Parent Component</b>

```
<!-- FormContainer.svelte -->

<WizardComponent
	on:stepChange={(e) => console.log(e)} // Returns the current step index
	on:stepsInitialized={(e) => console.log(e)} //Returns an object
/>
```

<br/>

<b>Child Component</b>

```
<!-- StepOne.svelte -->

<script>
	import { createEventDispatcher } from 'svelte';

	const dispatch = createEventDispatcher();

	const handleNext = () => {
		dispatch('handleNext');
	};
	const handleBack = () => {
		dispatch('handleBack');
	};
	const handleSkipTo = () => {
		dispatch('handleSkipTo', 1);
	};
	const handleSkipToEnd = () => {
		dispatch('handleSkipToEnd');
	};
	const handleSkipToStart = () => {
		dispatch('handleSkipToStart');
	};
	const handleReset = () => {
		dispatch('handleReset');
	};
</script>

<div>
	<p>STEP ONE</p>

	<button on:click={handleNext}> Next</button>
	<button on:click={handleSkipToEnd}> Skip</button>
</div>


```

| Props                | Description                                                                                           | Type                |
| -------------------- | ----------------------------------------------------------------------------------------------------- | ------------------- |
| handleStateUpdate    | Allow dynamic updates to the step state by modifying specific fields or entire parts of the form data | _`EventDispatcher`_ |
| on:handleNext        | Navigate to the next step                                                                             | _`EventDispatcher`_ |
| on:handleBack        | Navigate to the previous step                                                                         | _`EventDispatcher`_ |
| on:handleSkipTo      | Jump directly to a specified step based on the index passed in the event                              | _`EventDispatcher`_ |
| on:handleSkipToEnd   | Jump to the last step                                                                                 | _`EventDispatcher`_ |
| on:handleSkipToStart | Jump to the first step                                                                                | _`EventDispatcher`_ |
| on:handleReset       | Reset the entire form state and navigates back to the starting step                                   | _`EventDispatcher`_ |

<br/>

# State Management

Svelte Wizard offers streamlined state management through `wizardFormState`, which is readily accessible to all child components. State updates are handled efficiently using the `handleStateUpdate` function, enabling seamless data flow and synchronization across steps

# handleStateUpdate

The `handleStateUpdate` function accepts two arguments, with behavior determined by the type of the first argument.
This setup allows for fine-grained control over `wizardFormState`, whether updating individual fields or replacing the entire state object.

- `String` When the first argument is a string, it represents a key in `wizardFormState`. The second argument then becomes the value assigned to this key.

- `Object` When the first argument is an object, the function merges it into `wizardFormState` by default. The second argument is optional and also an object.

### Object props

| Props      | Description                                                                                    | Type      |
| ---------- | ---------------------------------------------------------------------------------------------- | --------- |
| replaceAll | If true, replaces the entire `wizardFormState` with the incoming object, instead of merging it | `boolean` |

### Example

```
<!-- StepOne.svelte -->

<script>
	export let handleStateUpdate;
	export let wizardFormState;

	let firstName = '';
	let lastName = wizardFormState?.lastName; //Persists during navigation

	const handleSubmit = () => {
		handleStateUpdate('firstName', firstName); // String
		handleStateUpdate({ lastName }); // Object
		handleStateUpdate({ firstName, lastName }, { replaceAll: true }); // Object with options
		console.log(wizardFormState); // Contains the updated state
	};
</script>

<div>
	<input bind:value={firstName} />
	<input bind:value={lastName} />
	<buttonon:click={handleSubmit}>Submit</button>
</div>


```

## defaultFormState

Svelte wizard also accepts the `defaultFormState` props used to define default data for `wizardFormState`

```
<WizardComponent defaultFormState={{ firstName: '' }} />
```

<br/>

# Using Custom Classes

Here’s how to apply custom CSS classes within a Svelte component, as opposed to using a global stylesheet.

```
:global(.activeTitleClass) {
	<!-- CSS Properties -->
}
:global(.inactiveTitleClass) {
	<!-- CSS Properties -->
}
:global(.activeBarItemClass) {
	<!-- CSS Properties -->
}
:global(.inactiveBarItemClass) {
	<!-- CSS Properties -->
}
:global(.activeStepNumberClass) {
	<!-- CSS Properties -->
}
:global(.inactiveStepNumberClass) {
	<!-- CSS Properties -->
}
```

## Contribution

Svelte-wizard is an open-source project and contributions are welcome.

<br />

## ⚖️ Licence

MIT (c) <a href="https://sohnya.dev" target="_blank">Sonia Uduma.</a>
