<!-- charset: utf-8 -->
<script>
    import { onMount } from 'svelte';
    import gsap from 'gsap';
    import FaSnowflake from 'svelte-icons/fa/FaSnowflake.svelte';
    import FaHandPointer from 'svelte-icons/fa/FaHandPointer.svelte';
  
    const messages = [
      { type: 'text', content: "¡Felices reyes!" },
      { type: 'text', content: "Llevamos muuuchos años juntos y cada vez es mas difícil ser original" },
      { type: 'text', content: "Asi que este año vamos a hacer algo diferente, vas a elegir tu propio regalo" },
      { type: 'text', content: "Tienes dos opciones..." },
      { type: 'text', content: "Dos noches de hotel en Asturias" },
      { type: 'text', content: "o..." },
      { type: 'text', content: "Un billete de avión a Japón" },
      { type: 'text', content: "Es broma, no me da el dinero" },
      { type: 'text', content: "Pero puedes elegir entre Copenhague o Amsterdam" },
      { type: 'text', content: "Si quieres puedes escuchar algo de música mientras lo piensas" },
      { type: 'spotify', content: "3n3Ppam7vgaVa1iaRUc9Lp" },
      { type: 'text', content: "Te quiero!" },
    ];
  
    let showIndicator = true;
  
    let cards = messages.map((message, index) => ({
      id: index,
      type: message.type,
      content: message.content,
      rotation: (Math.random() - 0.5) * 20,
      x: (Math.random() - 0.5) * 40,
      y: (Math.random() - 0.5) * 40,
      zIndex: messages.length - index
    }));
  
    onMount(() => {
  
      // Cargar el SDK de Spotify
      const script = document.createElement('script');
      script.src = 'https://open.spotify.com/embed-podcast/iframe-api/v1';
      script.async = true;
      document.body.appendChild(script);
  
      script.onload = () => {
        window.onSpotifyIframeApiReady = (IFrameAPI) => {
          const element = document.getElementById('spotify-embed');
          if (element) {
            const options = {
              width: '100%',
              height: '80',
              uri: `spotify:track:365MK5X3FgXaW7IWmNzNV5?si=uOu8UPf7Tim3kL60b6W6dw`
            };
            IFrameAPI.createController(element, options, () => {});
          }
        };
      };
  
      return () => {
        document.body.removeChild(script);
      };
    });
  
    const removeCard = (card) => {
      if (card.id === 0) {
        showIndicator = false;
      }
      
      gsap.to(`#card-${card.id}`, {
        x: window.innerWidth,
        rotation: 90,
        opacity: 0,
        duration: 0.8,
        onComplete: () => {
          cards = cards.filter(c => c.id !== card.id);
        }
      });
    };
  </script>
  
  <style>
    @import url('https://fonts.googleapis.com/css2?family=Bebas+Neue&display=swap');
    .container {
      height: 90vh;
      display: flex;
      justify-content: center;
      align-items: center;
      background-color: #000;
      font-family: "Bebas Neue", serif;
      overflow: hidden;
      margin: 0;
      overflow-x: hidden;
    }
  
    .cards-container {
      position: relative;
      width: 300px;
      height: 400px;
    }
  
    .card {
      position: absolute;
      width: 100%;
      height: 100%;
      background: #111;
      border-radius: 15px;
      color: white;
      padding: 20px;
      box-sizing: border-box;
      cursor: pointer;
      transition: transform 0.3s ease;
      display: flex;
      align-items: center;
      justify-content: center;
      text-align: center;
      font-size: 24px;
      box-shadow: 0 4px 8px rgba(255,255,255,0.1);
      border: 2px solid white;
      font-weight: 400;
      font-style: normal;
      font-optical-sizing: auto;
      font-family: "Bebas Neue", serif;
      font-size: 2rem;
    }
  
    .card:hover {
      transform: scale(1.02);
    }
  
    @keyframes snowfall {
      0% {
        transform: translateY(-95vh) translateX(0);
      }
      100% {
        transform: translateY(95vh) translateX(20px);
      }
    }
  
    .snowflake {
      position: absolute;
      top: -10px;
      color: white;
      width: 1rem;
      height: 1rem;
      animation: snowfall 10s linear infinite;
    }
  
    .indicator {
      position: absolute;
      top: 80%;
      left: 50%;
      transform: translateX(-50%);
      color: white;
      text-align: center;
      animation: bounce 1.5s infinite;
      z-index: 1000;
    }
  
    .indicator-text {
      font-size: 12px;
      white-space: nowrap;
    }
  
    @keyframes bounce {
      0%, 100% {
        transform: translateX(-50%) translateY(0);
      }
      50% {
        transform: translateX(-50%) translateY(-20px);
      }
    }
  
    .icon-wrapper {
      width: 20px;
      height: 20px;
      margin: 0 auto 10px;
      color: white;
    }
  
    @keyframes fadeOut {
      from {
        opacity: 1;
      }
      to {
        opacity: 0;
      }
    }
  </style>
  
  
  <div class="container">
    <div class="cards-container">
      {#if showIndicator}
        <div class="indicator" style="opacity: {showIndicator ? 1 : 0}">
          <div class="icon-wrapper">
            <FaHandPointer />
          </div>
          <div class="indicator-text">Pulsa para empezar</div>
        </div>
      {/if}
      {#each cards as card (card.id)}
      <div
        id="card-{card.id}"
        class="card {card.type === 'spotify' ? 'spotify-card' : ''}"
        style="transform: rotate({card.rotation}deg) translate({card.x}px, {card.y}px); z-index: {card.zIndex};"
        on:click={() => removeCard(card)}
        >
          {#if card.type === 'spotify'}
            <div id="spotify-embed"></div>
          {:else}
            {card.content}
          {/if}
        </div>
        {/each}
    </div>
  
    {#each Array(50) as _, i}
      <div
        class="snowflake"
        style="
          left: {Math.random() * 100}%;
          animation-duration: {Math.random() * 5 + 5}s;
          font-size: {Math.random() * 1.5 + 0.5}rem;
        "
      >
        <FaSnowflake />
      </div>
    {/each}
  </div>
  