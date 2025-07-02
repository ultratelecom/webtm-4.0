<script lang="ts">
  import { onMount } from 'svelte';
  import ParticleSystem from '$lib/ParticleSystem.svelte';
  import ThreePixelSpaceship from '$lib/ThreePixelSpaceship.svelte';
  import ThreeCountdown from '$lib/ThreeCountdown.svelte';
  
  let mouseX = 0;
  let mouseY = 0;
  let mainElement: HTMLElement;
  let glitchCount = 0;
  let showScoreAnimation = false;
  
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

  // Handle spaceship glitch scoring
  function handleSpaceshipGlitch() {
    glitchCount++;
    showScoreAnimation = true;
    
    // Reset animation after brief delay
    setTimeout(() => {
      showScoreAnimation = false;
    }, 600);
  }
  
  onMount(() => {
    // Improved mobile handling - allow some scrolling but control it
    const isMobile = window.innerWidth < 768;
    
    if (isMobile) {
      // On mobile, allow scrolling but make it smooth
      document.body.style.overflow = 'auto';
      document.documentElement.style.overflow = 'auto';
      document.body.style.overscrollBehavior = 'none'; // Prevent bounce
    } else {
      // On desktop, maintain the no-scroll behavior
      document.body.style.overflow = 'hidden';
      document.documentElement.style.overflow = 'hidden';
    }
    
    // Add mouse tracking
    window.addEventListener('mousemove', handleMouseMove);
    
    return () => {
      window.removeEventListener('mousemove', handleMouseMove);
    };
  });
</script>

<svelte:head>
  <title>Whisper - Silence. Encrypted. Delivered.</title>
  <meta name="robots" content="index, follow" />
</svelte:head>

<!-- Ambient Particle System -->
<ParticleSystem />

<!-- Three.js Pixel Spaceship Animation -->
<ThreePixelSpaceship on:glitch={handleSpaceshipGlitch} />

<!-- Glitch Counter Display -->
<div class="fixed top-6 right-6 z-20 text-right">
  <div class="text-white/60 text-sm font-mono mb-1">GLITCH SCORE</div>
  <div class="text-2xl font-black text-white font-mono tracking-wider glitch-score {showScoreAnimation ? 'score-pulse' : ''}">
    {glitchCount.toString().padStart(3, '0')}
  </div>
  {#if glitchCount > 0}
    <div class="text-xs text-white/40 font-mono mt-1">
      {glitchCount === 1 ? 'FIRST CONTACT' : 
       glitchCount < 5 ? 'SIGNAL DETECTED' :
       glitchCount < 10 ? 'FREQUENCY LOCKED' :
       glitchCount < 20 ? 'PATTERN RECOGNIZED' :
       glitchCount < 50 ? 'SYSTEM COMPROMISED' :
       'NEURAL INTERFACE'}
    </div>
  {/if}
</div>

<main 
  bind:this={mainElement}
  class="min-h-screen md:h-screen w-full bg-black text-white flex flex-col relative overflow-x-hidden layout-settle mobile-scroll"
  style="--mouse-x: 0px; --mouse-y: 0px"
>
  <!-- Logo - Top Left -->
  <div class="absolute top-4 md:top-6 left-4 md:left-6 z-10">
    <div class="relative">
      <!-- Heartbeat glow effect behind logo -->
      <div class="absolute inset-0 text-white font-black text-xl md:text-2xl logo-heartbeat">
        wyspr
      </div>
      <!-- Main logo with liquid hover effect -->
      <h1 class="relative text-white font-black text-xl md:text-2xl tracking-wider text-liquid">
        wyspr
      </h1>
    </div>
  </div>

  <!-- Main Content - Centered -->
  <div class="flex-1 flex items-center justify-center px-4 md:px-6 py-20 md:py-0">
    <div class="max-w-4xl mx-auto text-center space-y-6 md:space-y-8">
      
      <!-- Main Heading -->
      <div class="reveal-1">
        <h2 class="text-3xl sm:text-4xl md:text-5xl lg:text-7xl font-black text-white leading-tight tracking-tight">
          <span class="text-silence-glow">Silence.</span><br/>
          <span class="text-gray-300 text-glitch">Encrypted.</span><br/>
          <span class="text-gray-400">Delivered.</span>
        </h2>
      </div>

      <!-- Subheading -->
      <div class="reveal-2">
        <p class="text-lg md:text-xl lg:text-2xl text-gray-300 font-light max-w-2xl mx-auto leading-relaxed">
          A messaging app with no screenshots, no noise, no metadata.
        </p>
      </div>

      <!-- Animated Divider -->
      <div class="reveal-2 flex justify-center py-4">
        <div class="h-px bg-white divider-animated"></div>
      </div>

      <!-- Feature Points -->
      <div class="reveal-3">
        <div class="grid grid-cols-1 md:grid-cols-2 gap-4 md:gap-6 max-w-3xl mx-auto text-left">
          <div class="flex items-start space-x-3">
            <div class="w-2 h-2 bg-white rounded-full mt-3 flex-shrink-0"></div>
            <div>
              <h3 class="text-base md:text-lg font-bold text-white">Matrix-Powered</h3>
              <p class="text-gray-400 text-sm">Decentralized, federated messaging protocol</p>
            </div>
          </div>
          
          <div class="flex items-start space-x-3">
            <div class="w-2 h-2 bg-white rounded-full mt-3 flex-shrink-0"></div>
            <div>
              <h3 class="text-base md:text-lg font-bold text-white">Press-to-Reveal</h3>
              <p class="text-gray-400 text-sm">Messages appear only when intended</p>
            </div>
          </div>
          
          <div class="flex items-start space-x-3">
            <div class="w-2 h-2 bg-white rounded-full mt-3 flex-shrink-0"></div>
            <div>
              <h3 class="text-base md:text-lg font-bold text-white">Self-Erasing</h3>
              <p class="text-gray-400 text-sm">Conversations disappear automatically</p>
            </div>
          </div>
          
          <div class="flex items-start space-x-3">
            <div class="w-2 h-2 bg-white rounded-full mt-3 flex-shrink-0"></div>
            <div>
              <h3 class="text-base md:text-lg font-bold text-white">Zero Tracking</h3>
              <p class="text-gray-400 text-sm">You can't leak what you don't log</p>
            </div>
          </div>
        </div>
      </div>

      <!-- Brand Copy -->
      <div class="reveal-4 py-4 md:py-6">
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


