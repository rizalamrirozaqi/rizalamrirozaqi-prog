<script lang="ts">
  import { onMount } from 'svelte';
  import { gsap } from 'gsap';
  import { language } from '$lib/stores/language';

  let customCursor: HTMLDivElement | undefined = $state();

  // Teknologi yang pernah digunakan dalam proyek dan proses belajar.
  // Sesuaikan dengan kemampuan yang siap kamu demonstrasikan.
  const skillGroups = [
    {
      number: '01',
      name: 'FRONTEND DEVELOPMENT',
      category: 'STRUCTURE | INTERACTIVITY | RESPONSIVE UI',
      skills: [
        'HTML',
        'CSS',
        'JavaScript',
        'TypeScript',
        'Next.js',
        'Svelte'
      ]
    },
    {
      number: '02',
      name: 'UI & STYLING',
      category: 'LAYOUT | COMPONENTS | DESIGN',
      skills: [
        'Tailwind CSS',
        'Bootstrap',
        'Responsive Design',
        'Figma'
      ]
    },
    {
      number: '03',
      name: 'BACKEND & DATABASE',
      category: 'SERVER-SIDE | DATA | APPLICATIONS',
      skills: [
        'PHP',
        'CodeIgniter',
        'Yii2',
        'MySQL'
      ]
    },
    {
      number: '04',
      name: 'DEVELOPMENT TOOLS',
      category: 'VERSION CONTROL | WORKFLOW',
      skills: [
        'Git',
        'GitHub',
        'VS Code',
        'Docker',
        'Composer'
      ]
    }
  ];

  // Kemampuan kreatif tetap dipertahankan sebagai nilai tambah.
  const creativeTools = [
    'AFTER EFFECTS',
    'PHOTOSHOP',
    'BLENDER',
    'CANVA'
  ];

  const experiences = [
    {
      number: '01',
      company: 'PT Diginet Media',
      role: 'FrontEnd Developer — Intern',
      period: 'AUG 2025 — DEC 2025',
      description: {
        id: 'Berpengalaman dalam pengembangan antarmuka website dan aplikasi menggunakan teknologi frontend. Terlibat dalam implementasi serta penyesuaian tampilan antarmuka, pengecekan hasil pengembangan, dan perbaikan berdasarkan kebutuhan proyek. Pengalaman ini mengasah kemampuan pemecahan masalah, pemahaman sistem aplikasi, serta adaptasi terhadap kebutuhan teknis.',
        en: 'Gained experience in developing website and application interfaces using frontend technologies. Involved in implementing and adjusting user interfaces, checking development results, and making revisions based on project requirements. This experience strengthened problem-solving skills, understanding of application systems, and adaptability to technical needs.'
      }
    },
    {
      number: '02',
      company: 'Klinik Komputer WTC',
      role: 'IT Support — Intern',
      period: 'FEB 2022 — MAY 2022',
      description: {
        id: 'Mendapatkan pengalaman awal di bidang IT support, termasuk instalasi jaringan dasar, troubleshooting perangkat keras, dan penanganan konektivitas lokal.',
        en: 'Gained early experience in IT support, including basic network installation, hardware troubleshooting, and local connectivity issues.'
      }
    }
  ];

  const education = [
    {
      period: '2023 — 2026',
      institution: 'Universitas Amikom Yogyakarta',
      program: 'D3 Teknik Informatika'
    },
    {
      period: '2020 — 2023',
      institution: 'SMK Islam Pemalang',
      program: 'Teknik Komputer dan Jaringan'
    }
  ];

  onMount(() => {
    let disposed = false;
    let ctx: ReturnType<typeof gsap.context> | undefined;
    let mouseMoveHandler: ((event: MouseEvent) => void) | undefined;
    let refreshHandler: (() => void) | undefined;

    const refreshTimers: ReturnType<typeof setTimeout>[] = [];
    const hoverCleanups: Array<() => void> = [];

    const initializeAnimations = async () => {
      const { ScrollTrigger } = await import('gsap/ScrollTrigger');

      if (disposed) return;

      gsap.registerPlugin(ScrollTrigger);

      ctx = gsap.context(() => {
        // =====================================================
        // HERO INTRO
        // =====================================================

        gsap.timeline({
          defaults: {
            ease: 'power4.out'
          }
        })
          .fromTo(
            '.hero-line',
            {
              yPercent: 110,
              opacity: 0
            },
            {
              yPercent: 0,
              opacity: 1,
              duration: 1.1,
              stagger: 0.1
            }
          )
          .fromTo(
            '.hero-meta',
            {
              opacity: 0,
              y: 20
            },
            {
              opacity: 1,
              y: 0,
              duration: 0.8,
              stagger: 0.05
            },
            '-=0.6'
          )
          .fromTo(
            '.hero-visual',
            {
              opacity: 0,
              scale: 0.96,
              y: 40
            },
            {
              opacity: 1,
              scale: 1,
              y: 0,
              duration: 1.1
            },
            '-=0.45'
          );

        // =====================================================
        // HERO PARALLAX
        // =====================================================

        gsap.to('.hero-word', {
          yPercent: -8,
          ease: 'none',
          scrollTrigger: {
            trigger: '.hero',
            start: 'top top',
            end: 'bottom top',
            scrub: 1
          }
        });

        gsap.to('.hero-visual', {
          yPercent: -6,
          rotate: -0.5,
          ease: 'none',
          scrollTrigger: {
            trigger: '.hero',
            start: 'top top',
            end: 'bottom top',
            scrub: 1
          }
        });

        // =====================================================
        // GENERAL REVEAL
        // =====================================================

        gsap.utils
          .toArray<HTMLElement>('.reveal')
          .forEach((element) => {
            gsap.fromTo(
              element,
              {
                opacity: 0,
                y: 35
              },
              {
                opacity: 1,
                y: 0,
                duration: 0.8,
                ease: 'power3.out',
                scrollTrigger: {
                  trigger: element,
                  start: 'top 85%',
                  once: true
                }
              }
            );
          });

        // =====================================================
        // BACKGROUND WORD PARALLAX
        // =====================================================

        gsap.utils
          .toArray<HTMLElement>('.background-word')
          .forEach((element) => {
            gsap.to(element, {
              xPercent: -6,
              ease: 'none',
              scrollTrigger: {
                trigger: element.parentElement ?? element,
                start: 'top bottom',
                end: 'bottom top',
                scrub: 1
              }
            });
          });

        // =====================================================
        // EXPERIENCE REVEAL
        // =====================================================

        gsap.utils
          .toArray<HTMLElement>('.experience-item')
          .forEach((element, index) => {
            gsap.fromTo(
              element,
              {
                opacity: 0,
                x: index % 2 === 0 ? -25 : 25
              },
              {
                opacity: 1,
                x: 0,
                duration: 0.75,
                ease: 'power3.out',
                scrollTrigger: {
                  trigger: element,
                  start: 'top 86%',
                  once: true
                }
              }
            );
          });

        // =====================================================
        // SKILL REVEAL
        // =====================================================

        gsap.utils
          .toArray<HTMLElement>('.skill-row')
          .forEach((element, index) => {
            gsap.fromTo(
              element,
              {
                opacity: 0,
                y: 22
              },
              {
                opacity: 1,
                y: 0,
                duration: 0.65,
                delay: index * 0.04,
                ease: 'power3.out',
                scrollTrigger: {
                  trigger: element,
                  start: 'top 90%',
                  once: true
                }
              }
            );
          });

        // =====================================================
        // EDUCATION REVEAL
        // =====================================================

        gsap.utils
          .toArray<HTMLElement>('.education-item')
          .forEach((element, index) => {
            gsap.fromTo(
              element,
              {
                opacity: 0,
                y: 25
              },
              {
                opacity: 1,
                y: 0,
                duration: 0.7,
                delay: index * 0.08,
                ease: 'power3.out',
                scrollTrigger: {
                  trigger: element,
                  start: 'top 88%',
                  once: true
                }
              }
            );
          });

        // =====================================================
        // CLOSING
        // =====================================================

        gsap.to('.closing-word', {
          xPercent: -6,
          ease: 'none',
          scrollTrigger: {
            trigger: '.closing-section',
            start: 'top bottom',
            end: 'bottom top',
            scrub: 1
          }
        });

        gsap.fromTo(
          '.closing-content',
          {
            opacity: 0,
            y: 35
          },
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
      });

      // =====================================================
      // CUSTOM CURSOR
      // =====================================================

      if (
        customCursor &&
        window.matchMedia('(pointer: fine)').matches
      ) {
        const cursorX = gsap.quickTo(customCursor, 'x', {
          duration: 0.16,
          ease: 'power3.out'
        });

        const cursorY = gsap.quickTo(customCursor, 'y', {
          duration: 0.16,
          ease: 'power3.out'
        });

        mouseMoveHandler = (event: MouseEvent) => {
          cursorX(event.clientX);
          cursorY(event.clientY);
        };

        window.addEventListener('mousemove', mouseMoveHandler);

        document
          .querySelectorAll<HTMLElement>('[data-cursor-hover]')
          .forEach((item) => {
            const enter = () => {
              gsap.to(customCursor, {
                scale: 2.5,
                opacity: 0.45,
                duration: 0.25,
                ease: 'power3.out'
              });
            };

            const leave = () => {
              gsap.to(customCursor, {
                scale: 1,
                opacity: 1,
                duration: 0.25,
                ease: 'power3.out'
              });
            };

            item.addEventListener('mouseenter', enter);
            item.addEventListener('mouseleave', leave);

            hoverCleanups.push(() => {
              item.removeEventListener('mouseenter', enter);
              item.removeEventListener('mouseleave', leave);
            });
          });
      }

      // =====================================================
      // REFRESH
      // =====================================================

      refreshHandler = () => ScrollTrigger.refresh();

      window.addEventListener('load', refreshHandler);

      refreshTimers.push(
        window.setTimeout(() => ScrollTrigger.refresh(), 300)
      );

      refreshTimers.push(
        window.setTimeout(() => ScrollTrigger.refresh(), 800)
      );
    };

    void initializeAnimations().catch((error: unknown) => {
      console.error(
        'Failed to initialize about page animations:',
        error
      );
    });

    // CLEANUP

    return () => {
      disposed = true;

      if (mouseMoveHandler) {
        window.removeEventListener('mousemove', mouseMoveHandler);
      }

      if (refreshHandler) {
        window.removeEventListener('load', refreshHandler);
      }

      refreshTimers.forEach((timer) => {
        window.clearTimeout(timer);
      });

      hoverCleanups.forEach((cleanup) => cleanup());

      ctx?.revert();
    };
  });
</script>

<svelte:head>
  <title>About — Rizal Amri Rozaqi | Web Developer</title>

  <meta
    name="description"
    content="About Rizal Amri Rozaqi, a web developer with a background in informatics engineering, website development, and visual design."
  />
</svelte:head>

<!-- CUSTOM CURSOR -->

<div
  bind:this={customCursor}
  class="pointer-events-none fixed left-0 top-0 z-[99999] hidden h-4 w-4 -translate-x-1/2 -translate-y-1/2 rounded-full bg-[#172238] opacity-100 mix-blend-difference md:block"
></div>

<main
  class="min-h-screen overflow-hidden text-[#172238] selection:bg-[#18A9E8] selection:text-[#EEF0F2]"
>
  <!-- =====================================================
       HERO
  ====================================================== -->

  <section
    class="hero relative flex min-h-screen flex-col justify-between overflow-hidden px-6 py-8 md:px-12"
    style="background: linear-gradient(to bottom, #EEF0F2 0%, #E7EBEF 55%, #C9D1DA 100%);"
  >
    <header
      class="hero-meta flex items-center justify-between border-b border-[#172238]/15 pb-5 font-mono text-[9px] uppercase tracking-[0.18em] text-[#172238]/55"
    >
      <span>RIZAL AMRI ROZAQI</span>
      <span>Web Developer</span>
      <span class="hidden md:block">Indonesia</span>
    </header>

    <div class="hero-word relative z-10 mt-auto pt-24">
      <div class="overflow-hidden">
        <h1
          class="hero-line font-bebas text-[24vw] leading-[0.78] tracking-[-0.035em]"
        >
          RIZAL
        </h1>
      </div>

      <div
        class="flex flex-col justify-between gap-5 overflow-hidden md:flex-row md:items-end"
      >
        <div class="overflow-hidden">
          <h2
            class="hero-line font-bebas text-[17vw] leading-[0.8] tracking-[-0.03em] text-[#172238]/45 md:text-[11vw]"
          >
            AMRI ROZAQI
          </h2>
        </div>

        <p
          class="hero-meta max-w-xs pb-2 font-mono text-[10px] uppercase leading-relaxed tracking-[0.08em] text-[#172238]/55 md:text-xs"
        >
          Web development<br />
          Front-end & UI<br />
          Digital experiences
        </p>
      </div>
    </div>

    <div class="relative mt-10 flex justify-end">
      <div
        class="hero-visual relative aspect-[16/9] w-full overflow-hidden rounded-md bg-[#172238] shadow-[0_25px_70px_rgba(16,26,45,0.12)] md:w-[42vw]"
      >
        <video
          src="/videos/company-profile.webm"
          autoplay
          loop
          muted
          playsinline
          preload="metadata"
          class="h-full w-full object-cover"
        ></video>

        <div
          class="absolute bottom-0 left-0 right-0 flex justify-between bg-gradient-to-t from-[#101A2D]/70 to-transparent px-4 pb-3 pt-10 font-mono text-[8px] uppercase tracking-[0.15em] text-white/75"
        >
          <span>Selected Web Projects</span>
          <span>01 / 05</span>
        </div>
      </div>
    </div>

    <div
      class="hero-meta mt-5 flex items-center justify-between border-t border-[#172238]/15 pt-4 font-mono text-[9px] uppercase tracking-[0.15em] text-[#172238]/45"
    >
      <span>Scroll</span>
      <span>Front-end → Web Development</span>
      <span class="text-[#18A9E8]">↓</span>
    </div>
  </section>

  <!-- =====================================================
       ABOUT ME
  ====================================================== -->

  <section
    class="px-6 py-32 md:px-12 md:py-48"
    style="background: linear-gradient(to bottom, #C9D1DA 0%, #E7EBEF 22%, #EEF0F2 100%);"
  >
    <div class="mx-auto max-w-7xl">
      <div
        class="reveal mb-10 font-mono text-[9px] uppercase tracking-[0.2em] text-[#172238]/45"
      >
        / About Me
      </div>

      <h2
        class="reveal max-w-6xl font-playfair text-[9vw] font-medium leading-[0.96] md:text-[6.4vw]"
      >
        A developer who is
        <span class="text-[#172238]/30">always learning.</span>
      </h2>

      <div class="mt-20 grid gap-10 md:grid-cols-2">
        <div class="reveal">
          <div class="mb-4 h-px w-full bg-[#172238]/20"></div>

          {#if $language === 'id'}
            <p
              class="max-w-xl text-base leading-relaxed text-[#172238]/65 md:text-lg"
            >
              Saya lulusan D3 Teknik Informatika Universitas Amikom Yogyakarta.
              Saya memiliki ketertarikan pada pengembangan web, terutama dalam
              membangun antarmuka, menyusun layout yang responsif, dan mengubah
              kebutuhan menjadi pengalaman digital yang mudah digunakan.
            </p>
          {:else}
            <p
              class="max-w-xl text-base leading-relaxed text-[#172238]/65 md:text-lg"
            >
              I graduated with a Diploma in Informatics Engineering from
              Universitas Amikom Yogyakarta. I'm interested in web development,
              especially building interfaces, creating responsive layouts,
              and turning requirements into useful digital experiences.
            </p>
          {/if}
        </div>

        <div class="reveal">
          <div class="mb-4 h-px w-full bg-[#172238]/20"></div>

          {#if $language === 'id'}
            <p
              class="max-w-xl text-base leading-relaxed text-[#172238]/65 md:text-lg"
            >
              Saya juga mengeksplorasi motion graphics dan desain visual.
              Pengalaman kreatif ini melengkapi minat saya pada UI, detail
              antarmuka, dan cara sebuah produk digital menyampaikan informasi.
              Saya terus belajar melalui praktik, eksperimen, dan proyek.
            </p>
          {:else}
            <p
              class="max-w-xl text-base leading-relaxed text-[#172238]/65 md:text-lg"
            >
              I also explore motion graphics and visual design. This creative
              background complements my interest in UI, interface details,
              and how digital products communicate information. I continue
              learning through practice, experimentation, and projects.
            </p>
          {/if}
        </div>
      </div>
    </div>
  </section>

  <!-- =====================================================
       EXPERIENCE
  ====================================================== -->

  <section
    class="relative overflow-hidden px-6 py-32 md:px-12 md:py-40"
    style="background: linear-gradient(to bottom, #D8DEE5 0%, #B8C2CE 55%, #91A0B0 100%);"
  >
    <div
      class="background-word pointer-events-none absolute right-[-5vw] top-1/3 whitespace-nowrap font-bebas text-[18vw] leading-none text-[#172238]/[0.05]"
    >
      EXPERIENCE
    </div>

    <div class="relative z-10 mx-auto max-w-7xl">
      <div
        class="mb-20 flex flex-col justify-between gap-5 md:flex-row md:items-end"
      >
        <div class="reveal">
          <div
            class="font-mono text-[9px] uppercase tracking-[0.2em] text-[#172238]/45"
          >
            / Experience
          </div>

          <h2
            class="mt-5 font-bebas text-[15vw] leading-[0.75] md:text-[10vw]"
          >
            EXPERIENCE
          </h2>
        </div>

        <div
          class="reveal max-w-xs font-mono text-[10px] leading-relaxed text-[#172238]/45"
        >
          {#if $language === 'id'}
            Pengalaman kerja dan kesempatan untuk terus belajar serta berkembang.
          {:else}
            Work experience and opportunities to keep learning and growing.
          {/if}
        </div>
      </div>

      {#each experiences as experience}
        <article
          class="experience-item reveal border-t border-[#172238]/20 py-10"
        >
          <div class="grid gap-8 md:grid-cols-[120px_1fr_auto]">
            <span class="font-mono text-[10px] text-[#172238]/40">
              {experience.number}
            </span>

            <div>
              <h3 class="font-playfair text-4xl md:text-6xl">
                {experience.company}
              </h3>

              <p
                class="mt-2 font-mono text-[10px] uppercase tracking-[0.12em] text-[#172238]/50"
              >
                {experience.role}
              </p>

              <p class="mt-6 max-w-2xl leading-relaxed text-[#172238]/60">
                {experience.description[$language]}
              </p>
            </div>

            <span class="font-mono text-[10px] text-[#172238]/45">
              {experience.period}
            </span>
          </div>
        </article>
      {/each}

      <div class="h-px w-full bg-[#172238]/20"></div>
    </div>
  </section>

  <!-- =====================================================
       TECH STACK
  ====================================================== -->

  <section
    class="relative overflow-hidden py-32 text-[#EEF0F2] md:py-40"
    style="background: linear-gradient(to bottom, #1B2940 0%, #15233A 100%);"
  >
    <div class="px-6 md:px-12">
      <div class="mx-auto max-w-7xl">
        <div class="reveal">
          <div
            class="font-mono text-[9px] uppercase tracking-[0.2em] text-white/40"
          >
            / Technologies & Skills
          </div>

          <h2
            class="mt-6 font-bebas text-[18vw] leading-[0.72] tracking-[-0.03em] md:text-[11vw]"
          >
            TECH STACK
          </h2>

          {#if $language === 'id'}
            <p class="mt-8 max-w-md text-sm leading-relaxed text-white/45">
              Teknologi yang pernah saya gunakan dalam proses belajar dan
              pengembangan proyek web.
            </p>
          {:else}
            <p class="mt-8 max-w-md text-sm leading-relaxed text-white/45">
              Technologies I have used while learning and developing web projects.
            </p>
          {/if}
        </div>
      </div>
    </div>

    <div class="mt-20 border-t border-white/15">
      {#each skillGroups as group}
        <div
          class="skill-row group relative flex flex-col justify-between gap-5 border-b border-white/15 px-6 py-8 transition-colors duration-500 hover:bg-[#EEF0F2] hover:text-[#172238] md:flex-row md:items-center md:px-12 md:py-10"
        >
          <div class="flex items-start gap-5 md:w-[36%]">
            <span class="pt-2 font-mono text-[9px] text-[#18A9E8]">
              {group.number}
            </span>

            <div>
              <h3
                class="font-bebas text-4xl leading-none tracking-wide transition-transform duration-500 group-hover:translate-x-2 md:text-6xl"
              >
                {group.name}
              </h3>

              <p
                class="mt-3 font-mono text-[8px] uppercase leading-relaxed tracking-[0.12em] opacity-45"
              >
                {group.category}
              </p>
            </div>
          </div>

          <div
            class="flex flex-wrap gap-2 md:max-w-[60%] md:justify-end"
          >
            {#each group.skills as skill}
              <span
                class="border border-white/15 px-3 py-2 font-mono text-[8px] uppercase tracking-[0.08em] text-white/65 transition-colors group-hover:border-[#172238]/20 group-hover:text-[#172238]/75"
              >
                {skill}
              </span>
            {/each}
          </div>
        </div>
      {/each}
    </div>

    <!-- CREATIVE TOOLS AS A SECONDARY SKILL -->

    <div class="mx-auto mt-24 max-w-7xl px-6 md:px-12">
      <div class="reveal mb-8">
        <div
          class="font-mono text-[9px] uppercase tracking-[0.2em] text-white/40"
        >
          / Additional Creative Tools
        </div>

        <h3 class="mt-4 font-playfair text-3xl md:text-5xl">
          A visual perspective beyond code.
        </h3>

        {#if $language === 'id'}
          <p class="mt-4 max-w-xl text-sm leading-relaxed text-white/45">
            Pengalaman kreatif sebagai kemampuan pendukung untuk memahami
            visual, komposisi, dan presentasi produk digital.
          </p>
        {:else}
          <p class="mt-4 max-w-xl text-sm leading-relaxed text-white/45">
            Creative experience as a complementary skill for understanding
            visuals, composition, and digital product presentation.
          </p>
        {/if}
      </div>

      <div class="flex flex-wrap gap-2">
        {#each creativeTools as tool}
          <span
            class="border border-white/15 px-4 py-3 font-mono text-[9px] uppercase tracking-[0.1em] text-white/60"
          >
            {tool}
          </span>
        {/each}
      </div>
    </div>
  </section>

  <!-- =====================================================
       EDUCATION
  ====================================================== -->

  <section
    class="px-6 py-32 md:px-12 md:py-40"
    style="background: linear-gradient(to bottom, #DDE2E7 0%, #EEF0F2 60%, #F5F3EF 100%);"
  >
    <div class="mx-auto grid max-w-7xl gap-16 md:grid-cols-3">
      <div class="reveal">
        <div
          class="font-mono text-[9px] uppercase tracking-[0.2em] text-[#172238]/45"
        >
          / Education
        </div>

        <h2 class="mt-5 font-bebas text-7xl leading-[0.8] md:text-9xl">
          STUDY
        </h2>
      </div>

      <div class="md:col-span-2">
        {#each education as item}
          <div
            class="education-item reveal border-b border-[#172238]/15 py-8 first:pt-0"
          >
            <span class="font-mono text-[9px] text-[#172238]/40">
              {item.period}
            </span>

            <h3 class="mt-4 font-playfair text-3xl md:text-5xl">
              {item.institution}
            </h3>

            <p
              class="mt-3 font-mono text-[10px] uppercase tracking-[0.12em] text-[#172238]/50"
            >
              {item.program}
            </p>
          </div>
        {/each}
      </div>
    </div>
  </section>

  <!-- =====================================================
       BEYOND THE CODE
  ====================================================== -->

  <section
    class="relative overflow-hidden px-6 py-32 md:px-12 md:py-48"
    style="background: linear-gradient(to bottom, #EEF0F2 0%, #C8D1DB 52%, #AAB7C5 100%);"
  >
    <div
      class="background-word pointer-events-none absolute -right-10 top-1/3 whitespace-nowrap font-bebas text-[22vw] leading-none text-[#172238]/[0.05]"
    >
      BEYOND
    </div>

    <div class="relative z-10 mx-auto max-w-7xl">
      <div class="reveal grid gap-16 md:grid-cols-2 md:items-end">
        <div>
          <div
            class="font-mono text-[9px] uppercase tracking-[0.2em] text-[#172238]/45"
          >
            / Beyond the Code
          </div>

          <h2 class="mt-6 font-playfair text-6xl leading-[0.9] md:text-8xl">
            Still curious
            <span class="text-[#172238]/30">about other things too.</span>
          </h2>
        </div>

        <div
          class="max-w-xl text-base leading-relaxed text-[#172238]/60 md:text-lg"
        >
          {#if $language === 'id'}
            <p>
              Di luar web development, saya juga tertarik pada motion graphics,
              video editing, PC hardware, anime, dan game.
            </p>

            <p class="mt-6">
              Latar belakang kreatif membantu saya memperhatikan detail visual
              dan presentasi. Saya berharap dapat memadukannya dengan kemampuan
              teknis untuk membangun pengalaman web yang lebih baik.
            </p>
          {:else}
            <p>
              Outside web development, I'm also interested in motion graphics,
              video editing, PC hardware, anime, and games.
            </p>

            <p class="mt-6">
              My creative background helps me pay attention to visual details
              and presentation. I hope to combine that perspective with my
              technical skills to build better web experiences.
            </p>
          {/if}
        </div>
      </div>
    </div>
  </section>

  <!-- =====================================================
       CLOSING
  ====================================================== -->

  <section
    class="closing-section relative flex min-h-[72vh] items-center overflow-hidden px-6 py-32 md:px-12 md:py-44"
    style="background: linear-gradient(to bottom, #AAB7C5 0%, #DCE1E6 42%, #EEF0F2 100%);"
  >
    <div
      class="closing-word pointer-events-none absolute -bottom-8 left-[-4vw] select-none whitespace-nowrap font-bebas text-[20vw] leading-none text-[#172238]/[0.045]"
    >
      KEEP BUILDING
    </div>

    <div class="closing-content relative z-10 mx-auto w-full max-w-7xl">
      <div
        class="font-mono text-[9px] uppercase tracking-[0.15em] text-[#172238]/40"
      >
        / Until next time
      </div>

      <div class="mt-12">
        <h2
          class="font-playfair text-[8vw] leading-[0.95] tracking-[-0.025em] md:text-[7vw]"
        >
          雪の下にも、情熱がある。
        </h2>

        <p
          class="mt-8 font-bebas text-2xl tracking-[0.02em] text-[#172238]/70 md:text-4xl"
        >
          EVEN BENEATH THE SNOW, PASSION BURNS.
        </p>
      </div>

      <div
        class="mt-20 flex items-center gap-3 font-mono text-[8px] uppercase tracking-[0.15em] text-[#172238]/40"
      >
        <span class="h-2 w-2 rounded-full bg-[#18A9E8]"></span>
        More to come
      </div>
    </div>
  </section>
</main>