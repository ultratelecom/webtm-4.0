<script lang="ts">
  import { onMount } from 'svelte';
  
  // Countdown target: Fixed launch date - July 21, 2025
  const launchDate = new Date('2025-07-21T23:59:59Z');
  
  let timeRemaining = {
    days: 0,
    hours: 0,
    minutes: 0,
    seconds: 0
  };
  
  let countdownInterval: number;
  
  function updateCountdown() {
    const now = new Date().getTime();
    const distance = launchDate.getTime() - now;
    
    if (distance > 0) {
      timeRemaining.days = Math.floor(distance / (1000 * 60 * 60 * 24));
      timeRemaining.hours = Math.floor((distance % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60));
      timeRemaining.minutes = Math.floor((distance % (1000 * 60 * 60)) / (1000 * 60));
      timeRemaining.seconds = Math.floor((distance % (1000 * 60)) / 1000);
    } else {
      timeRemaining = { days: 0, hours: 0, minutes: 0, seconds: 0 };
    }
  }
  
  onMount(() => {
    updateCountdown();
    countdownInterval = setInterval(updateCountdown, 1000);
    
    return () => {
      if (countdownInterval) {
        clearInterval(countdownInterval);
      }
    };
  });
</script>

<div class="countdown-container">
  <!-- Launch Message -->
  <div class="launch-header">
    <h3 class="minecraft-title">LAUNCHING IN</h3>
  </div>
  
  <!-- Countdown Display -->
  <div class="countdown-grid">
    <div class="time-unit">
      <div class="minecraft-number">
        {timeRemaining.days.toString().padStart(2, '0')}
      </div>
      <div class="time-label">DAYS</div>
    </div>
    
    <div class="time-unit">
      <div class="minecraft-number">
        {timeRemaining.hours.toString().padStart(2, '0')}
      </div>
      <div class="time-label">HOURS</div>
    </div>
    
    <div class="time-unit">
      <div class="minecraft-number">
        {timeRemaining.minutes.toString().padStart(2, '0')}
      </div>
      <div class="time-label">MINUTES</div>
    </div>
    
    <div class="time-unit">
      <div class="minecraft-number">
        {timeRemaining.seconds.toString().padStart(2, '0')}
      </div>
      <div class="time-label">SECONDS</div>
    </div>
  </div>
</div>

<style>
  .countdown-container {
    width: 100%;
    max-width: 600px;
    margin: 0 auto;
    padding: 0.5rem 1rem;
    text-align: center;
  }
  
  .launch-header {
    margin-bottom: 1rem;
  }
  
  .minecraft-title {
    font-size: 0.9rem;
    font-weight: 800;
    color: #ffffff;
    letter-spacing: 0.2em;
    text-transform: uppercase;
    
    /* Minecraft 3D effect */
    text-shadow: 
      1px 1px 0 #cccccc,
      2px 2px 0 #aaaaaa,
      3px 3px 0 #888888,
      4px 4px 0 #666666,
      5px 5px 8px rgba(0, 0, 0, 0.3);
    
    /* Subtle bobbing animation */
    animation: minecraft-bob 3s ease-in-out infinite;
  }
  
  .countdown-grid {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 1rem;
    max-width: 500px;
    margin: 0 auto;
  }
  
  .time-unit {
    background: rgba(255, 255, 255, 0.03);
    border: 1px solid rgba(255, 255, 255, 0.08);
    border-radius: 8px;
    padding: 1rem 0.5rem;
    backdrop-filter: blur(5px);
  }
  
  .minecraft-number {
    font-size: 2rem;
    font-weight: 900;
    color: #ffffff;
    line-height: 1;
    margin-bottom: 0.3rem;
    font-family: monospace;
    
    /* Minecraft 3D number effect */
    text-shadow: 
      1px 1px 0 #e0e0e0,
      2px 2px 0 #c0c0c0,
      3px 3px 0 #a0a0a0,
      4px 4px 0 #808080,
      5px 5px 0 #606060,
      6px 6px 0 #404040,
      7px 7px 10px rgba(0, 0, 0, 0.4);
    
    /* Subtle tilt animation */
    animation: minecraft-tilt 4s ease-in-out infinite;
  }
  
  .time-label {
    font-size: 0.6rem;
    font-weight: 600;
    color: rgba(255, 255, 255, 0.6);
    letter-spacing: 0.1em;
    text-transform: uppercase;
    margin-top: 0.2rem;
  }
  
  /* Minecraft-style bobbing animation for title */
  @keyframes minecraft-bob {
    0%, 100% {
      transform: translateY(0px) rotate(0deg);
    }
    50% {
      transform: translateY(-2px) rotate(0.5deg);
    }
  }
  
  /* Minecraft-style subtle tilt for numbers */
  @keyframes minecraft-tilt {
    0%, 100% {
      transform: rotate(0deg) translateY(0px);
    }
    25% {
      transform: rotate(0.5deg) translateY(-1px);
    }
    75% {
      transform: rotate(-0.5deg) translateY(-1px);
    }
  }
  
  /* Stagger the tilt animation for each number */
  .time-unit:nth-child(1) .minecraft-number {
    animation-delay: 0s;
  }
  
  .time-unit:nth-child(2) .minecraft-number {
    animation-delay: 1s;
  }
  
  .time-unit:nth-child(3) .minecraft-number {
    animation-delay: 2s;
  }
  
  .time-unit:nth-child(4) .minecraft-number {
    animation-delay: 3s;
  }
  
  /* Responsive design */
  @media (max-width: 768px) {
    .countdown-grid {
      grid-template-columns: repeat(2, 1fr);
      gap: 0.8rem;
    }
    
    .minecraft-number {
      font-size: 1.8rem;
    }
    
    .time-unit {
      padding: 0.8rem 0.4rem;
    }
  }
  
  @media (max-width: 480px) {
    .countdown-grid {
      grid-template-columns: repeat(2, 1fr);
      gap: 0.6rem;
    }
    
    .minecraft-number {
      font-size: 1.5rem;
    }
    
    .time-unit {
      padding: 0.6rem 0.3rem;
    }
  }
  
  /* Respect reduced motion preferences */
  @media (prefers-reduced-motion: reduce) {
    .minecraft-title,
    .minecraft-number {
      animation: none !important;
    }
  }
</style> 