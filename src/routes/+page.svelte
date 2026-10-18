<script lang="ts">
  import { onMount } from 'svelte';
  import { gsap } from 'gsap';
  import { language } from '$lib/stores/language';

  const shards = [
    'polygon(0% 0%, 28% 4%, 22% 28%, 0% 35%)',
    'polygon(28% 4%, 58% 0%, 52% 26%, 22% 28%)',
    'polygon(58% 0%, 100% 6%, 100% 32%, 52% 26%)',
    'polygon(0% 35%, 22% 28%, 36% 68%, 8% 100%)',
    'polygon(22% 28%, 52% 26%, 48% 68%, 36% 68%)',
    'polygon(52% 26%, 100% 32%, 100% 100%, 48% 68%)',
    'polygon(8% 100%, 36% 68%, 48% 68%, 100% 100%)'
  ];

  const projects = [
    {
      number: '01',
      title: 'Company Profile',
      category: 'Front-End',
      media: '/videos/company-profile.webm',
      description: {
        id: 'Website company profile.',
        en: 'Company profile website.'
      }
    },
    {
      number: '02',
      title: 'Investment Potential',
      category: 'UI/UX',
      media: '/videos/invest-potential.webm',
      description: {
        id: 'Web Potensial Investasi Daerah.',
        en: 'Regional Investment Potential Website.'
      }
    },
    {
      number: '03',
      title: 'Link Archive',
      category: 'Full-Stack',
      media: '/videos/ungumark.webm',
      description: {
        id: 'Eksplorasi identitas visual brand fiksi.',
        en: 'Visual identity explorations.'
      }
    }
  ];

  const focusAreas = [
    {
      number: '01',
      title: {
        id: 'Front-End Development',
        en: 'Front-End Development'
      },
      description: {
        id: 'Membangun antarmuka website dengan perhatian pada struktur, responsivitas, dan interaksi.',
        en: 'Building website interfaces with attention to structure, responsiveness, and interaction.'
      }
    },
    {
      number: '02',
      title: {
        id: 'UI & Visual Design',
        en: 'UI & Visual Design'
      },
      description: {
        id: 'Menggabungkan pemahaman visual dengan implementasi antarmuka digital yang jelas dan konsisten.',
        en: 'Combining visual understanding with clear and consistent digital interface implementation.'
      }
    },
    {
      number: '03',
      title: {
        id: 'Web Applications',
        en: 'Web Applications'
      },
      description: {
        id: 'Mengeksplorasi pengembangan aplikasi web dan implementasi fitur sesuai kebutuhan proyek.',
        en: 'Exploring web application development and implementing features around project requirements.'
      }
    }
  ];

  let shardEls: HTMLDivElement[] = [];
  let hoveredShard: number | null = $state(null);

  let heroRoot: HTMLElement | undefined = $state();
  let marqueeContainer: HTMLDivElement | undefined = $state();
  let textWrapper: HTMLDivElement | undefined = $state();

  let vidRef: HTMLVideoElement | undefined = $state();
  let textRef1: HTMLHeadingElement | undefined = $state();
  let textRef2: HTMLHeadingElement | undefined = $state();
  let spanRef1: HTMLSpanElement | undefined = $state();
  let spanRef2: HTMLSpanElement | undefined = $state();
  let spanRef3: HTMLSpanElement | undefined = $state();
  let contRef: HTMLDivElement | undefined = $state();
  let mographSection: HTMLElement | undefined = $state();

  interface Particle {
    left: number;
    size: number;
    duration: number;
    delay: number;
    opacity: number;
  }

  let particles: Particle[] = $state([]);

  // =====================================================
  // HERO SHARDS INTERACTION
  // =====================================================

  const handleHeroMouseMove = (e: MouseEvent) => {
    if (!window.matchMedia('(hover: hover) and (pointer: fine)').matches) return;
    if (hoveredShard === null || !shardEls[hoveredShard]) return;

    const target = e.currentTarget as HTMLElement;
    const rect = target.getBoundingClientRect();

    const mouseX = e.clientX - rect.left;
    const mouseY = e.clientY - rect.top;

    const rotateX = (mouseY / Math.max(rect.height, 1)) * -12 + 6;
    const rotateY = (mouseX / Math.max(rect.width, 1)) * 12 - 6;

    gsap.to(shardEls[hoveredShard], {
      rotateX,
      rotateY,
      duration: 0.12,
      overwrite: 'auto'
    });
  };

  const handleShardEnter = (index: number) => {
    if (!window.matchMedia('(hover: hover) and (pointer: fine)').matches) return;

    hoveredShard = index;
    const shard = shardEls[index];
    if (!shard) return;

    gsap.to(shard, {
      scale: 1.06,
      filter: 'brightness(1.15) contrast(1.05)',
      duration: 0.3,
      overwrite: 'auto'
    });
  };

  const handleShardLeave = (index: number) => {
    hoveredShard = null;
    const shard = shardEls[index];
    if (!shard) return;

    gsap.to(shard, {
      scale: 1,
      rotateX: 0,
      rotateY: 0,
      filter: 'brightness(1) contrast(1)',
      duration: 0.3,
      overwrite: 'auto'
    });
  };

  // =====================================================
  // DESKTOP CINEMATIC REEL
  // =====================================================

  function updateMograph() {
    // Reel sinematik hanya berjalan pada desktop.
    if (!mographSection || window.innerWidth < 768) return;

    const rect = mographSection.getBoundingClientRect();
    if (!rect.height) return;

    const progress = Math.min(
      Math.max(-rect.top / rect.height, 0),
      0.4
    );

    if (vidRef) {
      vidRef.style.transform =
        `translateY(${20 * progress}px) scale(${6 - 7.5 * progress})`;
    }

    if (textRef1) {
      textRef1.style.transform =
        `translateX(-${1000 - 2000 * progress}px) scale(${2 + 2.5 * progress})`;
    }

    if (textRef2) {
      textRef2.style.transform =
        `translateX(${1000 - 2000 * progress}px) scale(${2 + 2.5 * progress})`;
    }

    [spanRef1, spanRef2, spanRef3].forEach((span) => {
      if (!span) return;

      span.style.transform =
        `translateX(${1100 - 2700 * progress}px)`;
    });

    if (contRef) {
      contRef.style.gap = `${400 * progress}px`;
      contRef.style.transform = `scaleX(${0.1 + 6 * progress})`;
    }
  }

  // =====================================================
  // GSAP
  // =====================================================

  onMount(() => {
    let disposed = false;
    let ctx: ReturnType<typeof gsap.context> | undefined;

    let refreshHandler: (() => void) | undefined;
    let scrollHandler: (() => void) | undefined;
    let resizeHandler: (() => void) | undefined;

    let ctaElement: HTMLElement | null = null;
    let ctaArrow: HTMLElement | null = null;

    let ctaEnterHandler: (() => void) | undefined;
    let ctaLeaveHandler: (() => void) | undefined;

    const refreshTimers: ReturnType<typeof setTimeout>[] = [];

    const initializeAnimations = async () => {
      const { ScrollTrigger } = await import('gsap/ScrollTrigger');

      if (disposed || !heroRoot) return;

      gsap.registerPlugin(ScrollTrigger);

      ScrollTrigger.config({
        ignoreMobileResize: true
      });

      ctx = gsap.context(() => {
        // HERO INTRO

        gsap.fromTo(
          '.hero-line',
          { yPercent: 110, opacity: 0 },
          {
            yPercent: 0,
            opacity: 1,
            duration: 1.1,
            stagger: 0.1,
            ease: 'power4.out'
          }
        );

        gsap.fromTo(
          '.hero-meta',
          { opacity: 0, y: 20 },
          {
            opacity: 1,
            y: 0,
            duration: 0.8,
            delay: 0.4,
            stagger: 0.05,
            ease: 'power3.out'
          }
        );

        gsap.fromTo(
          '.hero-visual',
          { opacity: 0, scale: 0.96, y: 40 },
          {
            opacity: 1,
            scale: 1,
            y: 0,
            duration: 1.1,
            delay: 0.25,
            ease: 'power4.out'
          }
        );

        // HERO PARALLAX

        gsap.to('.hero-word', {
          yPercent: -12,
          ease: 'none',
          scrollTrigger: {
            trigger: '.hero',
            start: 'top top',
            end: 'bottom top',
            scrub: 1
          }
        });

        gsap.to('.hero-visual', {
          yPercent: -10,
          rotate: -1,
          ease: 'none',
          scrollTrigger: {
            trigger: '.hero',
            start: 'top top',
            end: 'bottom top',
            scrub: 1
          }
        });

        // MARQUEE

        if (marqueeContainer && textWrapper) {
          const marqueeAnim = gsap.to(textWrapper, {
            xPercent: -50,
            ease: 'none',
            duration: 10,
            repeat: -1
          });

          ScrollTrigger.create({
            trigger: marqueeContainer,
            start: 'top bottom',
            end: 'bottom top',
            onUpdate: (self) => {
              gsap.to(marqueeAnim, {
                timeScale: self.direction,
                duration: 0.5,
                overwrite: 'auto'
              });
            }
          });

          gsap.to(marqueeContainer, {
            yPercent: -20,
            ease: 'none',
            scrollTrigger: {
              trigger: marqueeContainer,
              start: 'top bottom',
              end: 'bottom top',
              scrub: true
            }
          });
        }

        // PARTICLES: MOBILE USES FEWER PARTICLES.

        const newParticles: Particle[] = [];
        const isMobile = window.matchMedia('(max-width: 767px)').matches;
        const particleCount = isMobile ? 90 : 300;

        for (let i = 0; i < particleCount; i++) {
          newParticles.push({
            left: Math.random() * 100,
            size: Math.random() * 3 + 1,
            duration: Math.random() * 8 + 4,
            delay: Math.random() * -10,
            opacity: Math.random() * 0.5 + 0.2
          });
        }

        particles = newParticles;

        // THE CINEMATIC REEL IS DESKTOP-ONLY.

        if (mographSection && window.matchMedia('(min-width: 768px)').matches) {
          ScrollTrigger.create({
            trigger: mographSection,
            start: 'top bottom',
            end: 'bottom top',
            onUpdate: updateMograph
          });
        }

        // GENERAL REVEAL

        gsap.utils
          .toArray<HTMLElement>('.home-reveal, .mobile-project-card, .focus-card')
          .forEach((element) => {
            gsap.fromTo(
              element,
              { opacity: 0, y: 30 },
              {
                opacity: 1,
                y: 0,
                duration: 0.8,
                ease: 'power3.out',
                scrollTrigger: {
                  trigger: element,
                  start: 'top 88%',
                  once: true
                }
              }
            );
          });

        // MOBILE PROJECT VIDEOS PLAY ONLY NEAR THE VIEWPORT.

        if (window.matchMedia('(max-width: 767px)').matches) {
          gsap.utils
            .toArray<HTMLVideoElement>('.mobile-project-video')
            .forEach((video) => {
              ScrollTrigger.create({
                trigger: video,
                start: 'top 95%',
                end: 'bottom 5%',
                onEnter: () => video.play().catch(() => {}),
                onEnterBack: () => video.play().catch(() => {}),
                onLeave: () => video.pause(),
                onLeaveBack: () => video.pause()
              });
            });
        }

        // PROJECT CTA

        gsap.fromTo(
          '.project-cta',
          { opacity: 0, y: 25 },
          {
            opacity: 1,
            y: 0,
            duration: 0.8,
            ease: 'power3.out',
            scrollTrigger: {
              trigger: '.project-cta',
              start: 'top 85%',
              once: true
            }
          }
        );

        // CTA HOVER

        ctaElement =
          heroRoot?.querySelector<HTMLElement>('.project-cta') ?? null;

        ctaArrow =
          ctaElement?.querySelector<HTMLElement>('.cta-arrow') ?? null;

        if (ctaElement) {
          ctaEnterHandler = () => {
            if (!window.matchMedia('(hover: hover)').matches) return;

            gsap.to(ctaElement, {
              x: 8,
              duration: 0.35,
              ease: 'power3.out',
              overwrite: 'auto'
            });

            if (ctaArrow) {
              gsap.to(ctaArrow, {
                x: 5,
                y: -5,
                rotation: -8,
                duration: 0.35,
                ease: 'power3.out',
                overwrite: 'auto'
              });
            }
          };

          ctaLeaveHandler = () => {
            gsap.to(ctaElement, {
              x: 0,
              duration: 0.4,
              ease: 'power3.out',
              overwrite: 'auto'
            });

            if (ctaArrow) {
              gsap.to(ctaArrow, {
                x: 0,
                y: 0,
                rotation: 0,
                duration: 0.4,
                ease: 'power3.out',
                overwrite: 'auto'
              });
            }
          };

          ctaElement.addEventListener('mouseenter', ctaEnterHandler);
          ctaElement.addEventListener('mouseleave', ctaLeaveHandler);
        }

        // CLOSING

        gsap.fromTo(
          '.closing-content',
          { opacity: 0, y: 35 },
          {
            opacity: 1,
            y: 0,
            duration: 0.8,
            ease: 'power3.out',
            scrollTrigger: {
              trigger: '.closing-content',
              start: 'top 85%',
              once: true
            }
          }
        );

        gsap.to('.closing-word', {
          xPercent: -8,
          ease: 'none',
          scrollTrigger: {
            trigger: '.closing-section',
            start: 'top bottom',
            end: 'bottom top',
            scrub: 1
          }
        });
      }, heroRoot);

      scrollHandler = updateMograph;
      resizeHandler = updateMograph;

      refreshHandler = () => {
        ScrollTrigger.refresh();
        updateMograph();
      };

      window.addEventListener('scroll', scrollHandler, { passive: true });
      window.addEventListener('resize', resizeHandler, { passive: true });
      window.addEventListener('load', refreshHandler);

      refreshTimers.push(setTimeout(refreshHandler, 300));
      refreshTimers.push(setTimeout(refreshHandler, 800));
    };

    void initializeAnimations().catch((error: unknown) => {
      console.error('Failed to initialize homepage animations:', error);
    });

    return () => {
      disposed = true;

      if (scrollHandler) window.removeEventListener('scroll', scrollHandler);
      if (resizeHandler) window.removeEventListener('resize', resizeHandler);
      if (refreshHandler) window.removeEventListener('load', refreshHandler);

      if (ctaElement && ctaEnterHandler) {
        ctaElement.removeEventListener('mouseenter', ctaEnterHandler);
      }

      if (ctaElement && ctaLeaveHandler) {
        ctaElement.removeEventListener('mouseleave', ctaLeaveHandler);
      }

      refreshTimers.forEach((timer) => clearTimeout(timer));
      ctx?.revert();
    };
  });
</script>

<svelte:head>
  <title>Rizal Amri Rozaqi — Web Developer</title>
  <meta
    name="description"
    content="Web Developer portfolio of Rizal Amri Rozaqi, featuring selected website projects and digital experiences."
  />
</svelte:head>

<main bind:this={heroRoot} class="w-full max-w-full overflow-hidden text-gray-800">
  <!-- =====================================================
       01 — HERO
  ====================================================== -->

  <section class="hero relative flex min-h-screen w-full flex-col items-center justify-center overflow-hidden bg-gradient-to-br from-gray-200 via-gray-300 to-gray-100 md:flex-row">
    <div class="home-reveal hero-word relative z-30 flex w-full flex-col items-center p-6 text-center md:w-1/2 md:items-start md:pl-12 md:text-left">
      <p class="hero-line max-w-[15ch] text-3xl uppercase leading-tight text-gray-800 sm:text-4xl md:max-w-none md:text-5xl lg:text-6xl font-bebas">
        {#if $language === 'id'}
          Membangun pengalaman digital untuk web.
        {:else}
          Building thoughtful experiences for the web.
        {/if}
      </p>

      <p class="hero-meta mt-4 max-w-lg text-base text-gray-600 sm:text-lg md:text-xl">
        {#if $language === 'id'}
          Saya tertarik pada front-end development, antarmuka responsif, dan website yang fungsional.
        {:else}
          Exploring front-end development, responsive interfaces, and functional websites.
        {/if}
      </p>
    </div>

    <!-- VIDEO SHARDS -->

    <div class="hero-visual relative mt-8 h-[42svh] min-h-[250px] max-h-[430px] w-full overflow-hidden perspective-[1000px] sm:mt-10 md:mt-0 md:h-screen md:min-h-0 md:max-h-none md:w-1/2"
      onmousemove={handleHeroMouseMove}
      role="presentation"
    >
      {#each shards as path, i}
        <div
          onmouseenter={() => handleShardEnter(i)}
          onmouseleave={() => handleShardLeave(i)}
          style={`clip-path: ${path}; transform-style: preserve-3d; perspective: 800px;`}
          class="absolute inset-0"
          role="presentation"
        >
          <div bind:this={shardEls[i]} class="h-full w-full">
            <video
              src="/videos/rizal.webm"
              autoplay
              loop
              muted
              playsinline
              preload="metadata"
              class="absolute left-0 top-0 h-full w-full rounded-lg object-cover shadow-xl"
              style={`clip-path: ${path};`}
            ></video>

            <div class="absolute inset-0 rounded-lg bg-gradient-to-br from-white/20 to-transparent opacity-30 mix-blend-overlay"></div>
          </div>
        </div>
      {/each}
    </div>
  </section>

  <!-- =====================================================
       02 — MARQUEE
  ====================================================== -->

  <section bind:this={marqueeContainer} class="relative z-20 w-full max-w-full overflow-hidden bg-transparent">
    <div class="flex w-full overflow-hidden bg-gradient-to-b from-gray-800 to-slate-700 py-5">
      <div bind:this={textWrapper} class="flex w-fit whitespace-nowrap text-[6rem] font-bold tracking-widest text-white font-bebas sm:text-[10rem] md:text-[20rem]">
        {#each Array(4) as _}
          <span class="mx-8">RIZAL AMRI ROZAQI</span>
        {/each}
      </div>
    </div>
  </section>

  <!-- =====================================================
       03A — DESKTOP CINEMATIC REEL
       Desain scroll panjang tetap dipertahankan di desktop.
  ====================================================== -->

  <section
    bind:this={mographSection}
    id="target-section"
    class="hero relative hidden h-[200vh] w-screen translate-y-2 transform overflow-hidden bg-gray-300 md:block"
  >
    <div bind:this={contRef} class="absolute left-0 top-0 flex h-full w-screen flex-row justify-center gap-40 overflow-hidden">
      {#each Array(4) as _}
        <div class="relative h-full w-[2%] bg-gray-950">
          <div class="h-full w-full border-x border-gray-800 bg-[image:repeating-linear-gradient(315deg,gray_0,gray_1px,transparent_0,transparent_50%)] bg-[size:10px_10px] opacity-10"></div>
        </div>
      {/each}
    </div>

    <div class="absolute bottom-0 h-[50vh] w-screen overflow-hidden border-t border-gray-700 bg-gray-800">
      <div class="relative h-full w-full">
        <div class="relative z-10 flex items-center gap-10 border-b border-gray-800 bg-gray-950 py-3">
          <span bind:this={spanRef1} class="whitespace-nowrap text-3xl tracking-widest text-gray-400 font-mono">Web Development. Responsive Interfaces. Digital Experiences.</span>
          <span bind:this={spanRef2} class="whitespace-nowrap text-3xl tracking-widest text-gray-400 font-mono">Web Development. Responsive Interfaces. Digital Experiences.</span>
          <span bind:this={spanRef3} class="whitespace-nowrap text-3xl tracking-widest text-gray-400 font-mono">Web Development. Responsive Interfaces. Digital Experiences.</span>
        </div>

        <div class="pointer-events-none absolute inset-0 z-0 overflow-hidden bg-gray-900">
          {#each particles as p}
            <div class="particle-anim absolute rounded-full bg-sky-400" style="left: {p.left}%; width: {p.size}px; height: {p.size}px; opacity: {p.opacity}; animation-duration: {p.duration}s; animation-delay: {p.delay}s;"></div>
          {/each}
        </div>
      </div>
    </div>

    <div class="sticky top-0 z-10 flex h-screen items-center justify-center overflow-hidden">
      <video
        bind:this={vidRef}
        src="/videos/hero-mograph.webm"
        autoplay
        loop
        muted
        playsinline
        preload="metadata"
        class="pointer-events-none w-[250px] rounded-xl object-cover shadow-[0_0_50px_rgba(56,189,248,0.2)]"
      ></video>
    </div>

    <h1 bind:this={textRef1} class="absolute bottom-[35%] left-1/2 z-20 mb-4 -translate-x-1/2 -translate-y-1/2 transform whitespace-nowrap text-4xl font-black tracking-widest text-white mix-blend-difference font-bebas md:text-6xl">
      PROJECTS
    </h1>

    <h1 bind:this={textRef2} class="absolute bottom-[5%] left-1/2 z-20 mb-4 -translate-x-1/2 -translate-y-1/2 transform whitespace-nowrap text-4xl font-black tracking-widest text-sky-400 mix-blend-difference font-bebas md:text-6xl">
      DIGITAL EXPERIENCES
    </h1>
  </section>

  <!-- =====================================================
       03B — MOBILE PROJECT LIST
       Layout vertikal khusus layar kecil.
  ====================================================== -->

  <section class="mobile-projects-section bg-gray-800 px-5 py-16 text-white md:hidden sm:px-6 sm:py-20">
    <div class="home-reveal mb-9">
      <div class="font-mono text-[8px] uppercase tracking-[0.15em] text-sky-400">/ Selected Work</div>
      <h2 class="mt-4 font-bebas text-5xl leading-[0.85] tracking-[-0.02em] sm:text-6xl">WEB PROJECTS</h2>

      <p class="mt-4 max-w-sm text-sm leading-relaxed text-white/55">
        {#if $language === 'id'}
          Beberapa website dan antarmuka yang pernah saya kerjakan.
        {:else}
          Selected websites and interfaces I've worked on.
        {/if}
      </p>
    </div>

    <div class="space-y-5">
      {#each projects as project}
        <article class="mobile-project-card overflow-hidden rounded-[5px] bg-[#131B2C] ring-1 ring-white/10">
          <div class="relative aspect-video overflow-hidden bg-[#080D16]">
            <video
              src={project.media}
              loop
              muted
              playsinline
              preload="metadata"
              class="mobile-project-video h-full w-full object-cover"
            ></video>

            <div class="pointer-events-none absolute inset-0 bg-gradient-to-t from-[#0A111F]/70 via-transparent to-transparent"></div>

            <span class="absolute left-4 top-4 rounded-full border border-white/15 bg-black/25 px-3 py-1.5 font-mono text-[8px] uppercase tracking-wider text-white/85 backdrop-blur-sm">
              {project.category}
            </span>

            <span class="absolute bottom-3 right-4 font-bebas text-3xl text-white/80">
              {project.number}
            </span>
          </div>

          <div class="p-5 sm:p-6">
            <h3 class="font-playfair text-2xl leading-tight sm:text-3xl">{project.title}</h3>
            <p class="mt-3 text-sm leading-relaxed text-white/60">{project.description[$language]}</p>
          </div>
        </article>
      {/each}
    </div>

    <a href="/Project" class="mt-9 inline-flex items-center gap-4 border-b border-white/30 pb-2 font-mono text-[9px] uppercase tracking-[0.14em] transition-colors hover:border-sky-400 hover:text-sky-300">
      {#if $language === 'id'}Lihat Semua Proyek{:else}View All Projects{/if}
      <span class="text-sky-400">↗</span>
    </a>
  </section>

  <!-- =====================================================
       04 — PROJECT CTA
  ====================================================== -->

  <section class="relative flex min-h-[55vh] items-center overflow-hidden bg-[#AEB7C3] px-6 py-24 md:px-12 md:py-32">
    <div class="pointer-events-none absolute -right-[6vw] top-1/2 -translate-y-1/2 select-none font-bebas text-[22vw] leading-none text-[#172238]/[0.035]">WORK</div>

    <div class="mx-auto flex w-full max-w-7xl flex-col justify-between gap-12 md:flex-row md:items-end">
      <div class="home-reveal max-w-2xl">
        <div class="font-mono text-[9px] uppercase tracking-[0.15em] text-[#172238]/40">/ Selected Work</div>

        <h2 class="mt-6 font-playfair text-5xl leading-[0.9] tracking-[-0.02em] md:text-7xl">
          {#if $language === 'id'}
            Ingin melihat <span class="text-[#172238]/30">proyek web saya?</span>
          {:else}
            Want to see what <span class="text-[#172238]/30">I've been building?</span>
          {/if}
        </h2>
      </div>

      <a href="/Project" data-cursor-hover class="project-cta group inline-flex shrink-0 items-center gap-4 self-start font-mono text-[9px] uppercase tracking-[0.14em] text-[#172238] md:self-end">
        <span class="border-b border-[#172238]/35 pb-2">View Projects</span>
        <span class="cta-arrow text-[#18A9E8]">↗</span>
      </a>
    </div>
  </section>

  <!-- =====================================================
       05 — FOCUS AREAS
  ====================================================== -->

  <section class="relative overflow-hidden bg-[#DDE2E7] px-6 py-28 md:px-12 md:py-40">
    <div class="background-word pointer-events-none absolute -right-[5vw] top-[28%] select-none whitespace-nowrap font-bebas text-[19vw] leading-none text-[#172238]/[0.045]">DEVELOPMENT</div>

    <div class="relative z-10 mx-auto max-w-7xl">
      <div class="home-reveal max-w-3xl">
        <div class="font-mono text-[9px] uppercase tracking-[0.16em] text-[#172238]/45">/ Areas of Interest</div>
        <h2 class="mt-6 font-playfair text-4xl leading-[1.05] md:text-6xl">Design-minded development.</h2>

        <p class="mt-6 max-w-xl text-sm leading-relaxed text-[#172238]/60 md:text-base">
          {#if $language === 'id'}
            Saya tertarik pada proses menerjemahkan ide dan desain menjadi antarmuka digital yang jelas, responsif, dan berguna.
          {:else}
            I'm interested in translating ideas and designs into digital interfaces that are clear, responsive, and useful.
          {/if}
        </p>
      </div>

      <div class="mt-14 grid gap-8 md:grid-cols-3">
        {#each focusAreas as area}
          <article class="focus-card border-t border-[#172238]/20 pt-5">
            <span class="font-mono text-[9px] text-[#172238]/45">{area.number}</span>
            <h3 class="mt-5 font-playfair text-2xl md:text-3xl">{area.title[$language]}</h3>
            <p class="mt-4 max-w-sm text-sm leading-relaxed text-[#172238]/60">{area.description[$language]}</p>
          </article>
        {/each}
      </div>
    </div>
  </section>

  <!-- =====================================================
       06 — CLOSING
  ====================================================== -->

  <section class="closing-section relative flex min-h-[78vh] items-center overflow-hidden bg-[#EEF0F2] px-6 py-32 md:px-12 md:py-44">
    <div class="closing-word pointer-events-none absolute -bottom-8 left-[-4vw] select-none whitespace-nowrap font-bebas text-[20vw] leading-none text-[#172238]/[0.035]">PASSION</div>

    <div class="closing-content relative z-10 mx-auto w-full max-w-7xl">
      <div class="font-mono text-[9px] uppercase tracking-[0.15em] text-[#172238]/40">/ Until next time</div>

      <div class="mt-12">
        <h2 class="font-serif text-[clamp(1.35rem,8vw,7vw)] leading-[0.95] tracking-[-0.025em] md:text-[7vw]">
          雪の下にも、情熱がある。
        </h2>

        <p class="mt-8 font-bebas text-2xl tracking-[0.02em] text-[#172238]/70 md:text-4xl">
          EVEN BENEATH THE SNOW, PASSION BURNS.
        </p>
      </div>

      <div class="mt-20 flex items-center gap-3 font-mono text-[8px] uppercase tracking-[0.15em] text-[#172238]/40">
        <span class="h-2 w-2 rounded-full bg-[#18A9E8]"></span>
        More to come
      </div>
    </div>
  </section>
</main>

<style>
  .particle-anim {
    bottom: -10px;
    animation-name: floatUp;
    animation-timing-function: linear;
    animation-iteration-count: infinite;
    will-change: transform;
  }

  @keyframes floatUp {
    0% {
      transform: translateY(20px);
    }

    100% {
      transform: translateY(-55vh);
    }
  }
</style>