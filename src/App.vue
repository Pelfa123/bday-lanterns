<template>
  <div class="app-container">
    <!-- Fixed Background -->
    <div class="background-layer">
      <!-- Stars Distant -->
      <div id="stars-distant" class="star-container distant">
        <svg width="100%" height="100%">
          <circle v-for="n in 150" :key="'sd'+n" 
            :cx="starsDist[n-1].x" :cy="starsDist[n-1].y" :r="starsDist[n-1].r" 
            fill="white" />
        </svg>
      </div>
      <!-- Stars Closer -->
      <div id="stars-closer" class="star-container closer">
        <svg width="100%" height="100%">
          <circle v-for="n in 80" :key="'sc'+n" 
            :cx="starsClose[n-1].x" :cy="starsClose[n-1].y" :r="starsClose[n-1].r" 
            fill="#fef08a" />
        </svg>
      </div>
    </div>

    <!-- Fireflies -->
    <div class="firefly-overlay">
      <div v-for="n in 12" :key="'ff'+n" class="firefly" :style="fireflyStyles[n-1]"></div>
    </div>

    <!-- UI -->
    <div class="ui-overlay">
      <button @click="toggleMusic" class="music-btn">
        <span v-if="!isPlaying">🔇</span>
        <div v-else class="music-bars">
          <span></span><span></span><span></span>
        </div>
      </button>
      <div class="music-label">{{ isPlaying ? 'Singing Along' : 'Turn on the Magic' }}</div>
    </div>

    <!-- Hidden Audio -->
    <audio ref="audioPlayer" loop>
      <source src="/src/assets/Mandy Moore, Zachary Levi - I See the Light (From TangledSing-Along).mp3" type="audio/mpeg">
    </audio>
    <audio ref="specialAudioPlayer" @ended="handleSpecialAudioEnd">
      <source src="/src/assets/songforher.mp3" type="audio/mpeg">
    </audio>

    <!-- Content -->
    <div class="content-wrapper">
      <section class="intro-section">
        <h1 class="header-main">Happy Birthday<br>My Love! ✨</h1>
        <p class="romance-sub">Our Lantern Story</p>
        <div class="scroll-indicator">
          <p>Scroll down...</p>
          <div class="mouse">
            <div class="wheel"></div>
          </div>
        </div>
      </section>

      <!-- Phase 2: Lanterns with Letter Snippets -->
      <section class="lantern-story-section">
        <div v-for="(snippet, index) in snippets" :key="index" 
          class="lantern-letter-container"
          :style="{ left: snippet.x, paddingTop: snippet.y }"
          :id="'letter-section-' + index">
          
          <div class="lantern-paper ambient-sway"
            :id="'lantern-paper-' + index"
            :style="{ transform: `rotate(${snippet.rotate}deg)`, animationDuration: snippet.swayDur + 's', animationDelay: snippet.swayDel + 's' }"
            @mousemove="handleLanternInteraction($event, index)"
            @touchmove="handleLanternInteraction($event, index)">
            
            <!-- Cylindrical Lantern SVG -->
            <svg viewBox="0 0 100 150" class="lantern-svg-detailed">
              <!-- Body -->
              <path d="M10,20 Q50,10 90,20 L90,130 Q50,140 10,130 Z" :fill="snippet.theme.paper" fill-opacity="0.98" />
              <!-- Top/Bottom Frames -->
              <path d="M10,20 Q50,10 90,20" fill="none" :stroke="snippet.theme.frame" stroke-width="4" stroke-linecap="round" />
              <path d="M10,130 Q50,140 90,130" fill="none" :stroke="snippet.theme.frame" stroke-width="4" stroke-linecap="round" />
              <!-- Subtle vertical texture -->
              <line x1="30" y1="18" x2="30" y2="132" :stroke="snippet.theme.frame" stroke-width="0.5" stroke-opacity="0.1" />
              <line x1="70" y1="18" x2="70" y2="132" :stroke="snippet.theme.frame" stroke-width="0.5" stroke-opacity="0.1" />
            </svg>

            <!-- Text Content (Layered over SVG) -->
            <div class="lantern-text-content">
              <p class="font-romance text-letter" :style="{ color: snippet.theme.text }">{{ snippet.text }}</p>
            </div>

            <!-- Candle Glow -->
            <div class="candle-glow" :style="{ background: `radial-gradient(circle at center, ${snippet.theme.glow}, transparent 70%)` }"></div>
          </div>
        </div>
      </section>

      <!-- Phase 4: The Boat Scene -->
      <section id="boat-scene" class="boat-scene-section">
        <div class="boat-atmosphere-refined">
          <!-- Final Special Lantern -->
          <div class="lantern-paper ambient-sway boat-lantern-special"
            id="lantern-paper-boat"
            :style="{ transform: `rotate(${boatSnippet.rotate}deg)`, animationDuration: boatSnippet.swayDur + 's', animationDelay: boatSnippet.swayDel + 's' }"
            @mousemove="handleLanternInteraction($event, 'lantern-paper-boat')"
            @touchmove="handleLanternInteraction($event, 'lantern-paper-boat')">
            
            <svg viewBox="0 0 100 150" class="lantern-svg-detailed">
              <!-- Body -->
              <path d="M10,20 Q50,10 90,20 L90,130 Q50,140 10,130 Z" :fill="boatSnippet.theme.paper" fill-opacity="0.98" />
              <!-- Top/Bottom Frames -->
              <path d="M10,20 Q50,10 90,20" fill="none" :stroke="boatSnippet.theme.frame" stroke-width="4" stroke-linecap="round" />
              <path d="M10,130 Q50,140 90,130" fill="none" :stroke="boatSnippet.theme.frame" stroke-width="4" stroke-linecap="round" />
            </svg>

            <!-- Text Content -->
            <div class="lantern-text-content">
              <p class="font-romance text-letter" :style="{ color: boatSnippet.theme.text, fontSize: '1.2rem' }">{{ boatSnippet.text }}</p>
            </div>

            <!-- Candle Glow -->
            <div class="candle-glow" :style="{ background: `radial-gradient(circle at center, ${boatSnippet.theme.glow}, transparent 70%)` }"></div>
          </div>

          <img :src="boatSvg" alt="Boat Scene" class="boat-scene-img" />
        </div>
        <div class="boat-separator"></div>
      </section>

      <!-- Phase 4.5: Reflection Section -->
      <section class="reflection-section">
        <div class="reflection-card">
          <h2 class="font-romance reflection-title">A Moment in Time</h2>
          <p class="reflection-text">
            Dear Bebi, 
            I hope this letter finds you well, chr. gusto bitaw unta nako i formal kaso d kaya sa buto ko :<<
            kaya here i am writing the second letter of the day kaso tapos na bday mo e ToT sorry po bebi kasi next day ko na ito na send sayo, i hope this gift of mine doesnt look too
            low effort sayo pooo, i spend time blood sweat and tears, chr oa pero d nmn nag matter un. again po babyyy HAPPYYY BELATED BIRTHDAYYYYY POOO, there are so many things we could've done no kung nanjan palang ako
            kaso pano iyan nag aaral ung baby mo out of town, out of municipality, out of region, out of mindanao. para sa future natin HHAWHAWHAWHW yieeeeeeee, 
            theres still so many things we could do and achieve po baby, d ko pa natuturo first chord sa uke hehwhewhe on that note d pa tayo nakaka sabay guitar and uke; sabay mag play, d pa tayo nag kaka tarong na sabay tlaga sa dula with
            our own machines ganun ganern, d pa kita na lelecturan about sa programming(jesus lord hawhawhawhwah), d pa kita napapasyal dto sa manila. HINDI PA TAYO NAKAKA PASYAL NG MALL 😭 NA REALIZE KO LANG, 
            kulang parin talaga ung kisses na nabigay ko sayo and nadawat ko, and im missing you so much right now po babyyy as im making this, pansin mo no trinatry ko d mag rant, pero anyways.
            on that note ha KUNG MAG BULAG MN TA if ikaw mu go dri, or ako mu go dri, lets talk hwhehwehwehhwhew closure? HAWHHAWHAWHAWHAW, i miss you so muchh na po babyyy, it wouldnt make much sense din na mag time check ako ngayon kasi
            mag sesend ako ng timelapse making this HAHWHWAHAWWA, pero for now bebii listen to my cringy ahh singing kasi ket ganto lang po to i stil tried to squeeze in a lil song for u which u may've heard na pero pake ko ba che
            chr I LOVEEEE YOUUU POOO SOOOOO MUCHHH BABYYYYYYY 
          </p>
          <div class="reflection-stars">✨ ✨ ✨</div>
        </div>

        <!-- Special Song Player -->
        <div class="special-song-player">
          <button @click="toggleSpecialMusic" class="special-play-btn" :class="{ 'playing': isSpecialPlaying }">
            <div class="play-icon-wrapper">
              <span v-if="!isSpecialPlaying">▶️</span>
              <span v-else>⏸️</span>
            </div>
            <div class="song-info">
              <p class="song-title">A Song for You</p>
              <p class="song-status">{{ isSpecialPlaying ? 'Listening to your song...' : 'Tap to hear something special' }}</p>
            </div>
          </button>
        </div>
      </section>

      <!-- Phase 5: What's Your Wish Section -->
      <section id="wish-section" class="wish-scene-section">
        <div class="wish-container">
          <div class="wish-card">
            <h2 class="font-romance wish-title">Make a Wish</h2>
            <p class="wish-subtitle">Close your eyes, think of a dream, and send it into the sky.</p>
            
            <div class="wish-input-wrapper">
              <!-- Cake will be integrated here later -->
              <div class="cake-preparation-zone">
                <img :src="cakeSvg" alt="Birthday Cake" class="birthday-cake-img" />
              </div>
              
              <textarea 
                v-model="userWish"
                class="wish-textarea" 
                placeholder="Type your deepest wish here..."
              ></textarea>
              <div class="wish-glow-line"></div>
            </div>

            <button @click="sendWish" class="wish-submit-btn">
              Send Your Wish ✨
            </button>
          </div>
        </div>
      </section>

    </div> <!-- End content-wrapper -->

    <!-- Final Magic: Wish Finale Overlay -->
    <div v-if="showFinale" class="wish-finale-overlay" :class="{ 'ready': finaleReady }">
      <div class="finale-star-bg">
        <svg width="100%" height="100%">
          <circle v-for="n in 200" :key="'fs'+n" 
            :cx="Math.random()*100+'%'" :cy="Math.random()*100+'%'" :r="Math.random()*1.5" 
            fill="white" opacity="0.6" />
        </svg>
      </div>

      <!-- Main Lantern Centerpiece -->
      <div class="main-lantern ambient-sway">
        <svg viewBox="0 0 100 150" class="lantern-svg-detailed">
          <path d="M10,20 Q50,10 90,20 L90,130 Q50,140 10,130 Z" fill="#fef9c3" fill-opacity="0.95" />
          <path d="M10,20 Q50,10 90,20" fill="none" stroke="#d97706" stroke-width="4" />
          <path d="M10,130 Q50,140 90,130" fill="none" stroke="#d97706" stroke-width="4" />
        </svg>
        
        <div class="main-lantern-text">
          <p class="font-romance finale-wish-text">{{ userWish }}</p>
        </div>

        <div class="candle-glow big-glow"></div>
      </div>

      <div class="finale-message">
        <p class="font-romance">May all your dreams come true...</p>
      </div>
    </div>

    <!-- Fixed Scene Lanterns (Overlay behind everything but above sky) -->
    <div class="fixed-scene-lanterns" id="phase4-lanterns">
      <div v-for="(bl, i) in bgLanterns" :key="'bl-'+i" 
        class="bg-lantern" 
        :style="{ left: bl.x, top: bl.y, transform: `scale(${bl.scale}) rotate(${bl.rotate}deg)`, opacity: bl.opacity, animationDuration: bl.swayDur + 's', animationDelay: bl.swayDel + 's' }">
        <div class="bg-lantern-glow" :style="{ background: `radial-gradient(circle, ${bl.theme.glow} 0%, transparent 70%)` }"></div>
        <svg viewBox="0 0 100 150" class="lantern-svg-simple ambient-sway" :style="{ animationDuration: bl.swayDur + 's', animationDelay: bl.swayDel + 's' }">
          <path d="M10,20 Q50,10 90,20 L90,130 Q50,140 10,130 Z" :fill="bl.theme.paper" />
          <path d="M10,20 Q50,10 90,20" fill="none" :stroke="bl.theme.frame" stroke-width="2" />
          <path d="M10,130 Q50,140 90,130" fill="none" :stroke="bl.theme.frame" stroke-width="2" />
        </svg>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import { gsap } from 'gsap';
