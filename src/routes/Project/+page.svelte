<script lang="ts">
  import { onMount } from 'svelte';
  import { gsap } from 'gsap';
  import { language } from '$lib/stores/language';

  const rawProjects = [
    {
      title: 'Company Profile',
      category: 'Front-End',
      type: 'video',
      media: '/videos/company-profile.webm',
      gridClass: 'md:col-span-2 md:row-span-1',
      description: {
        id: 'Website company profile.',
        en: 'Company profile website.'
      }
    },
    {
      title: 'Investment Potential',
      category: 'UI/UX',
      type: 'video',
      media: '/videos/invest-potential.webm',
      gridClass: 'md:col-span-2 md:row-span-1',
      description: {
        id: 'Web Potensial Investasi Daerah.',
        en: 'Regional Investment Potential Website.'
      }
    },
    {
      title: 'Link Archive',
      category: 'Full-Stack',
      type: 'video',
      media: '/videos/ungumark.webm',
      gridClass: 'md:col-span-2 md:row-span-1',
      description: {
        id: 'Eksplorasi identitas visual brand fiksi.',
        en: 'Visual identity explorations.'
      }
    },
    {
      title: 'Unified Beneficiary',
      category: 'Front-End',
      type: 'video',
      media: '/videos/solidaritas.webm',
      gridClass: 'md:col-span-2 md:row-span-1',
      description: {
        id: 'Sistem pendaftaran penerima manfaat terpadu.',
        en: 'Unified beneficiary registry.'
      }
    },
    {
      title: 'Licensing',
      category: 'Front-End',
      type: 'video',
      media: '/videos/perizinan.webm',
      gridClass: 'md:col-span-2 md:row-span-1',
      description: {
        id: 'Website lisensi.',
        en: 'Licensing website.'
      }
    }
  ];

  export const projects = rawProjects.map((project, index) => ({
    number: String(index + 1).padStart(2, '0'),
    ...project
  }));

  const processes = [
    {
      num: '01',
      title: 'Discovery',
      desc: {
        id: 'Memahami visi, audiens, dan tujuan utama.',
        en: 'Understanding vision, audience, and core goals.'
      }
    },
    {
      num: '02',
      title: 'Design',
      desc: {
        id: 'Eksplorasi visual & pembuatan prototipe.',
        en: 'Visual exploration & prototyping.'
      }
    },
    {
      num: '03',
      title: 'Development',
      desc: {
        id: 'Menerjemahkan desain menjadi kode.',
        en: 'Translating design into code.'
      }
    },
    {
      num: '04',
      title: 'Delivery',
      desc: {
        id: 'Pengujian akhir dan peluncuran produk.',
        en: 'Final testing and product launch.'
      }
    }
  ];

  // =====================================================
  // AUDIO STATE
  // =====================================================

  let activeAudioVideo: HTMLVideoElement | null = null;
  let hasInteractedWithAudio = false;

  const updateVideoAudio = (video: HTMLVideoElement) => {
    if (video.muted) {
      if (activeAudioVideo && activeAudioVideo !== video) {
        activeAudioVideo.muted = true;
      }

      video.muted = false;
      activeAudioVideo = video;
      hasInteractedWithAudio = true;

      return;
    }

    video.muted = true;

    if (activeAudioVideo === video) {
      activeAudioVideo = null;
    }
  };

  const toggleProjectAudio = (container: HTMLElement) => {
    const video =
      container.querySelector<HTMLVideoElement>('.project-video');

    if (video) {
      updateVideoAudio(video);
    }
  };

  // =====================================================
  // GSAP / RESPONSIVE ANIMATIONS
  // =====================================================

  onMount(() => {
    let disposed = false;
    let mm: ReturnType<typeof gsap.matchMedia> | undefined;

    const initializeAnimations = async () => {
      const { ScrollTrigger } = await import('gsap/ScrollTrigger');

      if (disposed) return;

      gsap.registerPlugin(ScrollTrigger);

      ScrollTrigger.config({
        ignoreMobileResize: true
      });

      mm = gsap.matchMedia();

      mm.add(
        {
          isDesktop: '(min-width: 768px)',
          isMobile: '(max-width: 767px)'
        },
        (context) => {
          const { isMobile } = context.conditions as {
            isDesktop: boolean;
            isMobile: boolean;
          };

          // PROJECT CARD REVEAL

          gsap.utils
            .toArray<HTMLElement>('.bento-card')
            .forEach((card) => {
              gsap.fromTo(
                card,
                {
                  opacity: 0,
                  y: isMobile ? 20 : 40
                },
                {
                  opacity: 1,
                  y: 0,
                  duration: isMobile ? 0.8 : 1.2,
                  ease: 'power3.out',
                  scrollTrigger: {
                    trigger: card,
                    start: 'top 85%',
                    once: true
                  }
                }
              );
            });

          // PROCESS STEP REVEAL

          gsap.utils
            .toArray<HTMLElement>('.process-step')
            .forEach((step, index) => {
              gsap.fromTo(
                step,
                {
                  opacity: 0,
                  y: 30
                },
                {
                  opacity: 1,
                  y: 0,
                  duration: 0.8,
                  delay: index * (isMobile ? 0.05 : 0.12),
                  ease: 'power3.out',
                  scrollTrigger: {
                    trigger: step,
                    start: 'top 85%',
                    once: true
                  }
                }
              );
            });

          // VIDEO PLAY / PAUSE BASED ON VIEWPORT

          gsap.utils
            .toArray<HTMLVideoElement>('.project-video')
            .forEach((video) => {
              ScrollTrigger.create({
                trigger: video,
                start: 'top 100%',
                end: 'bottom 0%',

                onEnter: () => {
                  video.play().catch(() => {});
                },

                onEnterBack: () => {
                  video.play().catch(() => {});
                },

                onLeave: () => {
                  video.pause();
                },

                onLeaveBack: () => {
                  video.pause();
                }
              });
            });

          // INTRO BACKGROUND PARALLAX

          gsap.to('.intro-word', {
            xPercent: -6,
            ease: 'none',
            scrollTrigger: {
              trigger: '.intro-section',
              start: 'top top',
              end: 'bottom top',
              scrub: 1
            }
          });

          // PROCESS BACKGROUND PARALLAX

          gsap.to('.process-word', {
            xPercent: 6,
            ease: 'none',
            scrollTrigger: {
              trigger: '.process-section',
              start: 'top bottom',
              end: 'bottom top',
              scrub: 1
            }
          });
        }
      );

      requestAnimationFrame(() => {
        ScrollTrigger.refresh();
      });
    };

    void initializeAnimations().catch((error: unknown) => {
      console.error(
        'Failed to initialize portfolio animations:',
        error
      );
    });

    // CLEANUP

    return () => {
      disposed = true;

      mm?.revert();

      if (activeAudioVideo) {
        activeAudioVideo.muted = true;
        activeAudioVideo = null;
      }
    };
  });
