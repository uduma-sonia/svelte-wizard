<script lang="ts">
	import { createEventDispatcher } from 'svelte';
	import { fly, fade } from 'svelte/transition';
	import Check from './Check.svelte';

	type StepperProps = {
		stepsList: Array<{
			step: __sveltets_2_IsomorphicComponent;
			icon?: __sveltets_2_IsomorphicComponent;
			title?: string;
		}>;
		defaultFormState?: Record<string, any>;
		customClassnames?: {
			activeTitleClass?: string;
			inactiveTitleClass?: string;
			activeBarItemClass?: string;
			inactiveBarItemClass?: string;
			activeStepNumberClass?: string;
			inactiveStepNumberClass?: string;
		};
		options?: {
			showTitles?: boolean;
			showStepCount?: boolean;
			showCheckIcon?: boolean;
			shouldAnimate?: boolean;
			showProgressBar?: boolean;
			clickableNavigation?: boolean;
			showOneTitle?: boolean;
			defaultStep?: number;
		};
	};

	export let stepsList: StepperProps['stepsList'] = [];
	export let defaultFormState: StepperProps['defaultFormState'] = {};
	export let options: StepperProps['options'] = {};
	export let customClassnames: StepperProps['customClassnames'] = {};

	const dispatch = createEventDispatcher<{
		stepChange: { activeIndex: number };
		stepsInitialized: number;
	}>();

	let defaultOptions = {
		showTitles: true,
		showCheckIcon: false,
		showStepCount: true,
		clickableNavigation: false,
		shouldAnimate: true,
		showProgressBar: true,
		defaultStep: 0,
		showOneTitle: false
	};

	let defaultClassnames = {
		activeTitleClass: 'active_title_color',
		inactiveTitleClass: 'inactive_title_color',
		activeBarItemClass: 'active_bg_color',
		inactiveBarItemClass: 'inactive_bg_color',
		activeStepNumberClass: 'active_step_number_color',
		inactiveStepNumberClass: 'inactive_step_number_color'
	};

	$: wizardFormState = defaultFormState;
	let animateFromRight = true;

	$: mergedOptions = { ...defaultOptions, ...options };
	$: mergedClassnames = { ...defaultClassnames, ...customClassnames };

	$: currentStep = mergedOptions?.defaultStep;
	$: stepLength = stepsList.length;
	$: fadeOutConfig = {
		delay: mergedOptions?.shouldAnimate ? 0 : 0,
		duration: mergedOptions?.shouldAnimate ? 0 : 0
	};
	$: flyInConfig = {
		x: mergedOptions?.shouldAnimate ? (animateFromRight ? -70 : 70) : '',
		duration: mergedOptions?.shouldAnimate ? 600 : 0,
		delay: mergedOptions?.shouldAnimate ? 100 : 0
	};
	$: {
		if (currentStep === 0 || mergedOptions?.defaultStep) {
			dispatch('stepsInitialized', currentStep);
		}
	}

	const handleNext = () => {
		animateFromRight = false;
		if (currentStep < stepsList.length - 1) {
			currentStep += 1;
			dispatch('stepChange', { activeIndex: currentStep });
		}
	};
	const handleBack = () => {
		animateFromRight = true;
		if (currentStep > 0) {
			currentStep -= 1;
			dispatch('stepChange', { activeIndex: currentStep });
		}
	};
	const handleSkipTo = (event: CustomEvent<number> | number) => {
		if (event instanceof CustomEvent) {
			currentStep = event.detail;
		} else {
			currentStep = event;
		}
	};
	const handleSkipToEnd = () => {
		handleSkipTo(stepsList.length - 1);
	};
	const handleSkipToStart = () => {
		handleSkipTo(0);
	};
	const handleReset = () => {
		handleSkipToStart();
		wizardFormState = {};
	};
	const handleStateUpdate = (
		field: string | Record<string, any>,
		value?: any | Record<string, any>
	) => {
		if (typeof field === 'string') {
			wizardFormState = { ...wizardFormState, [field]: value };
		} else if (typeof field === 'object') {
			if (value?.replaceAll) {
				wizardFormState = field;
			} else {
				wizardFormState = { ...wizardFormState, ...field };
			}
		}
	};
	const handleNavigation = (index: number) => {
		if (mergedOptions?.clickableNavigation) handleSkipTo(index);
	};
