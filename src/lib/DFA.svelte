<script lang="ts">
	let initialState: string = '';
	let finalStates: string = '';
	let stateName: string = '';
	let transitions: string = '';
	let inputString: string = '';

	let definition: {
		start: string;
		finals: string[];
		states: Record<string, Record<string, string>>;
	} = {
		start: '',
		finals: [],
		states: {}
	};

	function addState(): void {
		if (stateName) {
			definition.states[stateName] = {}; // Placeholder for transitions
			stateName = '';
			transitions = '';
		}
	}
	function printAllStates_infile(): void {
		let output = '';
		for (const state in definition.states) {
			output += `State: ${state}\n`;
			for (const char in definition.states[state]) {
				output += ` Transition: ${char} -> ${definition.states[state][char]}\n`;
			}
		}
		output += '\nFinal States:\n';
		definition.finals.forEach((state) => {
			output += `State: ${state}\n`;
		});
		output += '\nCurrent State: ' + (initialState || 'None');

		// Create a Blob object with the content
		const blob = new Blob([output], { type: 'text/plain' });

		// Create a temporary link element
		const link = document.createElement('a');
		link.href = URL.createObjectURL(blob);
		link.download = 'states_and_transitions.txt';

		// Append the link to the body
		document.body.appendChild(link);

		// Programmatically click the link to start the download
		link.click();

		// Remove the link from the body
		document.body.removeChild(link);
	}

	function addTransition(): void {
		const transitionsArray = transitions.split(',').map((t) => t.trim());
		transitionsArray.forEach((t) => {
			const [state, char, nextState] = t.split(':');
			if (!definition.states[state]) {
				definition.states[state] = {};
			}
			definition.states[state][char] = nextState;
		});
		stateName = '';
		transitions = '';
	}

	function clearStates(): void {
		definition = {
			start: '',
			finals: [],
			states: {}
		};
		initialState = '';
		finalStates = '';
		stateName = '';
		transitions = '';
		inputString = '';
	}

	function checkString(): void {
		let currentState: string | null = definition.start;
		console.log(definition);
		for (const char of inputString) {
			if (
				currentState &&
				definition.states[currentState] &&
				definition.states[currentState][char]
			) {
				currentState = definition.states[currentState][char];
			} else {
				// If there's no transition for the current character, the string is rejected.
				currentState = null;
				break;
			}
		}

		// Check if the current state is a final state.
		const result = currentState && definition.finals.includes(currentState);
		alert(`The string "${inputString}" is ${result ? 'accepted' : 'rejected'}.`);
	}
	function printAllStates(): void {
		let output = '';
		for (const state in definition.states) {
			output += `State: ${state}\n`;
			for (const char in definition.states[state]) {
				output += ` Transition: ${char} -> ${definition.states[state][char]}\n`;
			}
		}
		output += '\nFinal States:\n';
		definition.finals.forEach((state) => {
			output += `State: ${state}\n`;
		});
		output += '\nCurrent State: ' + (initialState || 'None');
		alert(output);
	}
	function processFinalStates(): void {
		definition.finals = finalStates.split(',').map((state) => state.trim());
		alert(`Final states processed: ${definition.finals.join(', ')}`);
	}
	function processInitialState(): void {
		definition.start = initialState;
		alert(`Initial state processed: ${definition.start}`);
	}
</script>

<div class="flex justify-between items-center">
	<div class="flex space-x-2 flex-col">
		<label class="label">
			Initial State:
			<input bind:value={initialState} type="text" class="input" />
		</label>
		<label class="label">
			Final States (comma-separated):
			<input bind:value={finalStates} type="text" class="input" />
		</label>
		<label class="label">
			State Name:
			<input bind:value={stateName} type="text" class="input" />
		</label>
		<label class="label">
			Transitions (format: state:character:state, e.g., S0:a:S1):
			<input bind:value={transitions} class="input" type="text" />
		</label>
	</div>
	<div class="flex space-x-2 flex-col">
		<button on:click={processFinalStates} class="btn variant-filled mb-5"
			>Process Final States</button
		>
		<button on:click={processInitialState} class="btn variant-filled mb-5"
			>Process Initial State</button
		>
		<button on:click={addState} class="btn variant-filled mb-5">Add State</button>
		<button on:click={addTransition} class="btn variant-filled mb-5">Add Transitions</button>
		<button on:click={clearStates} class="btn variant-filled mb-5">Clear States</button>
		<button on:click={printAllStates} class="btn variant-filled mb-5">Print All States</button>
		<button on:click={printAllStates_infile} class="btn variant-filled mb-5"
			>Print All States in file</button
		>
	</div>
</div>

<div>
	<label class="label">
		Input String:
		<input bind:value={inputString} type="text" class="input" />
	</label>
	<button on:click={checkString} class="btn variant-filled">Check String</button>
</div>