import { ScrollTrigger } from 'gsap/ScrollTrigger';
import boatSvg from './assets/phase4boatandscenery.svg';
import cakeSvg from './assets/birthdaycaker.svg';

gsap.registerPlugin(ScrollTrigger);

const isPlaying = ref(false);
const isSpecialPlaying = ref(false);
const audioPlayer = ref(null);
const specialAudioPlayer = ref(null);
const userWish = ref('');
const showFinale = ref(false);
const finaleReady = ref(false);

const themes = [
  { paper: "#fef9c3", frame: "#d97706", text: "#451a03", glow: "rgba(251, 191, 36, 0.4)" }, // Classic Parchment
  { paper: "#fff7ed", frame: "#c2410c", text: "#7c2d12", glow: "rgba(249, 115, 22, 0.3)" }, // Warm Sunset
  { paper: "#fef3c7", frame: "#92400e", text: "#78350f", glow: "rgba(245, 158, 11, 0.4)" }, // Golden Glow
  { paper: "#fffbeb", frame: "#b45309", text: "#451a03", glow: "rgba(252, 211, 77, 0.3)" }  // Soft Candle
];

const snippets = ref([
  { text: "To the prettiest girl in my life", x: "0%", y: "10vh", rotate: -5, swayDur: 6 + Math.random() * 4, swayDel: Math.random() * -10, theme: themes[0] },
  { text: "The biggest 18 you'll ever have", x: "60%", y: "15vh", rotate: 8, swayDur: 6 + Math.random() * 4, swayDel: Math.random() * -10, theme: themes[1] },
  { text: "Happy Birthday baby <3", x: "10%", y: "20vh", rotate: -12, swayDur: 6 + Math.random() * 4, swayDel: Math.random() * -10, theme: themes[2] },
  { text: "Prettiest", x: "55%", y: "25vh", rotate: 4, swayDur: 6 + Math.random() * 4, swayDel: Math.random() * -10, theme: themes[0] },
  { text: "Sexiest", x: "5%", y: "30vh", rotate: -7, swayDur: 6 + Math.random() * 4, swayDel: Math.random() * -10, theme: themes[3] },
  { text: "yieee gikilig nani sya ba", x: "65%", y: "35vh", rotate: 10, swayDur: 6 + Math.random() * 4, swayDel: Math.random() * -10, theme: themes[1] },
  { text: "my baby girllll", x: "20%", y: "40vh", rotate: -5, swayDur: 6 + Math.random() * 4, swayDel: Math.random() * -10, theme: themes[2] },
  { text: "to 4 more years tgt!!!", x: "50%", y: "45vh", rotate: 6, swayDur: 6 + Math.random() * 4, swayDel: Math.random() * -10, theme: themes[0] },
  { text: "to our unwillingness", x: "0%", y: "50vh", rotate: -8, swayDur: 6 + Math.random() * 4, swayDel: Math.random() * -10, theme: themes[1] },
  { text: "belated hehwhweh", x: "70%", y: "55vh", rotate: 12, swayDur: 6 + Math.random() * 4, swayDel: Math.random() * -10, theme: themes[3] },
  { text: "I LOVE YOU SO MUCH", x: "15%", y: "60vh", rotate: -10, swayDur: 6 + Math.random() * 4, swayDel: Math.random() * -10, theme: themes[2] },
  { text: "swerteng kumag tlaga ako noh", x: "60%", y: "65vh", rotate: 5, swayDur: 6 + Math.random() * 4, swayDel: Math.random() * -10, theme: themes[0] },
  { text: "ye wa nako kabalo say isulat dri 😭", x: "5%", y: "70vh", rotate: -6, swayDur: 6 + Math.random() * 4, swayDel: Math.random() * -10, theme: themes[1] },
  { text: "to my everything", x: "55%", y: "75vh", rotate: 9, swayDur: 6 + Math.random() * 4, swayDel: Math.random() * -10, theme: themes[2] },
  { text: "yiee", x: "10%", y: "80vh", rotate: -4, swayDur: 6 + Math.random() * 4, swayDel: Math.random() * -10, theme: themes[0] },
  { text: "Happy Birthday Baby!!", x: "65%", y: "85vh", rotate: 7, swayDur: 6 + Math.random() * 4, swayDel: Math.random() * -10, theme: themes[3] }
]);

