<svelte:head>
	<title>Food</title>
</svelte:head>

<style>
	.success {
		color: green;
	}
	.error {
		color: red;
	}
	form input[type=number], form input[type=text] {
		padding: 5px;
		margin-bottom: 5px;
		@apply bg-gray-200;
	}
	input[type='number'] {
		width: 80px;
	}
	.filter {
		font-size: 150%;
		margin-top: 20px;
		margin-bottom: 10px;
	}
	.wrapper {
		display: grid;
		grid-template-columns: 90% 10%;
		grid-gap: 5px;
		padding-bottom: 40px;
	}
	.box {
		@apply bg-blue-100;
		padding: 10px;
	}
</style>

<script>
	import { onMount } from 'svelte';
	import { validate } from './validate.js';
	import Icon from 'svelte-awesome/components/Icon.svelte'
  import { trash } from 'svelte-awesome/icons';

	let name = ''
	let protein = ''
  let carbs = ''
  let fat = ''
	let food = []
	let mode = 'view'
	let oldName = ''
	let errorMessage = ''
	let successMessage = ''
	let showUpdateForm = false

	let filter = ''
	let filteredFood = []

	function handleUpdate() {
		filteredFood = food
		filter = ''

		let validReturn = {}

		successMessage = ''
		errorMessage = ''

		validReturn = validate({name, protein, carbs, fat})

		if(!validReturn.valid) {
			errorMessage = validReturn.message
			return
		}

		let index = food.findIndex(f => f.name === oldName);

		food[index].name = name
		food[index].protein = protein
		food[index].carbs = carbs
		food[index].fat = fat

		localStorage.setItem('food', JSON.stringify(food))
		successMessage = 'Food was updated'
		showUpdateForm = false
	}

	onMount(async () => {
		food = JSON.parse(localStorage.getItem('food')) || []
		filteredFood = food
	})

	function handleFoodClick(food) {
		filteredFood = food
		filter = ''

		showUpdateForm = true

		successMessage = ''
		errorMessage = ''

		oldName = food.name
		name = food.name
		protein = food.protein
		carbs = food.carbs
		fat = food.fat
		mode = 'update'
	}

	function handleDelete() {
		filteredFood = food
		filter = ''
		successMessage = ''
		errorMessage = ''
		let filtered = food.filter(function(f, index, arr){
			return f.name !== name;
		});

    localStorage.setItem('food', JSON.stringify(filtered))
		food = filtered
		mode = 'view'
		name = ''
		protein = ''
		carbs = ''
		fat = ''
		successMessage = 'Food was deleted'
		showUpdateForm = false
	}

	function handleFoodDelete(foodItem) {
		filteredFood = food
		filter = ''
		successMessage = ''
		errorMessage = ''

		let filtered = food.filter(function(f, index, arr){
			return f.name !== foodItem.name;
		});

    localStorage.setItem('food', JSON.stringify(filtered))
		food = filtered
		filteredFood = filtered
		mode = 'view'
		name = ''
		protein = ''
		carbs = ''
		fat = ''
		successMessage = 'Food was deleted'
		showUpdateForm = false
	}

	function handleFilter() {
		if(filter === '') {
			filteredFood = food
		}

		filteredFood = food.filter(f => f.name.toLowerCase().includes(filter.toLowerCase()));
	}

	function validateProtein() {
		// only 4 chars are allowed - 43.22
		let tmp = String(protein)
		protein = Number(tmp.substring(0,5))
	}
	function validateCarbs() {
		// only 4 chars are allowed - 43.22
		let tmp = String(carbs)
		carbs = Number(tmp.substring(0,5))
	}
	function validateFat() {
		// only 4 chars are allowed - 43.22
		let tmp = String(fat)
		fat = Number(tmp.substring(0,5))
	}
</script>

<h2 class="text-xl">Update Food</h2>

{#if successMessage !== ''}
	<p class='success'>{successMessage}</p>
{/if}

{#if errorMessage !== ''}
	<p class='error'>{errorMessage}</p>
{/if}

{#if food.length === 0}
	<p>You have no food.<p>
	<p> <a href="/food/manage-food/add-food">Add some food first</a>.</p>
{:else}
	{#if showUpdateForm}
		<form>
			<div><input class="" type="text" bind:value={name} placeholder="Name" maxlength="20" size="20"/></div>
			<div><input type="number" bind:value={protein} placeholder="Protein" on:keyup={validateProtein} min="0" max="999"/></div>
			<div><input type="number" bind:value={carbs} placeholder="Carbs" on:keyup={validateCarbs} min="0" max="999"/></div>
			<div><input type="number"  bind:value={fat} placeholder="Fat" on:keyup={validateFat} min="0" max="999"/></div>

			<div class="mt-3">
				<button class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-1 px-3" on:click={handleUpdate}>Update</button>
				<button class="text-red-400 py-1 px-1" on:click={handleDelete}><Icon data={trash}/></button>
			</div>
		</form>
	{/if}

	{#if !showUpdateForm}
		{#if food.length >= 10}
			<input bind:value={filter} class='filter bg-gray-200 w-24 px-2' type='text' placeholder='Search' on:input={handleFilter} maxlength="5" size="3" />
		{/if}
		<div class="wrapper" style="margin-top: {food.length >=10 ? 0 : 20}px;">
			{#each filteredFood as { id, name }, i}
				<button class='box' on:click={() => handleFoodClick(food[i])}>{name}</button><button class="text-red-400" on:click|preventDefault={() => handleFoodDelete(food[i])}><Icon data={trash}/></button>
			{/each}
		</div>
	{/if}
{/if}
