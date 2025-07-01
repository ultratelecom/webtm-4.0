<script lang="ts">
  import { onMount } from 'svelte';
  import ParticleSystem from '$lib/ParticleSystem.svelte';
  import ThreePixelSpaceship from '$lib/ThreePixelSpaceship.svelte';
  
  let email = '';
  let isNotified = false;
  let mouseX = 0;
  let mouseY = 0;
  let notifyButton: HTMLButtonElement;
  let mainElement: HTMLElement;
  
  // Mouse tracking for parallax and magnetic effects
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
    
    // Magnetic button effect - Enhanced
    if (notifyButton) {
      const rect = notifyButton.getBoundingClientRect();
      const buttonCenterX = rect.left + rect.width / 2;
      const buttonCenterY = rect.top + rect.height / 2;
      const distance = Math.sqrt(
        Math.pow(event.clientX - buttonCenterX, 2) + 
        Math.pow(event.clientY - buttonCenterY, 2)
      );
      
      if (distance < 120) {
        const strength = Math.max(0, (120 - distance) / 120);
        const moveX = (event.clientX - buttonCenterX) * strength * 0.08;
        const moveY = (event.clientY - buttonCenterY) * strength * 0.08;
        
        notifyButton.style.transform = `translate(${moveX}px, ${moveY}px)`;
      } else {
        notifyButton.style.transform = 'translate(0, 0)';
      }
    }
  }
  
  function handleNotify() {
    if (email && email.includes('@')) {
      isNotified = true;
      console.log('Email registered:', email);
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
        ZERO
      </div>
      <!-- Main logo with liquid hover effect -->
      <h1 class="relative text-white font-black text-2xl tracking-wider text-liquid">
        ZERO
      </h1>
    </div>
  </div>

  <!-- Main Content - Centered -->
  <div class="flex-1 flex items-center justify-center px-6">
    <div class="max-w-4xl mx-auto text-center space-y-8">
      
      <!-- Main Heading -->
      <div class="reveal-1">
        <h2 class="text-5xl md:text-7xl font-black text-white leading-tight tracking-tight">
          <span class="text-silence-glow text-liquid">Silence.</span><br/>
          <span class="text-gray-300 text-glitch">Encrypted.</span><br/>
          <span class="text-gray-400 text-liquid">Delivered.</span>
        </h2>
      </div>

      <!-- Subheading -->
      <div class="reveal-2">
        <p class="text-xl md:text-2xl text-gray-300 font-light max-w-2xl mx-auto leading-relaxed text-liquid">
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
          <div class="flex items-start space-x-3 text-liquid">
            <div class="w-2 h-2 bg-white rounded-full mt-3 flex-shrink-0"></div>
            <div>
              <h3 class="text-lg font-bold text-white">Matrix-Powered</h3>
              <p class="text-gray-400 text-sm">Decentralized, federated messaging protocol</p>
            </div>
          </div>
          
          <div class="flex items-start space-x-3 text-liquid">
            <div class="w-2 h-2 bg-white rounded-full mt-3 flex-shrink-0"></div>
            <div>
              <h3 class="text-lg font-bold text-white">Press-to-Reveal</h3>
              <p class="text-gray-400 text-sm">Messages appear only when intended</p>
            </div>
          </div>
          
          <div class="flex items-start space-x-3 text-liquid">
            <div class="w-2 h-2 bg-white rounded-full mt-3 flex-shrink-0"></div>
            <div>
              <h3 class="text-lg font-bold text-white">Self-Erasing</h3>
              <p class="text-gray-400 text-sm">Conversations disappear automatically</p>
            </div>
          </div>
          
          <div class="flex items-start space-x-3 text-liquid">
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
          <p class="text-liquid">"You can't leak what you don't log."</p>
          <p class="text-liquid">"Designed for silence."</p>
          <p class="text-liquid">"Matrix-powered. Zero-trace."</p>
        </div>
      </div>

      <!-- Email Signup -->
      <div class="reveal-5">
        {#if !isNotified}
          <div class="max-w-md mx-auto">
            <p class="text-gray-300 mb-4 text-lg font-medium text-liquid">Get notified when we launch</p>
            <div class="flex gap-3">
              <input
                type="email"
                bind:value={email}
                placeholder="your@email.com"
                class="flex-1 px-4 py-3 bg-gray-900 border border-gray-700 rounded-lg text-white placeholder-gray-500 focus:outline-none input-focus-ring transition-all duration-300"
              />
              <button
                bind:this={notifyButton}
                on:click={handleNotify}
                class="px-6 py-3 bg-white text-black font-bold rounded-lg button-elevation magnetic-button transition-all duration-300"
              >
                Notify Me
              </button>
            </div>
          </div>
        {:else}
          <div class="max-w-md mx-auto reveal-1">
            <div class="text-green-400 text-lg font-medium">
              ✓ You'll be the first to know
            </div>
            <p class="text-gray-400 text-sm mt-2">
              We'll notify you when ZERO launches
            </p>
          </div>
        {/if}
      </div>
    </div>
  </div>
</main>

<style>
  /* Ensure no scrolling on this component */
  :global(html, body) {
    overflow: hidden !important;
    height: 100vh;
  }
</style>