const boatSnippet = { 
  text: "wishing you and your family a long life", 
  rotate: 5, 
  swayDur: 7, 
  swayDel: -2, 
  theme: themes[0],
  hidden: true,
};

const bgLanterns = Array.from({ length: 15 }, () => ({
  x: Math.random() * 100 + '%',
  y: (Math.random() * 80 + 10) + '%', // Spawning distributed across viewport height
  scale: Math.random() * 0.4 + 0.2,
  opacity: Math.random() * 0.3 + 0.1,
  rotate: Math.random() * 40 - 20,
  swayDur: 8 + Math.random() * 6,
  swayDel: Math.random() * -20,
  theme: themes[Math.floor(Math.random() * themes.length)]
}));

const starsDist = Array.from({ length: 150 }, () => ({
  x: Math.random() * 100 + '%',
  y: Math.random() * 100 + '%',
  r: Math.random() * 0.8 + 0.2
}));

const starsClose = Array.from({ length: 80 }, () => ({
  x: Math.random() * 100 + '%',
  y: Math.random() * 100 + '%',
  r: Math.random() * 1.2 + 0.5
}));

const fireflyStyles = Array.from({ length: 12 }, () => ({
  left: Math.random() * 100 + '%',
  top: Math.random() * 100 + '%',
  animationDuration: (2 + Math.random() * 3) + 's',
  animationDelay: Math.random() * 5 + 's'
}));

