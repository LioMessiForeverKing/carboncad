<script>
	import { onMount } from 'svelte';
	import gsap from 'gsap';

	const grid = [14, 30]; // Grid dimensions

	onMount(() => {
		const prefersReducedMotion = window.matchMedia('(prefers-reduced-motion: reduce)').matches;

		if (prefersReducedMotion) {
			gsap.set('#leaf-grid', { opacity: 1 });
			gsap.set('.leaf-grid-item', {
				opacity: 0.2,
				scale: 1
			});
			return;
		}

		gsap.set('.leaf-grid-item', {
			opacity: 0,
			transformOrigin: 'center',
			color: '#34D399' // Green tone for sustainability
		});

		gsap.set('#leaf-grid', { opacity: 1 });

		const tl = gsap.timeline();

		// Entrance Animation - Flowing effect
		tl.to('.leaf-grid-item', {
			keyframes: [
				{
					opacity: 0,
					duration: 0
				},
				{
					opacity: 0.4,
					y: '-=20', // Simulates wind or growth motion
					color: '#60A5FA', // Blue tone
					scale: 1.5,
					duration: 0.6,
					stagger: {
						amount: 2,
						grid: grid,
						from: 'edges'
					}
				},
				{
					opacity: 0.2,
					y: '+=20', // Return motion
					color: '#34D399',
					scale: 1,
					delay: -2,
					duration: 0.6,
					stagger: {
						amount: 3,
						grid: grid,
						from: 'edges'
					}
				}
			]
		});

		// Loop Animation - Ripple effect
		tl.to('.leaf-grid-item', {
			delay: 10,
			repeat: -1,
			repeatDelay: 10,
			keyframes: [
				{
					opacity: 0.4,
					y: '-=10',
					color: '#60A5FA',
					scale: 1.3,
					duration: 0.6,
					stagger: {
						amount: 2,
						grid: grid,
						from: 'random'
					}
				},
				{
					opacity: 0.2,
					y: '+=10',
					color: '#34D399',
					scale: 1,
					delay: -2,
					duration: 0.6,
					stagger: {
						amount: 3,
						grid: grid,
						from: 'random'
					}
				}
			]
		});
	});
</script>

<svg
	xmlns="http://www.w3.org/2000/svg"
	fill="none"
	viewBox="0 0 935 425"
	class="absolute -left-2 -top-14 -z-10"
	id="leaf-grid"
	opacity={0}
	style="mask-image: linear-gradient(black, transparent);"
>
	<g class="leaf-grid-group">
		{#each Array(grid[0]) as _, i}
			{#each Array(grid[1]) as __, j}
				<path
					fill="currentColor"
					opacity=".2"
					class="leaf-grid-item"
					d="
						M{j * 32 + 5},{i * 32 + 10}
						c3,-5 7,-5 10,0
						c3,5 0,10 -5,10
						c-5,0 -8,-5 -5,-10
						Z" 
				/>
			{/each}
		{/each}
	</g>
</svg>
