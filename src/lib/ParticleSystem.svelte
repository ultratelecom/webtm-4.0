<script lang="ts">
  import { onMount } from 'svelte';
  
  interface Star {
    id: number;
    x: number;
    y: number;
    size: number;
    opacity: number;
    baseOpacity: number;
    twinkleDuration: number;
    twinkleDelay: number;
    driftX: number;
    driftY: number;
    driftDuration: number;
    color: string;
    type: 'tiny' | 'small' | 'medium' | 'large' | 'distant';
  }
  
  let stars: Star[] = [];
  let galaxyVisible = true;
  
  const starColors = [
    'rgba(255, 255, 255, 1)', // Pure white
    'rgba(220, 220, 255, 1)', // Blue-white
    'rgba(255, 245, 220, 1)', // Warm white
    'rgba(200, 220, 255, 1)', // Cooler blue
    'rgba(255, 230, 200, 1)', // Slight yellow
  ];
  
  onMount(() => {
    // Respect reduced motion preference
    const prefersReducedMotion = window.matchMedia('(prefers-reduced-motion: reduce)').matches;
    if (prefersReducedMotion) {
      galaxyVisible = false;
      return;
    }
    
    // Create distant background stars (tiny, dim)
    for (let i = 0; i < 40; i++) {
      stars.push({
        id: i,
        x: Math.random() * 100,
        y: Math.random() * 100,
        size: Math.random() * 1.0 + 0.5, // 0.5-1.5px
        opacity: Math.random() * 0.5 + 0.2, // 0.2-0.7
        baseOpacity: Math.random() * 0.5 + 0.2,
        twinkleDuration: Math.random() * 8 + 6, // 6-14 seconds
        twinkleDelay: Math.random() * 10,
        driftX: (Math.random() - 0.5) * 0.5, // Very subtle drift
        driftY: (Math.random() - 0.5) * 0.3,
        driftDuration: Math.random() * 40 + 60, // 60-100 seconds
        color: starColors[Math.floor(Math.random() * starColors.length)],
        type: 'tiny'
      });
    }
    
    // Create small stars
    for (let i = 40; i < 70; i++) {
      stars.push({
        id: i,
        x: Math.random() * 100,
        y: Math.random() * 100,
        size: Math.random() * 1.2 + 0.8, // 0.8-2px
        opacity: Math.random() * 0.5 + 0.3, // 0.3-0.8
        baseOpacity: Math.random() * 0.5 + 0.3,
        twinkleDuration: Math.random() * 6 + 4, // 4-10 seconds
        twinkleDelay: Math.random() * 8,
        driftX: (Math.random() - 0.5) * 0.8,
        driftY: (Math.random() - 0.5) * 0.6,
        driftDuration: Math.random() * 30 + 50, // 50-80 seconds
        color: starColors[Math.floor(Math.random() * starColors.length)],
        type: 'small'
      });
    }
    
    // Create medium stars (more prominent)
    for (let i = 70; i < 85; i++) {
      stars.push({
        id: i,
        x: Math.random() * 100,
        y: Math.random() * 100,
        size: Math.random() * 1.5 + 1.5, // 1.5-3px
        opacity: Math.random() * 0.6 + 0.4, // 0.4-1.0
        baseOpacity: Math.random() * 0.6 + 0.4,
        twinkleDuration: Math.random() * 5 + 3, // 3-8 seconds
        twinkleDelay: Math.random() * 6,
        driftX: (Math.random() - 0.5) * 1.2,
        driftY: (Math.random() - 0.5) * 0.8,
        driftDuration: Math.random() * 25 + 40, // 40-65 seconds
        color: starColors[Math.floor(Math.random() * starColors.length)],
        type: 'medium'
      });
    }
    
    // Create a few larger, brighter stars
    for (let i = 85; i < 92; i++) {
      stars.push({
        id: i,
        x: Math.random() * 100,
        y: Math.random() * 100,
        size: Math.random() * 2 + 2.5, // 2.5-4.5px
        opacity: Math.random() * 0.7 + 0.6, // 0.6-1.3 (can exceed 1 for glow)
        baseOpacity: Math.random() * 0.7 + 0.6,
        twinkleDuration: Math.random() * 4 + 2, // 2-6 seconds
        twinkleDelay: Math.random() * 5,
        driftX: (Math.random() - 0.5) * 1.5,
        driftY: (Math.random() - 0.5) * 1.0,
        driftDuration: Math.random() * 20 + 35, // 35-55 seconds
        color: starColors[Math.floor(Math.random() * starColors.length)],
        type: 'large'
      });
    }
    
    // Create very distant, barely visible stars
    for (let i = 92; i < 120; i++) {
      stars.push({
        id: i,
        x: Math.random() * 100,
        y: Math.random() * 100,
        size: Math.random() * 0.8 + 0.4, // 0.4-1.2px
        opacity: Math.random() * 0.3 + 0.15, // 0.15-0.45
        baseOpacity: Math.random() * 0.3 + 0.15,
        twinkleDuration: Math.random() * 12 + 8, // 8-20 seconds
        twinkleDelay: Math.random() * 15,
        driftX: (Math.random() - 0.5) * 0.3,
        driftY: (Math.random() - 0.5) * 0.2,
        driftDuration: Math.random() * 60 + 80, // 80-140 seconds
        color: starColors[Math.floor(Math.random() * starColors.length)],
        type: 'distant'
      });
    }
    
    // Trigger reactive update
    stars = [...stars];
  });