</script>

<div>
	{#if mergedOptions?.showProgressBar}
		{#if mergedOptions?.showOneTitle && mergedOptions?.showTitles}
			{#each stepsList as { title }, index}
				{#if currentStep === index}
					<p class="progress_single_title {mergedClassnames?.activeTitleClass}">
						{title}
					</p>
				{/if}
			{/each}
		{:else}
			<div class="progress_bar_wrapper">
				{#each stepsList as _item, index}
					{@const titleColor =
						index <= currentStep
							? mergedClassnames?.activeTitleClass
							: mergedClassnames?.inactiveTitleClass}

					{@const bgColor =
						index <= currentStep
							? mergedClassnames?.activeBarItemClass
							: mergedClassnames?.inactiveBarItemClass}
					{@const lineBgColor =
						index < currentStep
							? mergedClassnames?.activeBarItemClass
							: mergedClassnames?.inactiveBarItemClass}
					{@const stepNumberColor =
						index <= currentStep
							? mergedClassnames?.activeStepNumberClass
							: mergedClassnames?.inactiveStepNumberClass}
					{@const _cursor = mergedOptions?.clickableNavigation ? 'pointer' : 'inherit'}

					<button
						class="progress_bar_item_wrapper {bgColor}"
						style="cursor: {_cursor}"
						on:click={() => handleNavigation(index)}
					>
						{#if _item.icon}
							<svelte:component this={_item.icon} />
						{:else if mergedOptions?.showStepCount}
							<div style="display: flex;	align-items: center; justify-content: center;">
								{#if currentStep > index && mergedOptions?.showCheckIcon}
									<Check />
								{:else}
									<span class={stepNumberColor}>{index + 1}</span>
								{/if}
							</div>
						{/if}
						{#if mergedOptions?.showTitles}
							<p
								class="step_title {titleColor}"
								class:left_0={index === 0}
								class:right_0={index + 1 === stepLength}
							>
								{_item.title}
							</p>
						{/if}
					</button>

					{#if index < stepLength - 1}
						<div class="progress_separator_line {lineBgColor}"></div>
					{/if}
				{/each}
			</div>
		{/if}
	{/if}

	<div class="overflow_hidden">
		{#if stepLength}
			{#each stepsList as { step }, index (index)}
				{#if currentStep === index}
					<div in:fly={flyInConfig} out:fade={fadeOutConfig}>
						<svelte:component
							this={step}
							{wizardFormState}
							{handleStateUpdate}
							on:handleNext={handleNext}
							on:handleBack={handleBack}
							on:handleSkipTo={handleSkipTo}
							on:handleSkipToEnd={handleSkipToEnd}
							on:handleSkipToStart={handleSkipToStart}
							on:handleReset={handleReset}
						/>
					</div>
				{/if}
			{/each}
		{/if}
	</div>
</div>

<style>
	* {
		margin: 0;
		padding: 0;
		box-sizing: border-box;
	}
	button {
		outline: none;
		border: none;
	}
	.progress_bar_wrapper {
		position: relative;
		display: flex;
		align-items: center;
		gap: 8px;
		justify-content: space-between;
		padding-top: 32px;
		padding-bottom: 32px;
	}
	.progress_bar_item_wrapper {
		position: relative;
		z-index: 99;
		width: 30px;
		height: 30px;
		border-radius: 999px;
		display: flex;
		align-items: center;
		justify-content: center;
	}

	.progress_single_title {
		text-align: center;
		padding: 20px 10px;
		font-size: 20px;
	}

	.progress_separator_line {
		flex: 1 1 0;
		height: 4px;
	}
	.step_title {
		position: absolute;
		top: 100%;
		font-size: 16px;
		width: max-content;
		padding-top: 6px;
	}
	.left_0 {
		left: 0px;
	}
	.right_0 {
		right: 0px;
	}
	.active_bg_color {
		background-color: #00a15d;
	}
	.inactive_bg_color {
		background-color: #d7d0d0;
	}
	.active_title_color {
		color: #000000;
	}
	.inactive_title_color {
		color: #888888;
	}
	.active_step_number_color {
		color: #ffffff;
	}
	.inactive_step_number_color {
		color: #000000;
	}
	.overflow_hidden {
		overflow-x: hidden;
	}
</style>