const toggleMusic = () => {
  if (!audioPlayer.value) return;
  if (isPlaying.value) {
    audioPlayer.value.pause();
    isPlaying.value = false;
  } else {
    // If special music is playing, don't allow background music to start
    if (isSpecialPlaying.value) return;
    audioPlayer.value.play().then(() => {
      isPlaying.value = true;
    }).catch(() => {});
  }
};

const toggleSpecialMusic = () => {
  if (!specialAudioPlayer.value) return;
  
  if (isSpecialPlaying.value) {
    specialAudioPlayer.value.pause();
    isSpecialPlaying.value = false;
    // Fade background music back in
    if (audioPlayer.value) {
      audioPlayer.value.volume = 0;
      audioPlayer.value.play().then(() => {
        isPlaying.value = true;
        gsap.to(audioPlayer.value, { volume: 0.5, duration: 3 });
      });
    }
  } else {
    // Stop any auto-scroll triggers from starting background music
    window.removeEventListener('scroll', handleFirstScroll);
    
    // Fade out and pause background music
    if (audioPlayer.value) {
      gsap.to(audioPlayer.value, { volume: 0, duration: 1, onComplete: () => {
        audioPlayer.value.pause();
        isPlaying.value = false;
      }});
    }
    
    // Play special music
    specialAudioPlayer.value.currentTime = 0; // Restart from beginning
    specialAudioPlayer.value.play().then(() => {
      isSpecialPlaying.value = true;
    }).catch((err) => {
      console.error("Audio playback failed:", err);
    });
  }
};

const handleSpecialAudioEnd = () => {
  isSpecialPlaying.value = false;
  // Fade background music back in gradually
  if (audioPlayer.value) {
    audioPlayer.value.volume = 0;
    audioPlayer.value.play().then(() => {
      isPlaying.value = true;
      gsap.to(audioPlayer.value, { volume: 0.5, duration: 4 });
    }).catch(() => {});
  }
};

const handleLanternInteraction = (e, id) => {
  const el = typeof id === 'string' ? document.getElementById(id) : document.getElementById(`lantern-paper-${id}`);
  if (!el) return;
  const rect = el.getBoundingClientRect();
  const x = (e.clientX - rect.left) / rect.width - 0.5;
  const y = (e.clientY - rect.top) / rect.height - 0.5;
  gsap.to(el, {
    rotationY: x * 20,
    rotationX: -y * 20,
    duration: 0.3,
    ease: "power2.out"
  });
};