</script>

{#if galaxyVisible && stars.length > 0}
  <!-- Galaxy Background -->
  <div class="fixed inset-0 pointer-events-none z-1 overflow-hidden galaxy-bg">
    <!-- Subtle nebula-like gradients -->
    <div class="absolute inset-0 galaxy-nebula"></div>
    
    <!-- Star field -->
    <div class="absolute inset-0">
      {#each stars as star (star.id)}
        <div
          class="absolute star"
          class:star-tiny={star.type === 'tiny'}
          class:star-small={star.type === 'small'}
          class:star-medium={star.type === 'medium'}
          class:star-large={star.type === 'large'}
          class:star-distant={star.type === 'distant'}
          style="
            left: {star.x}%; 
            top: {star.y}%; 
            width: {star.size}px; 
            height: {star.size}px; 
            opacity: {star.opacity};
            background-color: {star.color};
            animation: 
              star-twinkle {star.twinkleDuration}s ease-in-out infinite {star.twinkleDelay}s,
              star-drift {star.driftDuration}s ease-in-out infinite {star.twinkleDelay * 0.5}s;
            --drift-x: {star.driftX}%;
            --drift-y: {star.driftY}%;
            --base-opacity: {star.baseOpacity};
          "
        ></div>
      {/each}
    </div>
  </div>
{/if}

<style>
  .galaxy-bg {
    background: radial-gradient(ellipse at center, 
      rgba(8, 8, 20, 0.08) 0%, 
      rgba(3, 3, 10, 0.12) 50%, 
      rgba(0, 0, 3, 0.15) 100%);
  }
  
  .galaxy-nebula {
    background: 
      radial-gradient(circle at 20% 30%, rgba(30, 20, 60, 0.03) 0%, transparent 50%),
      radial-gradient(circle at 80% 70%, rgba(60, 30, 80, 0.025) 0%, transparent 40%),
      radial-gradient(circle at 60% 20%, rgba(20, 40, 80, 0.02) 0%, transparent 35%);
    animation: nebula-drift 120s ease-in-out infinite;
  }
  
  @keyframes nebula-drift {
    0%, 100% {
      transform: translate(0, 0) scale(1);
      opacity: 0.6;
    }
    25% {
      transform: translate(-10px, -5px) scale(1.02);
      opacity: 0.8;
    }
    50% {
      transform: translate(5px, -10px) scale(0.98);
      opacity: 0.4;
    }
    75% {
      transform: translate(-5px, 5px) scale(1.01);
      opacity: 0.7;
    }
  }
  
  .star {
    border-radius: 50%;
    position: absolute;
    filter: blur(0px);
  }
  
  .star-tiny {
    box-shadow: 0 0 1px currentColor;
  }
  
  .star-small {
    box-shadow: 0 0 2px currentColor;
  }
  
  .star-medium {
    box-shadow: 0 0 3px currentColor, 0 0 6px rgba(255, 255, 255, 0.3);
  }
  
  .star-large {
    box-shadow: 
      0 0 4px currentColor, 
      0 0 8px rgba(255, 255, 255, 0.4), 
      0 0 12px rgba(255, 255, 255, 0.2);
  }
  
  .star-distant {
    box-shadow: 0 0 0.5px currentColor;
    filter: blur(0.3px);
  }
  
  @keyframes star-twinkle {
    0%, 100% {
      opacity: var(--base-opacity);
      transform: scale(1);
    }
    25% {
      opacity: calc(var(--base-opacity) * 0.6);
      transform: scale(0.9);
    }
    50% {
      opacity: calc(var(--base-opacity) * 1.4);
      transform: scale(1.1);
    }
    75% {
      opacity: calc(var(--base-opacity) * 0.8);
      transform: scale(0.95);
    }
  }
  
  @keyframes star-drift {
    0%, 100% {
      transform: translate(0, 0);
    }
    25% {
      transform: translate(calc(var(--drift-x) * 0.3), calc(var(--drift-y) * 0.2));
    }
    50% {
      transform: translate(var(--drift-x), var(--drift-y));
    }
    75% {
      transform: translate(calc(var(--drift-x) * 0.7), calc(var(--drift-y) * 0.8));
    }
  }
  
  /* Respect reduced motion preferences */
  @media (prefers-reduced-motion: reduce) {
    .star {
      animation: none !important;
    }
    
    .galaxy-nebula {
      animation: none !important;
    }
  }
</style> 