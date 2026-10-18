<script lang="ts">
  import { onMount } from 'svelte';
  import { gsap } from 'gsap';
  import { language } from '$lib/stores/language';

  let emailLink: HTMLAnchorElement | undefined = $state();

  const socials = [
    {
      name: 'Instagram',
      href: 'https://www.instagram.com/rizalamri.rar/'
    },
    {
      name: 'Youtube',
      href: 'https://www.youtube.com/@remka7721'
    },
    {
      name: 'Tiktok',
      href: 'https://www.tiktok.com/@rushia_remka'
    },
    {
      name: 'LinkedIn',
      href: 'https://www.linkedin.com/in/rizal-amri-rozaqi/'
    },
    {
      name: 'GitHub',
      href: 'https://github.com/rizalamrirozaqi/'
    }
  ];

  onMount(() => {
    let disposed = false;
    let ctx: ReturnType<typeof gsap.context> | undefined;
    let emailEnterHandler: (() => void) | null = null;
    let emailLeaveHandler: (() => void) | null = null;
    let refreshHandler: (() => void) | undefined;

    const refreshTimers: ReturnType<typeof setTimeout>[] = [];

    const initializeAnimations = async () => {
      const { ScrollTrigger } = await import('gsap/ScrollTrigger');

      if (disposed) return;

      gsap.registerPlugin(ScrollTrigger);

      ctx = gsap.context(() => {
        // =====================================================
        // HERO
        // =====================================================

        const heroTl = gsap.timeline({
          defaults: {
            ease: 'power4.out'
          }
        });

        heroTl
          .fromTo(
            '.contact-meta',
            {
              opacity: 0,
              y: 20
            },
            {
              opacity: 1,
              y: 0,
              duration: 0.7
            }
          )
          .fromTo(
            '.contact-title-line',
            {
              yPercent: 110
            },
            {
              yPercent: 0,
              duration: 1,
              stagger: 0.08
            },
            '-=0.4'
          )
          .fromTo(
            '.contact-intro',
            {
              opacity: 0,
              y: 20
            },
            {
              opacity: 1,
              y: 0,
              duration: 0.75
            },
            '-=0.45'
          );

        // =====================================================
        // HERO BACKGROUND PARALLAX
        // =====================================================

        gsap.to('.contact-bg-word', {
          xPercent: -8,
          ease: 'none',
          scrollTrigger: {
            trigger: '.hero-section',
            start: 'top top',
            end: 'bottom top',
            scrub: 1
          }
        });

        // =====================================================
        // GENERAL REVEAL
        // =====================================================

        gsap.utils.toArray<HTMLElement>('.reveal').forEach((element) => {
          gsap.fromTo(
            element,
            {
              opacity: 0,
              y: 35
            },
            {
              opacity: 1,
              y: 0,
              duration: 0.75,
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
        // EMAIL
        // =====================================================

        if (emailLink) {
          gsap.fromTo(
            emailLink,
            {
              opacity: 0,
              scale: 0.97
            },
            {
              opacity: 1,
              scale: 1,
              duration: 0.8,
              ease: 'power3.out',
              scrollTrigger: {
                trigger: emailLink,
                start: 'top 85%',
                once: true
              }
            }
          );
        }

        // =====================================================
        // SOCIAL LINKS
        // =====================================================

        gsap.utils.toArray<HTMLElement>('.social-item').forEach((item, index) => {
          gsap.fromTo(
            item,
            {
              opacity: 0,
              x: -20
            },
            {
              opacity: 1,
              x: 0,
              duration: 0.65,
              delay: index * 0.05,
              ease: 'power3.out',
              scrollTrigger: {
                trigger: item,
                start: 'top 90%',
                once: true
              }
            }
          );
        });

        // =====================================================
        // AVAILABILITY WORD
        // =====================================================

        gsap.to('.availability-word', {
          xPercent: 8,
          ease: 'none',
          scrollTrigger: {
            trigger: '.availability-section',
            start: 'top bottom',
            end: 'bottom top',
            scrub: 1
          }
        });

        // =====================================================
        // CLOSING WORD
        // =====================================================

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
      // EMAIL HOVER
      // =====================================================

      if (emailLink) {
        const arrow = emailLink.querySelector<HTMLElement>('.email-arrow');

        emailEnterHandler = () => {
          gsap.to(emailLink, {
            x: 10,
            duration: 0.4,
            ease: 'power3.out',
            overwrite: 'auto'
          });

          if (arrow) {
            gsap.to(arrow, {
              x: 6,
              y: -6,
              rotation: -10,
              duration: 0.35,
              ease: 'power3.out',
              overwrite: 'auto'
            });
          }
        };

        emailLeaveHandler = () => {
          gsap.to(emailLink, {
            x: 0,
            duration: 0.45,
            ease: 'power3.out',
            overwrite: 'auto'
          });

          if (arrow) {
            gsap.to(arrow, {
              x: 0,
              y: 0,
              rotation: 0,
              duration: 0.4,
              ease: 'power3.out',
              overwrite: 'auto'
            });
          }
        };

        emailLink.addEventListener('mouseenter', emailEnterHandler);
        emailLink.addEventListener('mouseleave', emailLeaveHandler);
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
      console.error('Failed to initialize contact animations:', error);
    });

    return () => {
      disposed = true;

      if (emailLink && emailEnterHandler) {
        emailLink.removeEventListener('mouseenter', emailEnterHandler);
      }

      if (emailLink && emailLeaveHandler) {
        emailLink.removeEventListener('mouseleave', emailLeaveHandler);
      }

      if (refreshHandler) {
        window.removeEventListener('load', refreshHandler);
      }

      refreshTimers.forEach((timer) => window.clearTimeout(timer));

      ctx?.revert();
    };
  });
</script>

<svelte:head>
  <title>Contact — Rizal Amri Rozaqi | Web Developer</title>

  <meta
    name="description"
    content="Contact Rizal Amri Rozaqi about Web Developer opportunities, website projects, and web development collaborations."
  />
</svelte:head>

<main
  class="overflow-hidden text-[#172238] selection:bg-[#18A9E8] selection:text-[#EEF0F2]"
>
  <!-- =======================================================
       01 — CONTACT HERO
  ======================================================== -->

  <section
    class="hero-section relative flex min-h-screen flex-col justify-end overflow-hidden bg-[#EEF0F2] px-6 pb-16 pt-28 md:px-12 md:pb-20"
  >
    <div
      class="contact-bg-word pointer-events-none absolute right-[-5vw] top-[16%] select-none whitespace-nowrap font-bebas text-[18vw] leading-none text-[#172238]/[0.025]"
    >
      CONTACT
    </div>

    <div class="relative z-10 mx-auto w-full max-w-7xl">
      <div
        class="contact-meta font-mono text-[9px] uppercase tracking-[0.16em] text-[#172238]/45"
      >
        / Contact — Web Developer Portfolio
      </div>

      <div class="mt-8 overflow-hidden">
        <h1
          class="contact-title-line font-bebas text-[20vw] leading-[0.72] tracking-[-0.035em] md:text-[13vw]"
        >
          LET'S
        </h1>
      </div>

      <div class="overflow-hidden">
        <h2
          class="contact-title-line font-bebas text-[20vw] leading-[0.72] tracking-[-0.035em] text-[#172238]/25 md:text-[13vw]"
        >
          TALK.
        </h2>
      </div>

      <div
        class="contact-intro mt-10 grid gap-10 md:grid-cols-[1fr_auto] md:items-end"
      >
        <div class="max-w-xl">
          {#if $language === 'id'}
            <p class="text-base leading-relaxed text-[#172238]/60 md:text-lg">
              Sedang mencari Web Developer, ingin berdiskusi tentang proyek,
              atau punya ide yang perlu diwujudkan menjadi website? Saya terbuka
              untuk kesempatan kerja dan kolaborasi di bidang pengembangan web.
            </p>
          {:else}
            <p class="text-base leading-relaxed text-[#172238]/60 md:text-lg">
              Looking for a Web Developer, have a website project, or an idea
              you'd like to bring to life? Feel free to reach out about job
              opportunities and web development collaborations.
            </p>
          {/if}
        </div>

        <div
          class="font-mono text-[8px] uppercase tracking-[0.15em] text-[#172238]/40"
        >
          <span class="mr-2 inline-block h-2 w-2 rounded-full bg-[#18A9E8]"></span>
          Open to Web Developer Opportunities
        </div>
      </div>
    </div>
  </section>

  <!-- =======================================================
       02 — EMAIL
  ======================================================== -->

  <section
    class="relative px-6 py-28 md:px-12 md:py-40"
    style="background: linear-gradient(135deg, #AEB7C3 0%, #8C99A9 100%);"
  >
    <div class="mx-auto max-w-7xl">
      <div
        class="reveal font-mono text-[9px] uppercase tracking-[0.16em] text-[#EEF0F2]/55"
      >
        / 01 — Direct contact
      </div>

      <div class="mt-10">
        <a
          bind:this={emailLink}
          href="mailto:rizalamrirozaqi@gmail.com"
          data-cursor-hover
          class="group inline-flex max-w-full items-end gap-4 overflow-hidden text-[#EEF0F2]"
        >
          <span
            class="whitespace-nowrap font-playfair text-[clamp(1.6rem,7vw,5.5vw)] leading-[0.9] tracking-[-0.025em]"
          >
            rizalamrirozaqi@gmail.com
          </span>

          <span
            class="email-arrow mb-2 shrink-0 font-mono text-xl text-[#18A9E8] md:text-3xl"
          >
            ↗
          </span>
        </a>
      </div>

      <div class="reveal mt-8 max-w-md">
        {#if $language === 'id'}
          <p class="text-sm leading-relaxed text-[#EEF0F2]/55">
            Untuk rekrutmen, diskusi proyek website, maupun kolaborasi
            di bidang pengembangan web, kamu bisa menghubungi saya melalui email.
          </p>
        {:else}
          <p class="text-sm leading-relaxed text-[#EEF0F2]/55">
            For recruitment, website projects, or web development
            collaborations, feel free to contact me by email.
          </p>
        {/if}
      </div>
    </div>
  </section>

  <!-- =======================================================
       03 — SOCIAL
  ======================================================== -->

  <section
    class="px-6 py-28 md:px-12 md:py-40 bg-[#172238] text-[#EEF0F2]"
  >
    <div class="mx-auto max-w-7xl">
      <div class="grid gap-16 md:grid-cols-[0.65fr_1.35fr]">
        <div class="reveal">
          <div
            class="font-mono text-[9px] uppercase tracking-[0.16em] text-white/40"
          >
            / 02 — Social
          </div>

          <h2
            class="mt-6 font-bebas text-[15vw] leading-[0.75] tracking-[-0.03em] md:text-[9vw]"
          >
            FIND ME
          </h2>
        </div>

        <div>
          {#each socials as social}
            <a
              href={social.href}
              target="_blank"
              rel="noreferrer"
              data-cursor-hover
              class="social-item group flex items-center justify-between border-b border-white/15 py-7"
            >
              <span
                class="font-playfair text-4xl transition-transform duration-500 group-hover:translate-x-3 md:text-6xl"
              >
                {social.name}
              </span>

              <span class="font-mono text-[9px] text-[#18A9E8]">
                ↗
              </span>
            </a>
          {/each}
        </div>
      </div>
    </div>
  </section>

  <!-- =======================================================
       04 — AVAILABILITY
  ======================================================== -->

  <section
    class="availability-section relative overflow-hidden px-6 py-32 md:px-12 md:py-44 bg-[#DDE2E7]"
  >
    <div
      class="availability-word pointer-events-none absolute -right-[5vw] top-[28%] select-none whitespace-nowrap font-bebas text-[21vw] leading-none text-[#172238]/[0.045]"
    >
      HELLO
    </div>

    <div class="relative z-10 mx-auto max-w-7xl">
      <div class="grid gap-16 md:grid-cols-2 md:items-end">
        <div class="reveal">
          <div
            class="font-mono text-[9px] uppercase tracking-[0.16em] text-[#172238]/45"
          >
            / 03 — Availability
          </div>

          <h2
            class="mt-6 font-playfair text-6xl leading-[0.88] md:text-8xl"
          >
            Open to
            <span class="text-[#172238]/25">
              Web Developer Roles.
            </span>
          </h2>
        </div>

        <div
          class="reveal max-w-xl text-base leading-relaxed text-[#172238]/55 md:text-lg"
        >
          {#if $language === 'id'}
            <p>
              Saya terbuka untuk kesempatan sebagai Web Developer,
              terutama untuk membangun antarmuka responsif,
              mengembangkan fitur website, dan terus meningkatkan
              kualitas solusi digital.
            </p>

            <p class="mt-6">
              Saya ingin berkontribusi melalui kemampuan yang saya miliki,
              bekerja sama dengan tim, dan terus belajar untuk menghasilkan
              website yang fungsional serta mudah digunakan.
            </p>
          {:else}
            <p>
              I'm open to Web Developer opportunities where I can build
              responsive interfaces, develop website features, and keep
              improving the quality of digital products.
            </p>

            <p class="mt-6">
              I aim to contribute my skills, collaborate with a team,
              and keep learning while helping build functional and
              user-friendly websites.
            </p>
          {/if}
        </div>
      </div>

      <div
        class="reveal mt-20 flex flex-wrap gap-x-8 gap-y-3 font-mono text-[9px] uppercase tracking-[0.12em] text-[#172238]/45"
      >
        <span>Web Development</span>
        <span>Front-End Development</span>
        <span>UI/UX Design</span>
        <span>Responsive Design</span>
      </div>
    </div>
  </section>

  <!-- =======================================================
       05 — CLOSING PHRASE
  ======================================================== -->

  <section
    class="closing-section relative flex min-h-[72vh] items-center overflow-hidden px-6 py-32 md:px-12 md:py-44 bg-[#EEF0F2]"
  >
    <div
      class="closing-word pointer-events-none absolute -bottom-8 left-[-4vw] select-none whitespace-nowrap font-bebas text-[20vw] leading-none text-[#172238]/[0.035]"
    >
      PASSION
    </div>

    <div class="closing-content relative z-10 mx-auto w-full max-w-7xl">
      <div
        class="font-mono text-[9px] uppercase tracking-[0.15em] text-[#172238]/40"
      >
        / Until next time
      </div>

      <div class="mt-12">
        <h2
          class="font-serif text-[8vw] leading-[0.95] tracking-[-0.025em] md:text-[7vw]"
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