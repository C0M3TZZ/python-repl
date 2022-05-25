<script lang="ts">
	// @ts-ignore
	import Monaco from '$lib/monaco.svelte';
	import { onMount } from 'svelte';
	let code: string = `def area():\n\theight = int(input("Enter height: "))\n\twidth = int(input("Enter width: "))\n\treturn height * width\n\nprint(area())\n\n\n# Made by t0ng.dev`;
	let excute: string = code;
	let log: HTMLParagraphElement;
	let output_box: HTMLDivElement;
	let btn: HTMLDivElement;
	let addLog: any = (data: string) => {
		let dateNow: Date = new Date();
		log.innerHTML += `[${dateNow.getHours()}:${dateNow.getMinutes()}:${dateNow.getSeconds()}] >>> ${data} <br>`;
		output_box.scrollTop = log.scrollHeight;
	};
	const pyodideConfig = {
		stdout: (data: string) => {
			addLog(data);
		},
		stdin: () => {
			let x = prompt('Enter your input: ');
			return x;
		}
	};

	onMount(async () => {
		// @ts-ignore
		let pyodide = await loadPyodide(pyodideConfig);
		btn.addEventListener('click', () => {
			excute = code;
			try {
				pyodide.runPython(excute);
			} catch (error: any) {
				let error_message: any = error.message.toString().split('\n');
				addLog(error_message[error_message.length - 2]);
			}
		});
	});
</script>

<svelte:head>
	{@html `<script src="https://cdn.jsdelivr.net/pyodide/v0.20.0/full/pyodide.js">{}</script>`}
    <title>Python REPL</title>
    <link rel="icon" href="/favicon.ico" />
</svelte:head>

<div class="container">
	<div class="card">
		<div class="editor">
			<Monaco bind:code />
		</div>
		<div class="btn_bar">
			<div
				class="btn"
				on:click={() => {
					log.innerHTML = '';
				}}
			>
				Clear Log
			</div>
			<div class="btn" bind:this={btn}>Excute Python</div>
		</div>
		<div bind:this={output_box} class="output_box">
			<p bind:this={log} />
		</div>
	</div>
</div>

<style>
	:global(body) {
		margin: 0;
		padding: 0;
		background-color: black;
	}
	.container {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		height: 100vh;
		width: 100vw;
	}
	.card {
		height: 50%;
		width: 50%;
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		border-radius: 2.5vh;
		overflow: hidden;
		background-color: #1a1a1a;
	}
	.editor {
		height: 50%;
		width: 100%;
	}
	.btn_bar {
		display: flex;
		flex-direction: row;
		justify-content: end;
		align-items: center;
		height: 10%;
		width: 100%;
		background-color: white;
	}
	.btn {
		height: 100%;
		width: 25%;
		background-color: #3498db;
		border-radius: 2.5vh;
		border: none;
		color: white;
		font-size: 1.5vh;
		font-weight: bold;
		cursor: pointer;
		outline: none;
		user-select: none;
		display: flex;
		justify-content: center;
		align-items: center;
		text-align: center;
		margin: 1vh;
		transition: all 0.2s ease-in-out;
	}
	.btn:hover {
		background-color: #2d82ba;
	}
	.output_box {
		height: 40%;
		width: 100%;
		background-color: white;
		overflow-y: scroll;
		padding-top: 1vh;
		padding-bottom: 1vh;
		padding-left: 1vh;
		font-family: monospace;
	}
</style>