</script>

<svelte:head>
  <title>Web Developer Portfolio — Rizal Amri Rozaqi</title>

  <meta
    name="description"
    content="Web development portfolio showcasing company profile websites, regional investment potential, link archive, beneficiary registration, and licensing projects."
  />
</svelte:head>

<main
  class="overflow-hidden bg-[#EEF0F2] text-[#172238] selection:bg-[#18A9E8] selection:text-[#F0F2F4]"
>
  <!-- =====================================================
       01 — INTRO
  ====================================================== -->

  <section
    class="intro-section relative flex min-h-[90vh] flex-col justify-end overflow-hidden px-6 pb-20 pt-32 md:px-12"
  >
    <div
      class="intro-word pointer-events-none absolute -right-[4vw] top-[20%] select-none font-bebas text-[17vw] leading-none text-[#172238]/[0.03] will-change-transform"
    >
      DEVELOPER
    </div>

    <div class="relative z-10 mx-auto w-full max-w-7xl">
      <div
        class="intro-meta font-mono text-[9px] uppercase tracking-[0.16em] text-[#172238]/45"
      >
        / 01 — Web Developer Portfolio
      </div>

      <div class="mt-8 overflow-hidden">
        <h1
          class="intro-title font-bebas text-[16vw] leading-[0.78] tracking-[-0.035em] md:text-[12vw] will-change-transform"
        >
          BUILDING
        </h1>
      </div>

      <div class="overflow-hidden">
        <h2
          class="intro-title font-bebas text-[11vw] leading-[0.78] tracking-[-0.03em] text-[#172238]/25 md:text-[8vw] will-change-transform"
        >
          FOR THE WEB.
        </h2>
      </div>

      <div
        class="intro-copy mt-10 flex flex-col justify-between gap-6 md:flex-row md:items-end"
      >
        {#if $language === 'id'}
          <p
            class="max-w-xl text-base leading-relaxed text-[#172238]/55 md:text-lg"
          >
            Saya tertarik pada pengembangan web, mulai dari
            membangun antarmuka hingga mengembangkan pengalaman
            digital yang fungsional dan responsif. Berikut beberapa
            proyek website dan antarmuka yang pernah saya kerjakan.
          </p>
        {:else}
          <p
            class="max-w-xl text-base leading-relaxed text-[#172238]/55 md:text-lg"
          >
            I'm interested in web development, from building
            interfaces to creating functional and responsive
            digital experiences. Here are selected website and
            interface projects I've worked on.
          </p>
        {/if}

        <div
          class="flex shrink-0 items-center gap-3 font-mono text-[8px] uppercase tracking-[0.15em] text-[#172238]/40"
        >
          <span class="h-2 w-2 rounded-full bg-[#18A9E8]"></span>
          Scroll to explore →
        </div>
      </div>
    </div>
  </section>

  <!-- =====================================================
       02 — WEB PROJECTS
  ====================================================== -->

  <section
    class="projects-section relative z-20 -mt-10 bg-[#172238] px-6 py-24 shadow-[0_-20px_50px_rgba(0,0,0,0.1)] md:-mt-16 md:px-12 md:py-32"
  >
    <div class="mx-auto max-w-7xl">
      <div class="mb-16 md:mb-24">
        <div
          class="font-mono text-[8px] uppercase tracking-[0.15em] text-[#18A9E8]"
        >
          / 02 — Selected Web Projects
        </div>

        <h2
          class="mt-6 max-w-2xl font-playfair text-4xl leading-[1.1] text-white/95 md:text-5xl"
        >
          Websites and interfaces
          <span class="opacity-30">
            built around real needs.
          </span>
        </h2>
      </div>

      <!-- BENTO GRID -->

      <div
        class="grid grid-cols-1 gap-2 md:auto-rows-[320px] md:grid-flow-row-dense md:grid-cols-4 md:gap-3"
      >
        {#each projects as project}
          <article
            class="bento-card group relative flex h-full w-full flex-col justify-end overflow-hidden rounded-[5px] bg-[#131B2C] ring-1 ring-white/5 {project.gridClass} will-change-[transform,opacity]"
            onclick={(event) => toggleProjectAudio(event.currentTarget)}
            onkeydown={(event) => {
              if (event.key === 'Enter' || event.key === ' ') {
                event.preventDefault();
                toggleProjectAudio(event.currentTarget);
              }
            }}
            role="button"
            tabindex="0"
            aria-label={`Toggle sound for ${project.title}`}
          >
            <!-- MEDIA LAYER -->

            <div
              class="relative z-0 h-auto w-full overflow-hidden bg-[#080D16] md:absolute md:inset-0 md:h-full md:w-full"
            >
              {#if project.type === 'video'}
                <video
                  src={project.media}
                  loop
                  muted
                  playsinline
                  preload="metadata"
                  class="project-video h-auto w-full object-cover opacity-80 transition-transform duration-[1.2s] ease-[cubic-bezier(0.16,1,0.3,1)] group-hover:scale-105 will-change-transform md:h-full md:w-full"
                ></video>
              {:else}
                <img
                  src={project.media}
                  alt={project.title}
                  loading="lazy"
                  class="h-auto w-full object-cover opacity-80 transition-transform duration-[1.2s] ease-[cubic-bezier(0.16,1,0.3,1)] group-hover:scale-105 will-change-transform md:h-full md:w-full"
                />
              {/if}
            </div>

            <!-- GRADIENT OVERLAY -->

            <div
              class="pointer-events-none absolute inset-0 z-10 bg-gradient-to-t from-[#0A111F] via-[#0A111F]/40 to-transparent opacity-10 transition-opacity duration-700 group-hover:opacity-100 will-change-opacity"
            ></div>

            <!-- PROJECT TAGS -->

            <div
              class="absolute left-6 top-6 z-20 flex w-[calc(100%-3rem)] items-start justify-between gap-3"
            >
              <span
                class="px-3.5 py-1.5 font-mono text-[9px] uppercase tracking-wider text-white/80 transition-colors duration-500 group-hover:text-white grroup-hover:drop-shadow-[0_0_10px_rgba(255,255,255,1)] will-change-[color,filter]"
              >
                {project.category}
              </span>

              <!-- {#if !hasInteractedWithAudio}
                <span
                  class="rounded-full bg-black/20 px-3 py-1.5 font-mono text-[8px] uppercase tracking-wider text-white/55"
                >
                  Click for sound
                </span>
              {/if} -->
            </div>

            <!-- PROJECT CONTENT -->

            <div class="relative z-20 mt-auto p-6 md:p-8">
              <div class="flex items-end justify-between gap-4">
                <div class="w-full">
                  <h3
                    class="font-playfair text-2xl font-medium leading-tight text-white/95 md:text-3xl"
                  >
                    {project.title}
                  </h3>

                  <div
                    class="grid grid-rows-[1fr] transition-[grid-template-rows] duration-[1s] ease-[cubic-bezier(0.16,1,0.3,1)] will-change-[grid-template-rows] md:grid-rows-[0fr] md:group-hover:grid-rows-[1fr]"
                  >
                    <div class="overflow-hidden">
                      <p
                        class="mt-3 max-w-[95%] translate-y-0 text-sm leading-relaxed text-white/60 opacity-100 transition-all duration-[1s] ease-[cubic-bezier(0.16,1,0.3,1)] will-change-[transform,opacity] md:translate-y-2 md:opacity-0 md:group-hover:translate-y-0 md:group-hover:opacity-100"
                      >
                        {project.description[$language]}
                      </p>
                    </div>
                  </div>
                </div>

                <span
                  class="shrink-0 font-bebas text-4xl leading-none text-white/20 transition-colors duration-700 group-hover:text-[#18A9E8]"
                >
                  {project.number}
                </span>
              </div>
            </div>
          </article>
        {/each}
      </div>
    </div>
  </section>

  <!-- =====================================================
       03 — WEB DEVELOPMENT PROCESS
  ====================================================== -->

  <section
    class="process-section relative overflow-hidden bg-[#AEB7C3] px-6 py-32 md:px-12 md:py-44"
  >
    <div
      class="process-word pointer-events-none absolute -right-[5vw] top-[36%] select-none font-bebas text-[20vw] leading-none text-[#172238]/[0.045]"
    >
      DEVELOPMENT
    </div>

    <div class="relative z-10 mx-auto max-w-7xl">
      <div class="grid gap-16 md:grid-cols-[0.7fr_1.3fr]">
        <div class="reveal">
          <div
            class="font-mono text-[9px] uppercase tracking-[0.15em] text-[#172238]/45"
          >
            / 03 — Web Development Process
          </div>

          <h2
            class="mt-6 font-bebas text-[13vw] leading-[0.72] tracking-[-0.03em] md:text-[9vw]"
          >
            HOW I<br />BUILD
          </h2>

          {#if $language === 'id'}
            <p
              class="mt-8 max-w-sm text-sm leading-relaxed text-[#172238]/55"
            >
              Pendekatan kerja dari memahami kebutuhan hingga menguji
              hasil akhir. Detail proses dapat menyesuaikan kebutuhan
              setiap proyek.
            </p>
          {:else}
            <p
              class="mt-8 max-w-sm text-sm leading-relaxed text-[#172238]/55"
            >
              A workflow from understanding requirements to reviewing
              the final result. The details can adapt to each project's
              needs.
            </p>
          {/if}
        </div>

        <div class="grid gap-8 sm:grid-cols-2">
          {#each processes as process}
            <article
              class="process-step border-t border-[#172238]/25 pt-5"
            >
              <span class="font-mono text-[9px] text-[#172238]/40">
                {process.num}
              </span>

              <h3 class="mt-4 font-playfair text-3xl">
                {process.title}
              </h3>

              <p
                class="mt-4 max-w-sm text-sm leading-relaxed text-[#172238]/55"
              >
                {process.desc[$language]}
              </p>
            </article>
          {/each}
        </div>
      </div>
    </div>
  </section>
</main>