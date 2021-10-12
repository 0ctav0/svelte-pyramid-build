<script lang="ts">
	export let name: string;
</script>

<main id="pyramid-build">
	<header class="top-menu">
		<span class="lang">English</span>
	</header>
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

.lang {
	margin-left: auto;
	color: var(--weak-text-color);
	cursor: pointer;
	user-select: none;
}
</style>