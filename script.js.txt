// Progressive enhancement JS: menu toggle, theme toggle, form validation, reveal on scroll, service worker registration

document.addEventListener('DOMContentLoaded', () => {
  // Year footer
  const yearEl = document.getElementById('year');
  if (yearEl) yearEl.textContent = new Date().getFullYear();

  // Mobile nav toggle
  const navToggle = document.getElementById('nav-toggle');
  const navList = document.getElementById('nav-list');
  if (navToggle && navList) {
    navToggle.addEventListener('click', () => {
      const expanded = navToggle.getAttribute('aria-expanded') === 'true';
      navToggle.setAttribute('aria-expanded', String(!expanded));
      navList.setAttribute('aria-hidden', String(expanded));
    });
  }

  // Theme toggle (persist)
  const themeToggle = document.getElementById('theme-toggle');
  const root = document.documentElement;
  const THEME_KEY = 'site-theme';
  const applyTheme = (name) => {
    if (name === 'dark') root.classList.add('dark');
    else root.classList.remove('dark');
    themeToggle?.setAttribute('aria-pressed', String(name === 'dark'));
  };
  const saved = localStorage.getItem(THEME_KEY);
  if (saved) applyTheme(saved);
  themeToggle?.addEventListener('click', () => {
    const dark = root.classList.toggle('dark');
    localStorage.setItem(THEME_KEY, dark ? 'dark' : 'light');
    themeToggle.setAttribute('aria-pressed', String(dark));
  });

  // Form validation (client-side)
  const form = document.getElementById('contact-form');
  const status = document.getElementById('form-status');
  if (form) {
    form.addEventListener('submit', (e) => {
      e.preventDefault();
      if (!form.checkValidity()) {
        form.reportValidity();
        return;
      }
      // Simulate async submit
      if (status) {
        status.hidden = false;
        status.textContent = 'Sending…';
      }
      setTimeout(() => {
        if (status) {
          status.textContent = 'Thanks — message sent!';
        }
        form.reset();
      }, 900);
    });
  }

  // IntersectionObserver for reveal on scroll (and lazy load additional actions)
  const observer = new IntersectionObserver((entries) => {
    entries.forEach(entry => {
      if (entry.isIntersecting) {
        entry.target.classList.add('is-visible');
        observer.unobserve(entry.target);
      }
    });
  }, { rootMargin: '0px 0px -8% 0px', threshold: 0.05 });

  document.querySelectorAll('.reveal').forEach(el => observer.observe(el));

  // Register service worker
  if ('serviceWorker' in navigator) {
    navigator.serviceWorker.register('service-worker.js').catch(err => {
      // registration failure is non-fatal
      console.warn('SW registration failed:', err);
    });
  }
});