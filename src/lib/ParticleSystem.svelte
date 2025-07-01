<script lang="ts">
  import { onMount } from 'svelte';
  
  interface Particle {
    id: number;
    x: number;
    y: number;
    size: number;
    opacity: number;
    duration: number;
    delay: number;
  }
  
  let particles: Particle[] = [];
  let canvasVisible = true;
  
  onMount(() => {
    // Respect reduced motion preference
    const prefersReducedMotion = window.matchMedia('(prefers-reduced-motion: reduce)').matches;
    if (prefersReducedMotion) {
      canvasVisible = false;
      return;
    }
    
    // Create visible particles
    for (let i = 0; i < 15; i++) {
      particles.push({
        id: i,
        x: Math.random() * 100, // Percentage
        y: Math.random() * 100, // Percentage
        size: Math.random() * 3 + 1, // 1-4px
        opacity: Math.random() * 0.4 + 0.2, // 0.2-0.6
        duration: Math.random() * 20 + 15, // 15-35 seconds
        delay: Math.random() * 5 // 0-5 second delay
      });
    }
    
    // Trigger reactive update
    particles = [...particles];
  });
</script>

{#if canvasVisible && particles.length > 0}
  <div class="fixed inset-0 pointer-events-none z-10 overflow-hidden">
    {#each particles as particle (particle.id)}
      <div
        class="absolute rounded-full bg-white animate-float particle-glow"
        style="
          left: {particle.x}%; 
          top: {particle.y}%; 
          width: {particle.size}px; 
          height: {particle.size}px; 
          opacity: {particle.opacity};
          animation-duration: {particle.duration}s;
          animation-delay: {particle.delay}s;
          box-shadow: 0 0 {particle.size * 2}px rgba(255, 255, 255, {particle.opacity * 0.5});
        "
      ></div>
    {/each}
  </div>
{/if}

<style>
  @keyframes float {
    0%, 100% {
      transform: translateY(0px) translateX(0px);
    }
    25% {
      transform: translateY(-20px) translateX(10px);
    }
    50% {
      transform: translateY(-10px) translateX(-5px);
    }
    75% {
      transform: translateY(-30px) translateX(-10px);
    }
  }
  
  .animate-float {
    animation: float linear infinite;
  }
</style> 