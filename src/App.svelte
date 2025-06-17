<script>
	import Board from "./Board.svelte";
	import { onMount } from "svelte";

	let selectedColor = "#ff0000";
	let darkMode = false;
	let loaded = false;

	let tileTexts = Array(25).fill("");

	function getCookie(name) {
		const match = document.cookie.match(
			new RegExp("(^| )" + name + "=([^;]+)"),
		);
		return match ? decodeURIComponent(match[2]) : null;
	}

	function savePreferences() {
		if (!loaded) return;

		document.cookie = `selectedColor=${encodeURIComponent(selectedColor)}; path=/; max-age=31536000`; // 1 year
		document.cookie = `darkMode=${darkMode ? 1 : 0}; path=/; max-age=31536000`;
	}

	onMount(async () => {
		// Load preferences from cookies
		const savedColor = getCookie("selectedColor");
		const savedDark = getCookie("darkMode");

		if (savedColor) {
			selectedColor = savedColor;
		}

		if (savedDark !== null) {
			darkMode = savedDark === "1";
		}

		// Load texts from file
		try {
			const res = await fetch("/texts.json");
			if (!res.ok) throw new Error("Failed to fetch texts.json");
			let data = await res.json();

			if (data.length < 25) {
				tileTexts = [...data, ...Array(25 - data.length).fill("")];
			} else if (data.length > 25) {
				tileTexts = [...data]
					.sort(() => Math.random() - 0.5)
					.slice(0, 25);
			} else {
				tileTexts = data;
			}
		} catch (err) {
			console.error("Error loading texts:", err);
			tileTexts = Array(25).fill("");
		}

		loaded = true;
	});

	$: selectedColor, savePreferences();
	$: darkMode, savePreferences();
</script>

<div class="app-container" class:dark={darkMode}>
	<div class="board-wrapper">
		<Board activeColor={selectedColor} texts={tileTexts} />
	</div>

	<div class="controls">
		<div class="color-picker">
			<label>
				Pick active color:
				<input type="color" bind:value={selectedColor} />
			</label>
		</div>

		<div>
			<label class="switch">
				<input type="checkbox" bind:checked={darkMode} />
				<span class="slider"></span>
			</label>
			<span>Dark Mode</span>
		</div>
	</div>
</div>

<style>
	.app-container {
		display: flex;
		height: 100vh;
		margin: 0;
		background-color: var(--bg-color);
		color: var(--text-color);
		transition:
			background-color 0.3s,
			color 0.3s;
	}

	.board-wrapper {
		flex: 1;
		display: flex;
		align-items: center;
		justify-content: center;
	}

	.controls {
		width: 200px;
		padding: 1rem;
		display: flex;
		flex-direction: column;
		gap: 1.5rem;
		border-left: 1px solid var(--tile-border-color);
		box-sizing: border-box;
	}

	.color-picker label {
		font-size: 1rem;
		display: flex;
		flex-direction: column;
		gap: 0.5rem;
	}

	label.switch {
		position: relative;
		display: inline-block;
		width: 50px;
		height: 24px;
	}

	label.switch input {
		opacity: 0;
		width: 0;
		height: 0;
	}

	.slider {
		position: absolute;
		cursor: pointer;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		background-color: #ccc;
		transition: 0.4s;
		border-radius: 24px;
	}

	.slider:before {
		position: absolute;
		content: "";
		height: 18px;
		width: 18px;
		left: 3px;
		bottom: 3px;
		background-color: white;
		transition: 0.4s;
		border-radius: 50%;
	}

	input:checked + .slider {
		background-color: #2196f3;
	}

	input:checked + .slider:before {
		transform: translateX(26px);
	}

	:root {
		--bg-color: #f0f0f0;
		--text-color: #000;
		--tile-border-color: #000;
	}

	.dark {
		--bg-color: #121212;
		--text-color: #e0e0e0;
		--tile-border-color: #bbb;
	}
</style>
