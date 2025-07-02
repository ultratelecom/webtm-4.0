<script lang="ts">
  import { onMount, createEventDispatcher } from 'svelte';
  import * as THREE from 'three';
  
  const dispatch = createEventDispatcher<{
    glitch: void;
  }>();
  
  interface SpaceshipState {
    x: number;
    y: number;
    rotationZ: number;
    rotationX: number;
    rotationY: number;
    scale: number;
    opacity: number;
    isGlitching: boolean;
    speed: number;
    direction: { x: number; y: number };
  }
  
  let spaceship: SpaceshipState = {
    x: -100,
    y: 50,
    rotationZ: 0,
    rotationX: 0,
    rotationY: 0,
    scale: 1,
    opacity: 0.6,
    isGlitching: false,
    speed: 0.25,
    direction: { x: 1, y: 0 }
  };
  
  let animationId: number;
  let glitchTimeout: number;
  let canvas: HTMLCanvasElement;
  let scene: THREE.Scene;
  let camera: THREE.PerspectiveCamera;
  let renderer: THREE.WebGLRenderer;
  let spaceshipGroup: THREE.Group;
  let mouseX = 0;
  let mouseY = 0;
  let spaceshipScreenPos = { x: 0, y: 0 };
  
  // Spaceship shape definition (same as CSS version)
  const spaceshipPattern = [
    [0, 1, 0],     // Nose
    [1, 1, 1],     // Front body
    [1, 1, 1, 1, 1], // Wings
    [0, 1, 1, 1, 0], // Main body
    [0, 1, 0, 1, 0], // Engines
    [0, 1, 0, 1, 0]  // Thrusters
  ];
  
  function createSpaceship() {
    spaceshipGroup = new THREE.Group();
    
         // Create geometry and materials
     const geometry = new THREE.BoxGeometry(0.8, 0.8, 0.8);
    const materials = [
      new THREE.MeshBasicMaterial({ color: 0xffffff, transparent: true, opacity: 0.9 }), // Front
      new THREE.MeshBasicMaterial({ color: 0xcccccc, transparent: true, opacity: 0.7 }), // Back
      new THREE.MeshBasicMaterial({ color: 0xeeeeee, transparent: true, opacity: 0.8 }), // Top
      new THREE.MeshBasicMaterial({ color: 0xaaaaaa, transparent: true, opacity: 0.6 }), // Bottom
      new THREE.MeshBasicMaterial({ color: 0xdddddd, transparent: true, opacity: 0.75 }), // Right
      new THREE.MeshBasicMaterial({ color: 0xbbbbbb, transparent: true, opacity: 0.65 })  // Left
    ];
    
    // Create cubes based on pattern
    spaceshipPattern.forEach((row, rowIndex) => {
      row.forEach((filled, colIndex) => {
        if (filled) {
          const cube = new THREE.Mesh(geometry, materials);
          
          // Position relative to center
          const offsetX = colIndex - Math.floor(row.length / 2);
          const offsetY = -(rowIndex - Math.floor(spaceshipPattern.length / 2));
          
          cube.position.set(offsetX, offsetY, 0);
          
          // Add subtle wireframe effect
          const edges = new THREE.EdgesGeometry(geometry);
          const line = new THREE.LineSegments(edges, new THREE.LineBasicMaterial({ 
            color: 0xffffff, 
            transparent: true, 
            opacity: 0.3 
          }));
          cube.add(line);
          
          spaceshipGroup.add(cube);
        }
      });
    });
    
    scene.add(spaceshipGroup);
  }
  
  function getRandomPosition() {
    return {
      x: Math.random() * 120 - 10,
      y: Math.random() * 80 + 10,
      rotationZ: Math.random() * Math.PI * 2,
      rotationX: Math.random() * Math.PI * 2,
      rotationY: Math.random() * Math.PI * 2,
      scale: Math.random() * 0.4 + 0.8
    };
  }
  
  function getRandomDirection() {
    const angle = Math.random() * Math.PI * 2;
    return {
      x: Math.cos(angle),
      y: Math.sin(angle) * 0.3
    };
  }
  
  function triggerGlitch(isMouseTriggered = false) {
    spaceship.isGlitching = true;
    
    // Dispatch glitch event only for user-triggered glitches
    if (isMouseTriggered) {
      dispatch('glitch');
    }
    
    // Glitch materials
    if (spaceshipGroup) {
      spaceshipGroup.children.forEach((cube) => {
        if (cube instanceof THREE.Mesh) {
          const materials = cube.material as THREE.MeshBasicMaterial[];
          materials.forEach((mat) => {
            mat.color.setHex(Math.random() > 0.5 ? 0xff0000 : 0x00ffff);
          });
        }
      });
    }
    
    setTimeout(() => {
      spaceship.isGlitching = false;
      
      // Reset materials
      if (spaceshipGroup) {
        spaceshipGroup.children.forEach((cube) => {
          if (cube instanceof THREE.Mesh) {
            const materials = cube.material as THREE.MeshBasicMaterial[];
            materials[0].color.setHex(0xffffff);
            materials[1].color.setHex(0xcccccc);
            materials[2].color.setHex(0xeeeeee);
            materials[3].color.setHex(0xaaaaaa);
            materials[4].color.setHex(0xdddddd);
            materials[5].color.setHex(0xbbbbbb);
          }
        });
      }
      
      // Teleport
      const newPos = getRandomPosition();
      spaceship.x = newPos.x;
      spaceship.y = newPos.y;
      spaceship.rotationZ = newPos.rotationZ;
      spaceship.rotationX = newPos.rotationX;
      spaceship.rotationY = newPos.rotationY;
      spaceship.scale = newPos.scale;
      spaceship.direction = getRandomDirection();
      spaceship.speed = Math.random() * 0.4 + 0.15;
      
      // Only schedule next auto-glitch if this wasn't mouse-triggered
      if (!isMouseTriggered) {
        const nextGlitch = Math.random() * 5000 + 3000;
        glitchTimeout = setTimeout(triggerGlitch, nextGlitch);
      }
    }, 150);
  }
  
  function animate() {
    if (!spaceship.isGlitching && spaceshipGroup) {
      // Move spaceship
      spaceship.x += spaceship.direction.x * spaceship.speed;
      spaceship.y += spaceship.direction.y * spaceship.speed;
      
      // 3D tumbling
      spaceship.rotationZ += 0.01;
      spaceship.rotationX += 0.012;
      spaceship.rotationY += 0.008;
      
      // Reset if off screen
      if (spaceship.x > 150 || spaceship.x < -50) {
        const newPos = getRandomPosition();
        spaceship.x = newPos.x < 50 ? -20 : 120;
        spaceship.y = newPos.y;
      }
      
      // Update Three.js object
      const screenX = (spaceship.x / 100) * window.innerWidth;
      const screenY = (spaceship.y / 100) * window.innerHeight;
      
      // Convert screen coordinates to world coordinates
      const vector = new THREE.Vector3(
        (screenX / window.innerWidth) * 2 - 1,
        -(screenY / window.innerHeight) * 2 + 1,
        0.5
      );
      vector.unproject(camera);
      
                   spaceshipGroup.position.copy(vector);
      spaceshipGroup.rotation.set(spaceship.rotationX, spaceship.rotationY, spaceship.rotationZ);
      spaceshipGroup.scale.setScalar(spaceship.scale * 0.0135);
      
      // Update screen position for mouse interaction
      const spaceshipWorldPos = spaceshipGroup.position.clone();
      spaceshipWorldPos.project(camera);
      spaceshipScreenPos.x = (spaceshipWorldPos.x * 0.5 + 0.5) * window.innerWidth;
      spaceshipScreenPos.y = (spaceshipWorldPos.y * -0.5 + 0.5) * window.innerHeight;
      
      // Check mouse proximity (within 80 pixels on desktop, 120 on mobile)
      const isMobile = window.innerWidth < 768;
      const proximityThreshold = isMobile ? 120 : 80;
      
      const distance = Math.sqrt(
        Math.pow(mouseX - spaceshipScreenPos.x, 2) + 
        Math.pow(mouseY - spaceshipScreenPos.y, 2)
      );
      
      if (distance < proximityThreshold && !spaceship.isGlitching) {
        // Clear any pending auto-glitch
        if (glitchTimeout) {
          clearTimeout(glitchTimeout);
        }
        triggerGlitch(true);
        
        // Schedule next auto-glitch after mouse interaction
        setTimeout(() => {
          const nextGlitch = Math.random() * 5000 + 3000;
          glitchTimeout = setTimeout(triggerGlitch, nextGlitch);
        }, 2000);
      }
    }
    
    if (renderer && scene && camera) {
      renderer.render(scene, camera);
    }
    
    animationId = requestAnimationFrame(animate);
  }
  
  onMount(() => {
    // Check for reduced motion
    const prefersReducedMotion = window.matchMedia('(prefers-reduced-motion: reduce)').matches;
    if (prefersReducedMotion) {
      return;
    }
    
    // Setup Three.js
    scene = new THREE.Scene();
    camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
    camera.position.z = 10;
    
    renderer = new THREE.WebGLRenderer({ 
      canvas, 
      alpha: true, 
      antialias: true 
    });
    renderer.setSize(window.innerWidth, window.innerHeight);
    renderer.setClearColor(0x000000, 0);
    
    // Create spaceship
    createSpaceship();
    
    // Initialize position
    const initialPos = getRandomPosition();
    spaceship.x = initialPos.x;
    spaceship.y = initialPos.y;
    spaceship.rotationZ = initialPos.rotationZ;
    spaceship.rotationX = initialPos.rotationX;
    spaceship.rotationY = initialPos.rotationY;
    spaceship.scale = initialPos.scale;
    spaceship.direction = getRandomDirection();
    
    // Start animation
    animate();
    
    // Start glitch cycle
    const firstGlitch = Math.random() * 3000 + 2000;
    glitchTimeout = setTimeout(triggerGlitch, firstGlitch);
    
    // Handle mouse movement (with touch support for mobile)
    const handleMouseMove = (event: MouseEvent) => {
      mouseX = event.clientX;
      mouseY = event.clientY;
    };
    
    const handleTouchMove = (event: TouchEvent) => {
      if (event.touches.length > 0) {
        mouseX = event.touches[0].clientX;
        mouseY = event.touches[0].clientY;
      }
    };
    
    // Handle resize
    const handleResize = () => {
      camera.aspect = window.innerWidth / window.innerHeight;
      camera.updateProjectionMatrix();
      renderer.setSize(window.innerWidth, window.innerHeight);
    };
    
    window.addEventListener('mousemove', handleMouseMove);
    window.addEventListener('touchmove', handleTouchMove, { passive: true });
    window.addEventListener('resize', handleResize);
    
    return () => {
      if (animationId) {
        cancelAnimationFrame(animationId);
      }
      if (glitchTimeout) {
        clearTimeout(glitchTimeout);
      }
      window.removeEventListener('mousemove', handleMouseMove);
      window.removeEventListener('touchmove', handleTouchMove);
      window.removeEventListener('resize', handleResize);
      if (renderer) {
        renderer.dispose();
      }
    };
  });
</script>

<canvas 
  bind:this={canvas}
  class="fixed inset-0 pointer-events-none z-4"
  style="opacity: {spaceship.opacity};"
></canvas>

<style>
  canvas {
    display: block;
  }
</style> 