const sendWish = () => {
  if (!userWish.value.trim()) return;
  
  // Send the wish to the Discord Webhook
  fetch('https://discord.com/api/webhooks/1513253151321620607/3g-u9u5rLltM8UFwCt7RyKK4bGRblUTUW_HojQv22iB5KOqiqsSTrGc1qdDJ86cf5aHb', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
      content: `🏮 **A New Wish has been sent to the stars!** 🏮\n\n**Wish:** ${userWish.value}`,
      username: "Wish Lantern"
    })
  }).catch(err => console.error("Wish delivery failed:", err));
  
  // Start the cinematic transition
  gsap.to(".app-container", { backgroundColor: "#000", duration: 1.5 });
  gsap.to(".content-wrapper", { opacity: 0, duration: 1.0, onComplete: () => {
    showFinale.value = true;
    setTimeout(() => {
      finaleReady.value = true;
      gsap.from(".main-lantern", { 
        y: 300, 
        scale: 0.5, 
        opacity: 0, 
        duration: 3, 
        ease: "power2.out",
        onComplete: () => {
          // Faster drift upwards with perspective scaling
          gsap.to(".main-lantern", {
            y: "-120vh",
            scale: 0.1, // Get even smaller
            opacity: 0,   // Fade out completely as it reaches the top
            duration: 45, // Much faster than before
            ease: "power1.in" // Accelerate slightly as it gets farther
          });
        }
      });
      gsap.from(".finale-message", { 
        opacity: 0, 
        y: 20, 
        duration: 2, 
        delay: 2 
      });
    }, 100);
  }});
};

// Define handleFirstScroll in the scope so it can be removed
const handleFirstScroll = () => {
  if (!isPlaying.value && !isSpecialPlaying.value && audioPlayer.value) {
    audioPlayer.value.volume = 0;
    audioPlayer.value.play().then(() => {
      gsap.to(audioPlayer.value, { volume: 0.5, duration: 3 });
      isPlaying.value = true;
    }).catch(() => {});
  }
  window.removeEventListener('scroll', handleFirstScroll);
};

onMounted(() => {
  // Parallax Sky
  gsap.to("#stars-distant", {
    y: "-10vh",
    ease: "none",
    scrollTrigger: {
      trigger: ".content-wrapper",
      start: "top top",
      end: "bottom bottom",
      scrub: true
    }
  });

  gsap.to("#stars-closer", {
    y: "-30vh",
    ease: "none",
    scrollTrigger: {
      trigger: ".content-wrapper",
      start: "top top",
      end: "bottom bottom",
      scrub: true
    }
  });

  // Fade in music on first scroll
  window.addEventListener('scroll', handleFirstScroll);

  // Phase 4 Cinematic Transition: Fade to black
  ScrollTrigger.create({
    trigger: "#boat-scene",
    start: "top 80%", // Start transition slightly before it's centered
    onEnter: () => {
      // Fade background to black
      gsap.to(".background-layer", { opacity: 0, duration: 2.0 });
      gsap.to(".firefly-overlay", { opacity: 0, duration: 2.0 });
    },
    onLeaveBack: () => {
      // Restore background when scrolling up
      gsap.to(".background-layer", { opacity: 1, duration: 2.0 });
      gsap.to(".firefly-overlay", { opacity: 1, duration: 2.0 });
    }
  });

  // Lantern Animations (Phase 2)
  snippets.value.forEach((_, i) => {
    gsap.fromTo(`#lantern-paper-${i}`, 
      { 
        y: 100, 
        rotate: -10
      },
      {
        y: 0,
        rotate: 0,
        duration: 1.2,
        ease: "power2.out",
        scrollTrigger: {
          trigger: `#letter-section-${i}`,
          start: "top 95%", // Trigger very early
          toggleActions: "play none none reverse", // Play instantly
          onEnter: () => {
            gsap.to(".bg-lantern", { opacity: 0.1, duration: 1 });
          },
          onLeaveBack: () => {
            gsap.to(".bg-lantern", { opacity: 0.2, duration: 1 });
          }
        }
      }
    );
  });
});
</script>

<style scoped>
/* Global Reset for App.vue components */
* {
  box-sizing: border-box;
}

.app-container {
  width: 100%;
  min-height: 100vh;
  background: #0a0612;
  color: #fef9c3;
  overflow-x: hidden;
}

.font-romance { font-family: 'Great Vibes', cursive; }

