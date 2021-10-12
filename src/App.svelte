<script lang="ts">

	type GameState = "NEW" | "SHOW" | "RUN" | "PAUSE" | "FINISH" | "GAME_OVER";

	const MAX_VISIBLE_WORDS_NUMBER = 7;
	const SHOWING_WORD_DELAY = 0;
	
	let gameState: GameState = "NEW";
	
	let level = 3;
	$: wordsNumber = 3 + level * 2

	let allWords: string[] = [];

	// Initial words in right order
	$: words = allWords.sort(() => Math.random() < 0.5 ? 1 : -1).slice(0, wordsNumber);

	let showingWordIndex = -1;

	let showingTimer;

	// Words that you need to pick in right order
	let runningWords: string[] = [];

	$: wordsProgress = () => {
		switch(gameState) {
			case "NEW": return 0;
			case "SHOW": return showingWordIndex + 1;
			default: return words.length - runningWords.length;
		}
	}

	

	///// Functions

	const init = () => {
		showingWordIndex = -1;
		runningWords = [];
		
	}

	const loadResources = () => {
		fetch("words_en.json").then(res => {
			res.json().then((allWordsIn: string[][]) => {
				allWords = allWordsIn.flat()
				console.log(words);
			});
		});
	}

	const nextShowingWord = () => {
		showingWordIndex++;
		if (showingWordIndex < words.length - 1) { // if not last showing word then show next
			showingTimer = setTimeout(nextShowingWord, SHOWING_WORD_DELAY);
		} else { // else start actaully a game
			gameState = "RUN";
			messUpWords();
		}
	}

	const messUpWords = () => {
		runningWords = words.sort(() => Math.random() < 0.5 ? 1 : -1)
	}

	const pickWord = (index: number) => {
		runningWords = runningWords.filter((_, i) => i !== index);
		if (runningWords.length === 0) {
			gameState = "FINISH";
		}
	}

	const startGame = () => {
		gameState = "SHOW";
		nextShowingWord();
	}

	const skipShowingWord = () => {
		clearTimeout(showingTimer);
		nextShowingWord();
	}

	const continueGame = () => {
		gameState = "RUN";
	}

	const playAgain = () => {
		level++;
		init();
		startGame();
	}

	//// Entry point

	
	loadResources();
	init();

</script>

<main id="pyramid-build">
	<header class="top-menu">
		<span class="words-number">Words {wordsProgress()} / {wordsNumber}</span>
		<span class="lang">English</span>
	</header>
	{#if gameState === "RUN"}
	<div class="content">
		{#each runningWords.slice(0, MAX_VISIBLE_WORDS_NUMBER) as word, index (word)}
			<div class="word" on:click={() => pickWord(index)}>{word}</div>
		{/each}
	</div>
	{:else}
	<div class="centered-content">
		{#if gameState === "NEW"}
			<div class="word" on:click={startGame}>START</div>
		{:else if gameState === "SHOW"}
			<div class="word" on:click={skipShowingWord}>{words[showingWordIndex]}</div>
		{:else if gameState === "PAUSE"}
			<div class="word" on:click={continueGame}>CONTINUE</div>
		{:else if gameState === "FINISH"}
			<h2>Congrats you won!!!</h2>
			<div class="word" on:click={playAgain}>NEXT LEVEL</div>
		{/if}
	</div>
	{/if}
</main>

<style>
#pyramid-build {
	width: 100vw;
	height: 100vh;
	max-width: 500px;
	max-height: 900px;
	display: flex;
	flex-direction: column;
	background-color: var(--main-color);
}

.top-menu {
	height: 40px;
	background-color: var(--main-color);
	filter: brightness(0.9);
	display: flex;
	padding: .5rem;
}

.words-number {
	color: var(--weak-text-color);
	user-select: none;
}

.lang {
	margin-left: auto;
	color: var(--weak-text-color);
	cursor: pointer;
	user-select: none;
}

.content {
	flex-grow: 1;
	display: flex;
	flex-direction: column;
	gap: 1rem;
	padding: 1rem;
}

.centered-content {
	flex-grow: 1;
	display: flex;
	flex-direction: column;
	align-items: center;
	justify-content: center;
	gap: 1rem;
}

.word {
	padding: .5rem;
	background-color: var(--text-color);
	color: var(--main-color);
	border-radius: 8px;
	font-size: 2rem;
	word-break: break-all;
	cursor: pointer;
	user-select: none;
}
</style>