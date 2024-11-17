<script lang="ts">
	import { onMount } from 'svelte';
	import { PrismicImage, PrismicLink, PrismicRichText, PrismicText } from '@prismicio/svelte';

	import Bounded from '$lib/components/Bounded.svelte';
	import ButtonLink from '$lib/components/ButtonLink.svelte';
	import TriangleGrid from '$lib/components/Triangle.svelte';

	/** @type {import("@prismicio/client").Content.HeroSlice} */
	export let slice;

	import gsapModule from 'gsap';
	let gsap: typeof gsapModule;
	let ScrollTrigger: typeof import('gsap/ScrollTrigger').ScrollTrigger;

	onMount(async () => {
		const prefersReducedMotion = window.matchMedia('(prefers-reduced-motion: reduce)').matches;

		if (typeof window !== 'undefined') {
			const { default: gsapModule } = await import('gsap');
			const { ScrollTrigger: scrollTriggerModule } = await import('gsap/ScrollTrigger');
			gsap = gsapModule;
			ScrollTrigger = scrollTriggerModule;

			gsap.registerPlugin(ScrollTrigger);

			if (prefersReducedMotion) {
				gsap.set('.hero__heading, .hero__body, .hero__button, .hero__image, .hero__glow', {
					opacity: 1
				});
				return;
			}

			// Elements
			const heroHeading = document.querySelector('.hero__heading') as HTMLElement | null;
			const heroBody = document.querySelector('.hero__body') as HTMLElement | null;
			const heroButton = document.querySelector('.hero__button') as HTMLElement | null;
			const heroImage = document.querySelector('.hero__image') as HTMLElement | null;

			// Mousemove Animation
			const animateElement = (element: HTMLElement | null, xFactor: number, yFactor: number, scale: number = 1) => {
				element?.addEventListener('mousemove', (event: MouseEvent) => {
					const { clientX, clientY } = event;
					const { innerWidth, innerHeight } = window;
					const rotateX = (clientY / innerHeight - 0.5) * yFactor;
					const rotateY = (clientX / innerWidth - 0.5) * xFactor;

					gsap.to(element, {
						rotateX,
						rotateY,
						scale,
						transformPerspective: 800,
						duration: 0.5,
						ease: 'power3.out',
					});
				});
				element?.addEventListener('mouseleave', () => {
					gsap.to(element, {
						rotateX: 0,
						rotateY: 0,
						scale: 1,
						duration: 0.5,
						ease: 'power3.out',
					});
				});
			};

			animateElement(heroHeading, 12, 12, 1.05); // Enhanced 3D effect for climate-related title
			animateElement(heroBody, 10, 10, 1.03);    // Subtle movement for description text
			animateElement(heroButton, 8, 8);          // Interactive hover effects for buttons
			animateElement(heroImage, 14, 14);         // Subtle movements for imagery

			// Scroll-triggered animations
			const timeline = gsap.timeline({
				scrollTrigger: {
					trigger: '.hero-section',
					start: 'top 85%',
					end: 'bottom 15%',
					toggleActions: 'play none none reverse',
				}
			});

			timeline.fromTo('.hero__heading', { y: 60, opacity: 0 }, { y: 0, opacity: 1, duration: 1.2, ease: 'power2.out' });
			timeline.fromTo('.hero__body', { y: 40, opacity: 0 }, { y: 0, opacity: 1, duration: 0.9, stagger: 0.25 }, '-=0.6');
			timeline.fromTo('.hero__button', { scale: 0.8, opacity: 0 }, { scale: 1, opacity: 1, duration: 1.1, ease: 'power4.out' }, '-=0.4');
			timeline.fromTo('.hero__image', { y: 100, opacity: 0 }, { y: 0, opacity: 1, duration: 1.4, ease: 'power3.out' }, '+=0.2');
			timeline.fromTo('.hero__glow', { scale: 0.6, opacity: 0 }, { scale: 1, opacity: 1, duration: 2.2, ease: 'elastic.out(1, 0.8)' }, '-=0.8');
		}
	});
</script>

<style>
	.hero__heading {
		color: #2f855a;
		text-shadow: 0px 4px 20px rgba(0, 200, 100, 0.5);
		letter-spacing: 1px;
		font-family: 'Roboto', sans-serif;
	}

	.hero__body {
		color: #2b6cb0;
		text-shadow: 0px 2px 8px rgba(100, 150, 250, 0.3);
		font-size: 1.125rem;
		line-height: 1.75rem;
	}

	.hero__image {
		background: linear-gradient(to bottom right, rgba(0, 128, 96, 0.2), rgba(50, 150, 255, 0.3));
		border-radius: 12px;
		overflow: hidden;
		box-shadow: 0px 12px 40px rgba(0, 128, 128, 0.5);
	}

	.hero__glow {
		position: absolute;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		border-radius: 12px;
		background: radial-gradient(circle, rgba(40, 200, 120, 0.3), rgba(50, 200, 250, 0.1));
		mix-blend-mode: overlay;
		opacity: 0;
		filter: blur(80px);
	}

	.hero__overlay {
		background: rgba(255, 255, 255, 0.15);
		transition: all 0.5s ease;
		border-radius: 12px;
	}
</style>

<Bounded data-slice-type={slice.slice_type} data-slice-variation={slice.variation} class="hero-section">
	<div class="relative text-center">
		<TriangleGrid />

		{#if slice.primary.heading}
			<h1 class="hero__heading mx-auto max-w-3xl text-8xl font-semibold opacity-0">
				<PrismicText field={slice.primary.heading} />
			</h1>
		{/if}
		{#if slice.primary.body}
			<p class="hero__body mx-auto mt-6 max-w-lg opacity-0">
				<PrismicText field={slice.primary.body} />
			</p>
		{/if}
		{#if slice.primary.button_link}
			<ButtonLink class="hero__button mt-8 opacity-0" field={slice.primary.button_link}>
				{slice.primary.button_label}
			</ButtonLink>
		{/if}
		{#if slice.primary.image}
			<div class="hero__image mt-16 w-fit opacity-0">
				<div class="hero__overlay"></div>
				<div class="hero__glow"></div>
				<PrismicImage class="rounded-lg" field={slice.primary.image} />
			</div>
		{/if}
	</div>
</Bounded>