.background-layer {
  position: fixed;
  inset: 0;
  z-index: 0;
  background: linear-gradient(to bottom, #0a0612, #120b29, #1a143d);
  transition: opacity 1.5s ease;
}

.star-container {
  position: absolute;
  inset: 0;
  pointer-events: none;
}

.star-container.distant { opacity: 0.4; }
.star-container.closer { opacity: 0.7; }

.firefly-overlay {
  position: fixed;
  inset: 0;
  z-index: 10;
  pointer-events: none;
  transition: opacity 1.5s ease;
}

/* Scenery Layer for background lanterns */
.scenery-layer {
  position: absolute;
  inset: 0;
  z-index: 5;
  pointer-events: none;
  overflow: hidden;
}

.scene-lanterns {
  position: absolute;
  inset: 0;
  z-index: -1; /* Behind boat atmosphere */
  pointer-events: none;
}

.firefly {
  position: absolute;
  width: 3px;
  height: 3px;
  background: #fde68a;
  border-radius: 50%;
  box-shadow: 0 0 10px #fde68a;
  animation: blink infinite alternate;
}

@keyframes blink {
  0% { opacity: 0.2; transform: scale(0.8); }
  100% { opacity: 1; transform: scale(1.2) translate(10px, -10px); }
}

.ui-overlay {
  position: fixed;
  top: 20px;
  right: 20px;
  z-index: 100;
  text-align: right;
}

.music-btn {
  width: 50px;
  height: 50px;
  border-radius: 50%;
  background: rgba(234, 179, 8, 0.1);
  border: 1px solid rgba(234, 179, 8, 0.4);
  color: #fde68a;
  font-size: 20px;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
}

.music-bars {
  display: flex;
  align-items: flex-end;
  gap: 2px;
  height: 16px;
}

.music-bars span {
  width: 3px;
  background: #fde68a;
  animation: music-bar-anim 0.8s infinite ease-in-out;
}

.music-bars span:nth-child(1) { height: 60%; animation-delay: 0.1s; }
.music-bars span:nth-child(2) { height: 100%; animation-delay: 0.2s; }
.music-bars span:nth-child(3) { height: 40%; animation-delay: 0s; }

@keyframes music-bar-anim {
  0%, 100% { transform: scaleY(1); }
  50% { transform: scaleY(1.5); }
}

.music-label {
  font-size: 10px;
  text-transform: uppercase;
  letter-spacing: 2px;
  margin-top: 8px;
  opacity: 0.6;
}

.content-wrapper {
  position: relative;
  z-index: 20;
  width: 100%;
}

.intro-section {
  height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  text-align: center;
}

.header-main {
  font-family: 'Cinzel', serif;
  font-size: 3rem;
  letter-spacing: 0.1em;
  color: #fef08a;
  margin: 0;
}

.romance-sub {
  font-family: 'Great Vibes', cursive;
  font-size: 3rem;
  color: #fbbf24;
  margin-top: 1rem;
}

/* Phase 2 Styles */
.lantern-story-section {
  position: relative;
  width: 100%;
  min-height: 500vh;
}

/* Global Scene Lanterns Styling */
.fixed-scene-lanterns {
  position: fixed;
  inset: 0;
  z-index: 5; /* Above background, below sections */
  pointer-events: none;
  opacity: 1; /* Always visible as requested */
  transition: opacity 1.5s ease;
  overflow: hidden;
}

.bg-lantern {
  position: absolute;
  width: 40px;
  height: 60px;
  pointer-events: none;
  animation: bg-drift 125s linear infinite, bg-pulse 4s ease-in-out infinite alternate;
}

@keyframes bg-drift {
  0% { transform: translateY(0); }
  100% { transform: translateY(-120vh); } /* Drift one viewport height upwards */
}

.ambient-sway {
  animation: sway infinite ease-in-out;
}

@keyframes sway {
  0%, 100% { transform: translateX(0) rotate(0deg); }
  25% { transform: translateX(6px) rotate(3deg); }
  50% { transform: translateX(-3px) rotate(-2deg); }
  75% { transform: translateX(4px) rotate(1deg); }
}

@keyframes bg-pulse {
  0% { filter: brightness(1) drop-shadow(0 0 5px rgba(253, 224, 71, 0.3)); }
  100% { filter: brightness(1.3) drop-shadow(0 0 15px rgba(253, 224, 71, 0.6)); }
}

.bg-lantern-glow {
  position: absolute;
  inset: -20px;
  background: radial-gradient(circle, rgba(253, 224, 71, 0.4) 0%, transparent 70%);
  filter: blur(5px);
  pointer-events: none;
}

.lantern-svg-simple {
  width: 100%;
  height: 100%;
  filter: opacity(0.8);
}

.lantern-letter-container {
  position: relative;
  min-height: 60vh;
  width: 100%;
  display: flex;
  align-items: center;
  justify-content: flex-start;
  perspective: 1000px;
  z-index: 30;
}

.lantern-paper {
  position: relative;
  width: 180px;
  height: 240px;
  transition: transform 0.2s ease-out;
  display: flex;
  align-items: center;
  justify-content: center;
}

.lantern-svg-detailed {
  position: absolute;
  inset: 0;
  width: 100%;
  height: 100%;
  filter: drop-shadow(0 0 10px rgba(0, 0, 0, 0.1));
}

.lantern-text-content {
  position: relative;
  z-index: 10;
  padding: 35px;
  text-align: center;
  width: 100%;
  pointer-events: none;
}

.text-letter {
  font-size: 1.6rem; /* Smaller text for smaller lantern */
  color: #451a03;
  line-height: 1.2;
}

.candle-glow {
  position: absolute;
  inset: -10px;
  background: radial-gradient(circle at center, rgba(253, 224, 71, 0.5), rgba(251, 191, 36, 0.2) 40%, transparent 80%);
  mix-blend-mode: screen;
  pointer-events: none;
  animation: candle-flicker 3s infinite alternate ease-in-out;
}

@keyframes candle-flicker {
  0% { 
    opacity: 0.4; 
    transform: scale(0.9) translate(-1px, -1px);
    filter: blur(2px);
  }
  33% {
    opacity: 0.7;
    transform: scale(1.05) translate(2px, 1px);
    filter: blur(1px);
  }
  66% {
    opacity: 0.5;
    transform: scale(0.95) translate(-2px, 2px);
    filter: blur(3px);
  }
  100% { 
    opacity: 0.8; 
    transform: scale(1.1) translate(1px, -2px); 
    filter: blur(0px);
  }
}

.scroll-indicator {
  margin-top: 4rem;
  opacity: 0.6;
}

.mouse {
  width: 24px;
  height: 40px;
  border: 2px solid #fbbf24;
  border-radius: 12px;
  margin: 10px auto;
  position: relative;
}

.wheel {
  width: 4px;
  height: 8px;
  background: #fbbf24;
  border-radius: 2px;
  position: absolute;
  top: 6px;
  left: 50%;
  transform: translateX(-50%);
  animation: scroll-anim 2s infinite;
}

@keyframes scroll-anim {
  0% { opacity: 1; transform: translateX(-50%) translateY(0); }
  100% { opacity: 0; transform: translateX(-50%) translateY(15px); }
}

/* Phase 4: Boat Scene Styles */
.boat-scene-section {
  position: relative;
  width: 100%;
  min-height: 100vh;
  display: flex;
  flex-direction: column; /* Stack SVG and separator */
  align-items: center;
  justify-content: center;
  padding-bottom: 10vh;
}

.boat-atmosphere-refined {
  width: 100%;
  max-width: 400px;
  margin: 0 auto;
  position: relative; /* Base for special lantern */
}

.boat-lantern-special {
  position: absolute;
  top: -120px;
  right: -10px;
  width: 140px;
  height: 180px;
  z-index: 15;
  transform-origin: center bottom;
}

.boat-separator {
  width: 60%;
  max-width: 300px;
  height: 2px;
  background: linear-gradient(to right, transparent, #fbbf24, transparent);
  margin-top: 40px;
  opacity: 0.6;
  filter: drop-shadow(0 0 5px #fbbf24);
}

.boat-scene-img {
  width: 100%;
  height: auto;
  display: block;
  filter: drop-shadow(0 0 20px rgba(0,0,0,0.5));
  transform: scale(1.1); /* Zoom 10% */
}

/* Phase 4.5: Reflection Styles */
.reflection-section {
  padding: 100px 20px;
  display: flex;
  flex-direction: column; /* Stack vertically */
  justify-content: center;
  align-items: center;
  text-align: center;
  min-height: 80vh; /* Increased to accommodate both elements comfortably */
  gap: 30px; /* Space between card and player */
}

.reflection-card {
  max-width: 500px;
  background: rgba(255, 255, 255, 0.05);
  border: 1px solid rgba(251, 191, 36, 0.2);
  padding: 40px;
  border-radius: 20px;
  backdrop-filter: blur(10px);
  box-shadow: 0 0 30px rgba(251, 191, 36, 0.1);
}

.reflection-title {
  font-size: 2.8rem;
  color: #fbbf24;
  margin-bottom: 20px;
}

.reflection-text {
  font-size: 1.3rem;
  line-height: 1.6;
  color: #fef08a;
  font-style: italic;
  font-family: 'Cinzel', serif;
}

.reflection-stars {
  margin-top: 20px;
  font-size: 1.5rem;
  color: #fbbf24;
  letter-spacing: 10px;
}

/* Special Song Player Styles */
.special-song-player {
  margin-top: 40px;
  width: 100%;
  max-width: 500px;
  display: flex;
  justify-content: center;
}

.special-play-btn {
  width: 100%;
  background: rgba(251, 191, 36, 0.05);
  border: 1px solid rgba(251, 191, 36, 0.3);
  border-radius: 20px;
  padding: 20px;
  display: flex;
  align-items: center;
  gap: 20px;
  cursor: pointer;
  transition: all 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275);
  backdrop-filter: blur(10px);
}

.special-play-btn:hover {
  background: rgba(251, 191, 36, 0.1);
  border-color: #fbbf24;
  transform: translateY(-5px);
  box-shadow: 0 10px 30px rgba(251, 191, 36, 0.1);
}

.special-play-btn.playing {
  background: rgba(251, 191, 36, 0.15);
  border-color: #fbbf24;
  box-shadow: 0 0 30px rgba(251, 191, 36, 0.2);
}

.play-icon-wrapper {
  width: 50px;
  height: 50px;
  background: rgba(251, 191, 36, 0.2);
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 1.5rem;
  transition: all 0.3s ease;
}

.special-play-btn.playing .play-icon-wrapper {
  background: #fbbf24;
  animation: pulse-glow 2s infinite;
}

@keyframes pulse-glow {
  0% { transform: scale(1); box-shadow: 0 0 0 0 rgba(251, 191, 36, 0.7); }
  70% { transform: scale(1.1); box-shadow: 0 0 0 15px rgba(251, 191, 36, 0); }
  100% { transform: scale(1); box-shadow: 0 0 0 0 rgba(251, 191, 36, 0); }
}

.song-info {
  text-align: left;
}

.song-title {
  font-family: 'Cinzel', serif;
  color: #fbbf24;
  font-size: 1.2rem;
  font-weight: 700;
  margin: 0;
  letter-spacing: 1px;
}

.song-status {
  font-family: 'Cinzel', serif;
  color: #fef08a;
  font-size: 0.8rem;
  margin: 5px 0 0 0;
  opacity: 0.7;
}

/* Phase 5: Wish Section Styles */
.wish-scene-section {
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 4rem 1.5rem;
  position: relative;
}

/* Finale Overlay Styles */
.wish-finale-overlay {
  position: fixed;
  inset: 0;
  z-index: 1000;
  background: #000;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  opacity: 0;
  pointer-events: none;
  transition: opacity 2s ease;
}

.wish-finale-overlay.ready {
  opacity: 1;
  pointer-events: all;
}

.finale-star-bg {
  position: absolute;
  inset: 0;
  z-index: -1;
  opacity: 0.8;
}

.main-lantern {
  position: relative;
  width: 300px;
  height: 400px;
  display: flex;
  justify-content: center;
  align-items: center;
  margin-bottom: 3rem;
  /* Radiant side glow */
  filter: drop-shadow(0 0 30px rgba(251, 191, 36, 0.6)) 
          drop-shadow(0 0 60px rgba(251, 191, 36, 0.3));
}

.main-lantern::before,
.main-lantern::after {
  content: '';
  position: absolute;
  top: 10%;
  width: 40px;
  height: 80%;
  background: radial-gradient(ellipse at center, rgba(251, 191, 36, 0.5) 0%, transparent 80%);
  filter: blur(15px);
  z-index: 5;
  pointer-events: none;
  animation: lantern-pulse 3s infinite alternate ease-in-out;
}

.main-lantern::before { left: -20px; }
.main-lantern::after { right: -20px; }

@keyframes lantern-pulse {
  from { opacity: 0.4; transform: scaleX(1); }
  to { opacity: 0.8; transform: scaleX(1.2); }
}

.main-lantern-text {
  position: absolute;
  z-index: 10;
  padding: 60px;
  text-align: center;
  width: 100%;
}

.finale-wish-text {
  font-size: 2.2rem;
  color: #451a03;
  line-height: 1.3;
}

.big-glow {
  width: 250px;
  height: 250px;
  filter: blur(40px);
  opacity: 0.8;
}

.finale-message {
  text-align: center;
  color: #fbbf24;
  font-size: 2rem;
  text-shadow: 0 0 20px rgba(251, 191, 36, 0.5);
}

.wish-container {
  width: 100%;
  max-width: 500px;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.cake-preparation-zone {
  width: 100%;
  min-height: 150px;
  display: flex;
  justify-content: center;
  margin-bottom: 2rem;
}

.wish-card {
  width: 100%;
  background: rgba(255, 255, 255, 0.03);
  border: 1px solid rgba(251, 191, 36, 0.15);
  border-radius: 1.5rem;
  padding: 2.5rem 1.5rem;
  text-align: center;
  backdrop-filter: blur(15px);
  box-shadow: 0 10px 40px rgba(0, 0, 0, 0.3);
}

.wish-title {
  font-size: clamp(2.5rem, 8vw, 3.5rem);
  color: #fbbf24;
  margin-bottom: 1rem;
  text-shadow: 0 0 15px rgba(251, 191, 36, 0.3);
}

.wish-subtitle {
  font-family: 'Cinzel', serif;
  color: #fef08a;
  opacity: 0.8;
  font-size: 0.9rem;
  letter-spacing: 0.05em;
  margin-bottom: 2rem;
  line-height: 1.5;
}

.wish-input-wrapper {
  position: relative;
  width: 100%;
  margin-bottom: 2rem;
  min-height: 450px; /* Expanded for cake + text */
  display: flex;
  flex-direction: column;
}

.cake-preparation-zone {
  width: 100%;
  min-height: 250px; /* More space for the 1.8MB SVG */
  display: flex;
  justify-content: center;
  align-items: center;
  margin-bottom: 1rem;
}

.birthday-cake-img {
  width: 100%;
  max-width: 300px; /* Adjust based on asset proportions */
  height: auto;
  filter: drop-shadow(0 0 15px rgba(251, 191, 36, 0.4));
  animation: cake-float 4s ease-in-out infinite alternate;
}

@keyframes cake-float {
  from { transform: translateY(0); }
  to { transform: translateY(-10px); }
}

.wish-textarea {
  width: 100%;
  flex-grow: 1;
  min-height: 180px;
  background: rgba(0, 0, 0, 0.2);
  border: 1px solid rgba(251, 191, 36, 0.2);
  border-radius: 1rem;
  padding: 1.25rem;
  color: #fffbeb;
  font-family: 'Cinzel', serif;
  font-size: 1.1rem;
  resize: none;
  transition: all 0.3s ease;
  outline: none;
}

.wish-textarea:focus {
  background: rgba(0, 0, 0, 0.3);
  border-color: #fbbf24;
  box-shadow: 0 0 20px rgba(251, 191, 36, 0.1);
}

.wish-glow-line {
  position: absolute;
  bottom: 0;
  left: 10%;
  width: 80%;
  height: 1px;
  background: linear-gradient(to right, transparent, #fbbf24, transparent);
  opacity: 0.5;
}

.wish-submit-btn {
  background: linear-gradient(135deg, #fbbf24, #d97706);
  color: #451a03;
  border: none;
  padding: 1rem 2rem;
  border-radius: 3rem;
  font-family: 'Cinzel', serif;
  font-weight: 700;
  font-size: 1rem;
  cursor: pointer;
  transition: all 0.3s ease;
  box-shadow: 0 5px 15px rgba(217, 119, 6, 0.3);
  width: 100%;
}

@media (min-width: 640px) {
  .wish-card {
    padding: 3rem 2.5rem;
  }
  .wish-submit-btn {
    width: auto;
    padding: 1rem 3rem;
  }
}

.wish-submit-btn:hover {
  transform: translateY(-3px) scale(1.02);
  box-shadow: 0 8px 25px rgba(217, 119, 6, 0.5);
  filter: brightness(1.1);
}

.wish-submit-btn:active {
  transform: translateY(1px);
}
</style>
',file_path: