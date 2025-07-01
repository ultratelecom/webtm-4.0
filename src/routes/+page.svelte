<script lang="ts">
  import { onMount } from 'svelte';
  import ParticleSystem from '$lib/ParticleSystem.svelte';
  import ThreePixelSpaceship from '$lib/ThreePixelSpaceship.svelte';
  import ThreeCountdown from '$lib/ThreeCountdown.svelte';
  
  let mouseX = 0;
  let mouseY = 0;
  let mainElement: HTMLElement;
  
  // Mouse tracking for parallax effects
  function handleMouseMove(event: MouseEvent) {
    mouseX = event.clientX;
    mouseY = event.clientY;
    
    // Micro-parallax for background elements
    if (mainElement) {
      const centerX = window.innerWidth / 2;
      const centerY = window.innerHeight / 2;
      const moveX = (event.clientX - centerX) * 0.01;
      const moveY = (event.clientY - centerY) * 0.01;
      
      mainElement.style.setProperty('--mouse-x', `${moveX}px`);
      mainElement.style.setProperty('--mouse-y', `${moveY}px`);
    }
  }
  
  onMount(() => {
    // Ensure no scrolling
    document.body.style.overflow = 'hidden';
    document.documentElement.style.overflow = 'hidden';
    
    // Add mouse tracking
    window.addEventListener('mousemove', handleMouseMove);
    
    return () => {
      window.removeEventListener('mousemove', handleMouseMove);
    };
  });
</script>

<svelte:head>
  <meta name="robots" content="index, follow" />
</svelte:head>

<!-- Ambient Particle System -->
<ParticleSystem />

<!-- Three.js Pixel Spaceship Animation -->
<ThreePixelSpaceship />

<main 
  bind:this={mainElement}
  class="h-screen w-full bg-black text-white flex flex-col relative overflow-hidden layout-settle"
  style="--mouse-x: 0px; --mouse-y: 0px"
>
  <!-- Breathing Background Gradients - Removed -->

  <!-- Logo - Top Left -->
  <div class="absolute top-6 left-6 z-10">
    <div class="relative">
      <!-- Heartbeat glow effect behind logo -->
      <div class="absolute inset-0 text-white font-black text-2xl logo-heartbeat">
        wyspr
      </div>
      <!-- Main logo with liquid hover effect -->
      <h1 class="relative text-white font-black text-2xl tracking-wider text-liquid">
        wyspr
      </h1>
    </div>
  </div>

  <!-- Main Content - Centered -->
  <div class="flex-1 flex items-center justify-center px-6">
    <div class="max-w-4xl mx-auto text-center space-y-8">
      
      <!-- Main Heading -->
      <div class="reveal-1">
        <h2 class="text-5xl md:text-7xl font-black text-white leading-tight tracking-tight">
          <span class="text-silence-glow">Silence.</span><br/>
          <span class="text-gray-300 text-glitch">Encrypted.</span><br/>
          <span class="text-gray-400">Delivered.</span>
        </h2>
      </div>

      <!-- Subheading -->
      <div class="reveal-2">
        <p class="text-xl md:text-2xl text-gray-300 font-light max-w-2xl mx-auto leading-relaxed">
          A messaging app with no screenshots, no noise, no metadata.
        </p>
      </div>

      <!-- Animated Divider -->
      <div class="reveal-2 flex justify-center py-4">
        <div class="h-px bg-white divider-animated"></div>
      </div>

      <!-- Feature Points -->
      <div class="reveal-3">
        <div class="grid grid-cols-1 md:grid-cols-2 gap-6 max-w-3xl mx-auto text-left">
          <div class="flex items-start space-x-3">
            <div class="w-2 h-2 bg-white rounded-full mt-3 flex-shrink-0"></div>
            <div>
              <h3 class="text-lg font-bold text-white">Matrix-Powered</h3>
              <p class="text-gray-400 text-sm">Decentralized, federated messaging protocol</p>
            </div>
          </div>
          
          <div class="flex items-start space-x-3">
            <div class="w-2 h-2 bg-white rounded-full mt-3 flex-shrink-0"></div>
            <div>
              <h3 class="text-lg font-bold text-white">Press-to-Reveal</h3>
              <p class="text-gray-400 text-sm">Messages appear only when intended</p>
            </div>
          </div>
          
          <div class="flex items-start space-x-3">
            <div class="w-2 h-2 bg-white rounded-full mt-3 flex-shrink-0"></div>
            <div>
              <h3 class="text-lg font-bold text-white">Self-Erasing</h3>
              <p class="text-gray-400 text-sm">Conversations disappear automatically</p>
            </div>
          </div>
          
          <div class="flex items-start space-x-3">
            <div class="w-2 h-2 bg-white rounded-full mt-3 flex-shrink-0"></div>
            <div>
              <h3 class="text-lg font-bold text-white">Zero Tracking</h3>
              <p class="text-gray-400 text-sm">You can't leak what you don't log</p>
            </div>
          </div>
        </div>
      </div>

      <!-- Brand Copy -->
      <div class="reveal-4 py-6">
        <div class="space-y-2 text-gray-500 text-sm font-medium">
          <p>"You can't leak what you don't log."</p>
          <p>"Designed for silence."</p>
          <p>"Matrix-powered. Zero-trace."</p>
        </div>
      </div>

      <!-- Launch Countdown -->
      <div class="reveal-5">
        <div class="max-w-5xl mx-auto">
          <ThreeCountdown />
        </div>
      </div>
    </div>
  </div>
</main>


