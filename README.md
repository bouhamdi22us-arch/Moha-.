<!DOCTYPE html>
<html lang="en" class="no-js">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=5.0" />
  <title>MOHA — Culinary Sanctum & Fine Dining</title>
  <meta name="description" content="An immersive cinematic fine dining experience. Architected taste, contemporary atmosphere, seasonal culinary mastery." />

  <!-- Fonts -->
  <link rel="preconnect" href="https://fonts.googleapis.com" />
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
  <link href="https://fonts.googleapis.com/css2?family=Cormorant+Garamond:ital,wght@0,300;0,400;0,600;1,400&family=Plus+Jakarta+Sans:wght@300;400;500;600&family=Syne:wght@400;700;800&display=swap" rel="stylesheet" />

  <!-- External CDN Libraries -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.5/gsap.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.5/ScrollTrigger.min.js"></script>
  <script src="https://unpkg.com/lenis@1.1.9/dist/lenis.min.js"></script>

  <style>
    /* ==========================================================================
       01. DESIGN SYSTEM & CSS VARIABLES
       ========================================================================== */
    :root {
      --bg-dark: #0c0b0a;
      --bg-card: #151412;
      --bg-elevated: #1e1c19;
      --text-main: #f4f0ea;
      --text-muted: #a39e93;
      --text-dim: #5c5850;
      --accent-terracotta: #bf573f;
      --accent-burnt: #8c3a27;
      --accent-sand: #d1ba9e;
      --accent-earth: #2c332b;
      --border-subtle: rgba(244, 240, 234, 0.08);
      --border-active: rgba(191, 87, 63, 0.4);

      --font-serif: 'Cormorant Garamond', Georgia, serif;
      --font-sans: 'Plus Jakarta Sans', -apple-system, BlinkMacSystemFont, sans-serif;
      --font-display: 'Syne', sans-serif;

      --ease-out-expo: cubic-bezier(0.16, 1, 0.3, 1);
      --ease-in-out-smooth: cubic-bezier(0.77, 0, 0.175, 1);
      --ease-apple: cubic-bezier(0.23, 1, 0.32, 1);
      --ease-drawer: cubic-bezier(0.32, 0.72, 0, 1);

      --nav-height: 80px;
    }

    /* Reset & Base Styles */
    *, *::before, *::after {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
    }

    html, body {
      width: 100%;
      height: 100%;
      background-color: var(--bg-dark);
      color: var(--text-main);
      font-family: var(--font-sans);
      font-weight: 300;
      overflow-x: hidden;
      -webkit-font-smoothing: antialiased;
      -moz-osx-font-smoothing: grayscale;
    }

    /* Selection */
    ::selection {
      background-color: var(--accent-terracotta);
      color: var(--text-main);
    }

    a {
      color: inherit;
      text-decoration: none;
    }

    button {
      background: none;
      border: none;
      color: inherit;
      font: inherit;
      cursor: pointer;
    }

    img {
      max-width: 100%;
      height: auto;
      display: block;
      object-fit: cover;
    }

    /* WebGL Canvas */
    #webgl-canvas {
      position: fixed;
      top: 0;
      left: 0;
      width: 100vw;
      height: 100vh;
      z-index: 0;
      pointer-events: none;
      opacity: 0.85;
      transition: opacity 1s var(--ease-out-expo);
    }

    /* Custom Cursor (Desktop Only) */
    #cursor-follower {
      position: fixed;
      top: 0;
      left: 0;
      width: 32px;
      height: 32px;
      border: 1px solid rgba(244, 240, 234, 0.4);
      border-radius: 50%;
      pointer-events: none;
      z-index: 9999;
      transform: translate(-50%, -50%);
      display: flex;
      align-items: center;
      justify-content: center;
      transition: width 0.3s var(--ease-apple), height 0.3s var(--ease-apple), border-color 0.3s var(--ease-apple), background-color 0.3s var(--ease-apple);
    }

    #cursor-dot {
      width: 4px;
      height: 4px;
      background-color: var(--text-main);
      border-radius: 50%;
      pointer-events: none;
    }

    #cursor-text {
      font-family: var(--font-display);
      font-size: 8px;
      font-weight: 700;
      letter-spacing: 0.1em;
      text-transform: uppercase;
      color: var(--bg-dark);
      opacity: 0;
      transition: opacity 0.2s ease;
      white-space: nowrap;
    }

    /* Cursor States */
    #cursor-follower.hovered {
      width: 64px;
      height: 64px;
      background-color: rgba(244, 240, 234, 0.95);
      border-color: transparent;
    }

    #cursor-follower.hovered #cursor-dot {
      display: none;
    }

    #cursor-follower.hovered #cursor-text {
      opacity: 1;
    }

    @media (hover: none) or (pointer: coarse) {
      #cursor-follower { display: none !important; }
    }

    /* ==========================================================================
       02. CINEMATIC LOADER
       ========================================================================== */
    #loader {
      position: fixed;
      inset: 0;
      background-color: var(--bg-dark);
      z-index: 10000;
      display: flex;
      flex-direction: column;
      justify-content: space-between;
      padding: 40px;
      pointer-events: all;
    }

    .loader-title {
      font-family: var(--font-serif);
      font-size: clamp(2rem, 5vw, 4rem);
      font-weight: 300;
      letter-spacing: 0.2em;
      text-transform: uppercase;
    }

    .loader-bottom {
      display: flex;
      justify-content: space-between;
      align-items: flex-end;
    }

    .loader-counter {
      font-family: var(--font-display);
      font-size: clamp(4rem, 12vw, 10rem);
      font-weight: 800;
      line-height: 0.8;
      color: var(--text-dim);
    }

    .loader-status {
      font-size: 0.85rem;
      letter-spacing: 0.15em;
      text-transform: uppercase;
      color: var(--accent-sand);
    }

    /* ==========================================================================
       03. GLOBAL NAVIGATION & HEADER
       ========================================================================== */
    header {
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      height: var(--nav-height);
      display: flex;
      align-items: center;
      justify-content: space-between;
      padding: 0 5vw;
      z-index: 1000;
      mix-blend-mode: difference;
      transition: transform 0.4s var(--ease-apple);
    }

    .brand-logo {
      font-family: var(--font-serif);
      font-size: 1.8rem;
      font-weight: 400;
      letter-spacing: 0.3em;
      text-transform: uppercase;
      color: #fff;
    }

    nav.desktop-nav {
      display: flex;
      align-items: center;
      gap: 2.5rem;
    }

    .nav-link {
      font-size: 0.8rem;
      font-weight: 500;
      letter-spacing: 0.2em;
      text-transform: uppercase;
      color: #fff;
      position: relative;
      padding: 8px 0;
      transition: opacity 0.3s ease;
    }

    .nav-link::after {
      content: '';
      position: absolute;
      bottom: 0;
      left: 0;
      width: 100%;
      height: 1px;
      background-color: #fff;
      transform: scaleX(0);
      transform-origin: right;
      transition: transform 0.4s var(--ease-out-expo);
    }

    .nav-link:hover::after,
    .nav-link.active::after {
      transform: scaleX(1);
      transform-origin: left;
    }

    .btn-reserve-nav {
      border: 1px solid rgba(255, 255, 255, 0.4);
      padding: 10px 22px;
      border-radius: 40px;
      font-size: 0.75rem;
      letter-spacing: 0.2em;
      text-transform: uppercase;
      color: #fff;
      transition: background-color 0.3s ease, border-color 0.3s ease, transform 0.2s var(--ease-apple);
    }

    .btn-reserve-nav:hover {
      background-color: #fff;
      color: #000;
    }

    .mobile-burger {
      display: none;
      flex-direction: column;
      gap: 6px;
      padding: 10px;
      z-index: 1002;
    }

    .burger-line {
      width: 28px;
      height: 1px;
      background-color: #fff;
      transition: transform 0.3s ease, opacity 0.3s ease;
    }

    /* Mobile Nav Drawer */
    #mobile-drawer {
      position: fixed;
      inset: 0;
      background-color: var(--bg-dark);
      z-index: 1001;
      display: flex;
      flex-direction: column;
      justify-content: center;
      padding: 10vw;
      transform: translateY(-100%);
      transition: transform 0.6s var(--ease-drawer);
    }

    #mobile-drawer.open {
      transform: translateY(0);
    }

    .mobile-nav-links {
      display: flex;
      flex-direction: column;
      gap: 2rem;
    }

    .mobile-nav-link {
      font-family: var(--font-serif);
      font-size: 2.5rem;
      text-transform: uppercase;
      letter-spacing: 0.1em;
      color: var(--text-main);
    }

    /* ==========================================================================
       04. SINGLE FILE PAGE ENGINE (VIRTUAL PAGES CONTAINER)
       ========================================================================== */
    #page-stage {
      position: relative;
      width: 100%;
      min-height: 100vh;
      z-index: 1;
    }

    .virtual-page {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      min-height: 100vh;
      opacity: 0;
      visibility: hidden;
      pointer-events: none;
      padding-top: var(--nav-height);
      transition: opacity 0.6s var(--ease-apple), visibility 0.6s ease;
    }

    .virtual-page.active-page {
      position: relative;
      opacity: 1;
      visibility: visible;
      pointer-events: all;
      z-index: 2;
    }

    /* Page Overlay Transition Shield */
    #transition-curtain {
      position: fixed;
      inset: 0;
      background-color: var(--bg-dark);
      z-index: 999;
      pointer-events: none;
      clip-path: inset(100% 0 0 0);
    }

    /* ==========================================================================
       05. PAGE 1 — HOME
       ========================================================================== */
    #home {
      height: 100vh;
      display: flex;
      flex-direction: column;
      justify-content: space-between;
      padding: calc(var(--nav-height) + 40px) 5vw 5vw 5vw;
      overflow: hidden;
    }

    .hero-bg-wrapper {
      position: absolute;
      inset: 0;
      z-index: -1;
      overflow: hidden;
    }

    .hero-bg-img {
      width: 100%;
      height: 100%;
      object-fit: cover;
      filter: brightness(0.45) contrast(1.1);
      transform: scale(1.05);
      transition: transform 10s ease-out;
    }

    .hero-center {
      margin: auto 0;
    }

    .hero-tag {
      font-size: 0.85rem;
      letter-spacing: 0.3em;
      text-transform: uppercase;
      color: var(--accent-sand);
      margin-bottom: 1.5rem;
      display: block;
    }

    .hero-title {
      font-family: var(--font-serif);
      font-size: clamp(3.5rem, 11vw, 10rem);
      font-weight: 300;
      line-height: 0.9;
      letter-spacing: -0.02em;
      text-transform: uppercase;
    }

    .hero-title span {
      display: block;
      font-style: italic;
      margin-left: 2vw;
      color: var(--accent-sand);
    }

    .hero-bottom {
      display: grid;
      grid-template-columns: 1fr 1fr;
      align-items: flex-end;
      gap: 2rem;
    }

    .hero-subtext {
      max-width: 420px;
      font-size: 0.95rem;
      line-height: 1.6;
      color: var(--text-muted);
    }

    .hero-cta-wrapper {
      justify-self: end;
    }

    .btn-primary {
      display: inline-flex;
      align-items: center;
      gap: 1rem;
      padding: 1.2rem 2.5rem;
      background-color: var(--accent-terracotta);
      color: var(--text-main);
      font-size: 0.8rem;
      font-weight: 600;
      letter-spacing: 0.2em;
      text-transform: uppercase;
      border-radius: 50px;
      transition: background-color 0.3s var(--ease-apple), transform 0.3s var(--ease-apple);
    }

    .btn-primary:hover {
      background-color: var(--accent-burnt);
      transform: translateY(-2px);
    }

    /* ==========================================================================
       06. PAGE 2 — EXPERIENCE
       ========================================================================== */
    #experience {
      padding: calc(var(--nav-height) + 60px) 5vw 100px 5vw;
    }

    .section-header {
      margin-bottom: 5rem;
    }

    .section-subtitle {
      font-size: 0.8rem;
      letter-spacing: 0.3em;
      text-transform: uppercase;
      color: var(--accent-terracotta);
      margin-bottom: 1rem;
      display: block;
    }

    .section-title {
      font-family: var(--font-serif);
      font-size: clamp(2.5rem, 6vw, 5rem);
      font-weight: 300;
      line-height: 1.05;
      max-width: 900px;
    }

    .exp-grid {
      display: grid;
      grid-template-columns: repeat(12, 1fr);
      gap: 2vw;
      align-items: center;
    }

    .exp-col-large {
      grid-column: span 7;
      position: relative;
    }

    .exp-col-small {
      grid-column: span 5;
      padding-left: 2vw;
    }

    .img-frame {
      position: relative;
      overflow: hidden;
      border-radius: 4px;
      background-color: var(--bg-card);
    }

    .img-frame img {
      width: 100%;
      height: 100%;
      transition: transform 1.2s var(--ease-out-expo);
    }

    .exp-col-large .img-frame {
      height: 70vh;
    }

    .exp-col-small .img-frame {
      height: 45vh;
      margin-bottom: 2rem;
    }

    .exp-text-block h3 {
      font-family: var(--font-serif);
      font-size: 2rem;
      margin-bottom: 1rem;
      font-weight: 400;
    }

    .exp-text-block p {
      font-size: 0.95rem;
      line-height: 1.7;
      color: var(--text-muted);
      margin-bottom: 1.5rem;
    }

    /* Architectural Strip */
    .arch-banner {
      margin-top: 100px;
      position: relative;
      height: 60vh;
      width: 100%;
      overflow: hidden;
    }

    .arch-banner img {
      width: 100%;
      height: 100%;
      filter: brightness(0.6);
    }

    .arch-banner-content {
      position: absolute;
      inset: 0;
      display: flex;
      align-items: center;
      justify-content: center;
      text-align: center;
      padding: 2vw;
    }

    .arch-banner-content h2 {
      font-family: var(--font-serif);
      font-size: clamp(2rem, 5vw, 4.5rem);
      font-weight: 300;
      max-width: 800px;
      font-style: italic;
    }

    /* ==========================================================================
       07. PAGE 3 — MENU
       ========================================================================== */
    #menu {
      padding: calc(var(--nav-height) + 60px) 5vw 120px 5vw;
      position: relative;
    }

    .menu-tabs {
      display: flex;
      gap: 2rem;
      margin-bottom: 4rem;
      border-bottom: 1px solid var(--border-subtle);
      padding-bottom: 1.5rem;
    }

    .menu-tab {
      font-size: 0.85rem;
      letter-spacing: 0.2em;
      text-transform: uppercase;
      color: var(--text-dim);
      transition: color 0.3s ease;
      position: relative;
    }

    .menu-tab.active {
      color: var(--text-main);
    }

    .menu-tab.active::after {
      content: '';
      position: absolute;
      bottom: -1.5rem;
      left: 0;
      width: 100%;
      height: 2px;
      background-color: var(--accent-terracotta);
    }

    .menu-list {
      display: flex;
      flex-direction: column;
      gap: 2.5rem;
      max-width: 1000px;
    }

    .menu-item {
      display: grid;
      grid-template-columns: 1fr auto;
      gap: 1.5rem;
      padding-bottom: 2rem;
      border-bottom: 1px solid var(--border-subtle);
      cursor: pointer;
      position: relative;
    }

    .menu-item-info h4 {
      font-family: var(--font-serif);
      font-size: clamp(1.5rem, 3vw, 2.2rem);
      font-weight: 400;
      margin-bottom: 0.4rem;
      transition: color 0.3s ease;
    }

    .menu-item:hover .menu-item-info h4 {
      color: var(--accent-sand);
    }

    .menu-item-info p {
      font-size: 0.85rem;
      color: var(--text-muted);
      max-width: 600px;
    }

    .menu-item-price {
      font-family: var(--font-display);
      font-size: 1.2rem;
      color: var(--accent-terracotta);
      font-weight: 700;
    }

    /* Floating Hover Image Preview */
    #menu-floating-preview {
      position: fixed;
      top: 0;
      left: 0;
      width: 280px;
      height: 360px;
      pointer-events: none;
      z-index: 100;
      overflow: hidden;
      border-radius: 4px;
      opacity: 0;
      transform: translate(-50%, -50%) scale(0.8);
      transition: opacity 0.3s ease, transform 0.4s var(--ease-out-expo);
    }

    #menu-floating-preview img {
      width: 100%;
      height: 100%;
      object-fit: cover;
    }

    /* ==========================================================================
       08. PAGE 4 — GALLERY
       ========================================================================== */
    #gallery {
      padding: calc(var(--nav-height) + 60px) 5vw 120px 5vw;
    }

    .gallery-grid {
      display: grid;
      grid-template-columns: repeat(12, 1fr);
      gap: 2vw;
      row-gap: 8vw;
    }

    .gallery-item {
      position: relative;
    }

    .gallery-item:nth-child(1) { grid-column: span 8; height: 70vh; }
    .gallery-item:nth-child(2) { grid-column: span 4; height: 50vh; margin-top: 10vh; }
    .gallery-item:nth-child(3) { grid-column: span 5; height: 60vh; }
    .gallery-item:nth-child(4) { grid-column: span 7; height: 80vh; margin-top: -5vh; }
    .gallery-item:nth-child(5) { grid-column: span 12; height: 75vh; }

    .gallery-item .img-frame {
      width: 100%;
      height: 100%;
    }

    .gallery-caption {
      position: absolute;
      bottom: -2rem;
      left: 0;
      font-size: 0.75rem;
      letter-spacing: 0.2em;
      text-transform: uppercase;
      color: var(--text-dim);
    }

    /* ==========================================================================
       09. PAGE 5 — STORY
       ========================================================================== */
    #story {
      padding: calc(var(--nav-height) + 60px) 5vw 120px 5vw;
    }

    .story-hero {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 4vw;
      margin-bottom: 8rem;
      align-items: center;
    }

    .story-title {
      font-family: var(--font-serif);
      font-size: clamp(3rem, 7vw, 6rem);
      font-weight: 300;
      line-height: 0.95;
    }

    .story-lead {
      font-size: 1.2rem;
      line-height: 1.7;
      color: var(--accent-sand);
    }

    .chef-quote-block {
      background-color: var(--bg-card);
      padding: 6vw;
      border-left: 2px solid var(--accent-terracotta);
      margin-bottom: 8rem;
    }

    .chef-quote {
      font-family: var(--font-serif);
      font-size: clamp(1.8rem, 4vw, 3rem);
      font-style: italic;
      line-height: 1.3;
      margin-bottom: 2rem;
    }

    .chef-name {
      font-size: 0.85rem;
      letter-spacing: 0.2em;
      text-transform: uppercase;
      color: var(--accent-sand);
    }

    /* ==========================================================================
       10. PAGE 6 — RESERVATION
       ========================================================================== */
    #reservation {
      min-height: 100vh;
      padding: calc(var(--nav-height) + 40px) 5vw 80px 5vw;
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
    }

    .res-container {
      width: 100%;
      max-width: 800px;
      text-align: center;
    }

    .res-title {
      font-family: var(--font-serif);
      font-size: clamp(3rem, 7vw, 5.5rem);
      font-weight: 300;
      margin-bottom: 1rem;
    }

    .res-subtitle {
      font-size: 0.95rem;
      color: var(--text-muted);
      margin-bottom: 4rem;
    }

    .res-form {
      display: flex;
      flex-direction: column;
      gap: 2.5rem;
      background-color: var(--bg-card);
      padding: 4vw;
      border: 1px solid var(--border-subtle);
      border-radius: 8px;
    }

    .form-group-grid {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
      gap: 2rem;
    }

    .form-field {
      display: flex;
      flex-direction: column;
      align-items: flex-start;
      gap: 0.5rem;
    }

    .form-field label {
      font-size: 0.75rem;
      letter-spacing: 0.2em;
      text-transform: uppercase;
      color: var(--accent-sand);
    }

    .form-field input,
    .form-field select {
      width: 100%;
      background: transparent;
      border: none;
      border-bottom: 1px solid var(--border-subtle);
      padding: 12px 0;
      color: var(--text-main);
      font-family: var(--font-sans);
      font-size: 1rem;
      outline: none;
      transition: border-color 0.3s ease;
    }

    .form-field input:focus,
    .form-field select:focus {
      border-color: var(--accent-terracotta);
    }

    /* ==========================================================================
       11. PAGE 7 — LOCATION
       ========================================================================== */
    #location {
      padding: calc(var(--nav-height) + 60px) 5vw 120px 5vw;
    }

    .location-grid {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 5vw;
      align-items: center;
    }

    .loc-info-card {
      display: flex;
      flex-direction: column;
      gap: 3rem;
    }

    .loc-block h4 {
      font-size: 0.8rem;
      letter-spacing: 0.2em;
      text-transform: uppercase;
      color: var(--accent-terracotta);
      margin-bottom: 0.8rem;
    }

    .loc-block p {
      font-family: var(--font-serif);
      font-size: 1.8rem;
      line-height: 1.3;
    }

    .map-representation {
      width: 100%;
      height: 60vh;
      background-color: var(--bg-card);
      border: 1px solid var(--border-subtle);
      position: relative;
      overflow: hidden;
      display: flex;
      align-items: center;
      justify-content: center;
    }

    .map-grid-pattern {
      position: absolute;
      inset: 0;
      background-image: linear-gradient(var(--border-subtle) 1px, transparent 1px),
                        linear-gradient(90deg, var(--border-subtle) 1px, transparent 1px);
      background-size: 40px 40px;
      opacity: 0.4;
    }

    .map-pin {
      position: relative;
      z-index: 2;
      display: flex;
      flex-direction: column;
      align-items: center;
      gap: 10px;
    }

    .pin-dot {
      width: 16px;
      height: 16px;
      background-color: var(--accent-terracotta);
      border-radius: 50%;
      box-shadow: 0 0 30px var(--accent-terracotta);
      animation: pulse 2s infinite;
    }

    @keyframes pulse {
      0% { transform: scale(1); opacity: 1; }
      50% { transform: scale(1.6); opacity: 0.5; }
      100% { transform: scale(1); opacity: 1; }
    }

    /* ==========================================================================
       12. RESPONSIVE BREAKPOINTS
       ========================================================================== */
    @media (max-width: 900px) {
      header nav.desktop-nav { display: none; }
      .mobile-burger { display: flex; }
      
      .hero-bottom { grid-template-columns: 1fr; }
      .hero-cta-wrapper { justify-self: start; }

      .exp-grid { grid-template-columns: 1fr; }
      .exp-col-large, .exp-col-small { grid-column: span 12; padding-left: 0; }
      .exp-col-large .img-frame { height: 45vh; }

      .gallery-grid { grid-template-columns: 1fr; }
      .gallery-item:nth-child(n) { grid-column: span 12; height: 45vh; margin-top: 0; }

      .story-hero { grid-template-columns: 1fr; }
      .location-grid { grid-template-columns: 1fr; }
    }

    /* Reduced Motion */
    @media (prefers-reduced-motion: reduce) {
      *, *::before, *::after {
        animation-duration: 0.01ms !important;
        animation-iteration-count: 1 !important;
        transition-duration: 0.01ms !important;
        scroll-behavior: auto !important;
      }
      #webgl-canvas { display: none; }
    }
  </style>
</head>
<body>

  <!-- 3D WebGL Canvas -->
  <canvas id="webgl-canvas"></canvas>

  <!-- Custom Cursor -->
  <div id="cursor-follower">
    <div id="cursor-dot"></div>
    <span id="cursor-text">View</span>
  </div>

  <!-- Page Transition Curtain -->
  <div id="transition-curtain"></div>

  <!-- Floating Menu Image Preview -->
  <div id="menu-floating-preview">
    <img id="menu-preview-img" src="" alt="Dish preview" />
  </div>

  <!-- Loader Sequence -->
  <div id="loader">
    <div class="loader-title">MOHA</div>
    <div class="loader-bottom">
      <div class="loader-status">Preparing Ambient Space...</div>
      <div class="loader-counter" id="loader-counter">00</div>
    </div>
  </div>

  <!-- Global Header Navigation -->
  <header id="main-header">
    <a href="#home" class="brand-logo" data-cursor="EXPLORE">MOHA</a>
    
    <nav class="desktop-nav">
      <a href="#experience" class="nav-link" data-cursor="VIEW">Experience</a>
      <a href="#menu" class="nav-link" data-cursor="VIEW">Menu</a>
      <a href="#gallery" class="nav-link" data-cursor="VIEW">Gallery</a>
      <a href="#story" class="nav-link" data-cursor="VIEW">Story</a>
      <a href="#location" class="nav-link" data-cursor="VIEW">Location</a>
      <a href="#reservation" class="btn-reserve-nav" data-cursor="BOOK">Book a Table</a>
    </nav>

    <button class="mobile-burger" id="burger-btn" aria-label="Toggle Navigation">
      <div class="burger-line"></div>
      <div class="burger-line"></div>
    </button>
  </header>

  <!-- Mobile Drawer -->
  <div id="mobile-drawer">
    <div class="mobile-nav-links">
      <a href="#home" class="mobile-nav-link">Home</a>
      <a href="#experience" class="mobile-nav-link">Experience</a>
      <a href="#menu" class="mobile-nav-link">Menu</a>
      <a href="#gallery" class="mobile-nav-link">Gallery</a>
      <a href="#story" class="mobile-nav-link">Story</a>
      <a href="#reservation" class="mobile-nav-link">Reservation</a>
      <a href="#location" class="mobile-nav-link">Location</a>
    </div>
  </div>

  <!-- PAGE STAGE (VIRTUAL PAGES) -->
  <main id="page-stage">

    <!-- ===================================================================
         VIRTUAL PAGE: HOME
         =================================================================== -->
    <section id="home" class="virtual-page active-page">
      <div class="hero-bg-wrapper">
        <img class="hero-bg-img" src="https://images.unsplash.com/photo-1550966871-3ed3cdb5ed0c?q=80&w=2070&auto=format&fit=crop" alt="MOHA Atmosphere" />
      </div>

      <div style="height: 10px;"></div> <!-- Spacer -->

      <div class="hero-center">
        <span class="hero-tag">Culinary Sanctum & Architectural Taste</span>
        <h1 class="hero-title">
          MOHA <span>Experience</span>
        </h1>
      </div>

      <div class="hero-bottom">
        <p class="hero-subtext">
          Where raw terracotta meets avant-garde gastronomy. An architectural fine dining journey suspended in contemporary elegance.
        </p>
        <div class="hero-cta-wrapper">
          <a href="#reservation" class="btn-primary" data-cursor="BOOK">
            <span>Reserve Your Table</span>
            <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M5 12h14M12 5l7 7-7 7"/></svg>
          </a>
        </div>
      </div>
    </section>

    <!-- ===================================================================
         VIRTUAL PAGE: EXPERIENCE
         =================================================================== -->
    <section id="experience" class="virtual-page">
      <div class="section-header">
        <span class="section-subtitle">Space & Atmosphere</span>
        <h2 class="section-title">An immersive physical realm designed to elevate sensory consciousness.</h2>
      </div>

      <div class="exp-grid">
        <div class="exp-col-large">
          <div class="img-frame">
            <img src="https://images.unsplash.com/photo-1517248135467-4c7edcad34c4?q=80&w=2070&auto=format&fit=crop" alt="Dining Room" />
          </div>
        </div>
        <div class="exp-col-small">
          <div class="img-frame">
            <img src="https://images.unsplash.com/photo-1559339352-11d035aa65de?q=80&w=1974&auto=format&fit=crop" alt="Table Setting" />
          </div>
          <div class="exp-text-block">
            <h3>Architectural Harmony</h3>
            <p>Every element inside MOHA—from the acoustics to the warm cast shadows—is meticulously sculpted. Charcoal plaster, raw stone, and flickering flame converge.</p>
          </div>
        </div>
      </div>

      <div class="arch-banner">
        <img src="https://images.unsplash.com/photo-1544025162-d76694265947?q=80&w=2069&auto=format&fit=crop" alt="Plating Detail" />
        <div class="arch-banner-content">
          <h2>"Food is the briefest art form; it exists only in the moment of consumption."</h2>
        </div>
      </div>
    </section>

    <!-- ===================================================================
         VIRTUAL PAGE: MENU
         =================================================================== -->
    <section id="menu" class="virtual-page">
      <div class="section-header">
        <span class="section-subtitle">Editorial Tasting</span>
        <h2 class="section-title">Curated Gastronomic Collections</h2>
      </div>

      <div class="menu-tabs">
        <button class="menu-tab active" data-category="starters">Starters</button>
        <button class="menu-tab" data-category="mains">Mains</button>
        <button class="menu-tab" data-category="desserts">Desserts</button>
        <button class="menu-tab" data-category="drinks">Mixology</button>
      </div>

      <div class="menu-list" id="menu-items-container">
        <!-- Rendered Dynamically via JavaScript -->
      </div>
    </section>

    <!-- ===================================================================
         VIRTUAL PAGE: GALLERY
         =================================================================== -->
    <section id="gallery" class="virtual-page">
      <div class="section-header">
        <span class="section-subtitle">Exhibition</span>
        <h2 class="section-title">Visual Moments & Texture</h2>
      </div>

      <div class="gallery-grid">
        <div class="gallery-item">
          <div class="img-frame">
            <img src="https://images.unsplash.com/photo-1555396273-367ea4eb4db5?q=80&w=1974&auto=format&fit=crop" alt="Interior Details" />
          </div>
          <span class="gallery-caption">I — The Sanctum Hall</span>
        </div>
        <div class="gallery-item">
          <div class="img-frame">
            <img src="https://images.unsplash.com/photo-1565299624946-b28f40a0ae38?q=80&w=1981&auto=format&fit=crop" alt="Culinary Creation" />
          </div>
          <span class="gallery-caption">II — Smoked Ember Cacio</span>
        </div>
        <div class="gallery-item">
          <div class="img-frame">
            <img src="https://images.unsplash.com/photo-1514362545857-3bc16c4c7d1b?q=80&w=2070&auto=format&fit=crop" alt="Mixology" />
          </div>
          <span class="gallery-caption">III — Botanical Elixir</span>
        </div>
        <div class="gallery-item">
          <div class="img-frame">
            <img src="https://images.unsplash.com/photo-1577219491135-ce391730fb2c?q=80&w=1977&auto=format&fit=crop" alt="Chef at work" />
          </div>
          <span class="gallery-caption">IV — Fire & Precision</span>
        </div>
        <div class="gallery-item">
          <div class="img-frame">
            <img src="https://images.unsplash.com/photo-1507048331197-7d4ac70811cf?q=80&w=1974&auto=format&fit=crop" alt="Ingredients" />
          </div>
          <span class="gallery-caption">V — Elemental Roots</span>
        </div>
      </div>
    </section>

    <!-- ===================================================================
         VIRTUAL PAGE: STORY
         =================================================================== -->
    <section id="story" class="virtual-page">
      <div class="story-hero">
        <h2 class="story-title">The Philosophy of MOHA</h2>
        <p class="story-lead">
          Founded on the principle that dining should mimic the reverence of ancient rituals, MOHA bridges heritage culinary methods with sculptural modernism.
        </p>
      </div>

      <div class="chef-quote-block">
        <p class="chef-quote">"We do not decorate plates. We distil nature’s violent beauty into unforgettable taste memory."</p>
        <span class="chef-name">Executive Chef & Founder — Moha</span>
      </div>
    </section>

    <!-- ===================================================================
         VIRTUAL PAGE: RESERVATION
         =================================================================== -->
    <section id="reservation" class="virtual-page">
      <div class="res-container">
        <h2 class="res-title">Your Table Awaits</h2>
        <p class="res-subtitle">Reservations open 30 days in advance. Intimate seating for an uninterrupted culinary journey.</p>

        <form class="res-form" id="booking-form" onsubmit="event.preventDefault(); alert('Reservation Request Received. We will contact you via email.');">
          <div class="form-group-grid">
            <div class="form-field">
              <label for="res-date">Date</label>
              <input type="date" id="res-date" required />
            </div>
            <div class="form-field">
              <label for="res-time">Time</label>
              <select id="res-time" required>
                <option value="18:00">18:00 — Early Seating</option>
                <option value="20:30">20:30 — Main Evening</option>
                <option value="22:00">22:00 — Late Nocturne</option>
              </select>
            </div>
            <div class="form-field">
              <label for="res-guests">Guests</label>
              <select id="res-guests" required>
                <option value="1">1 Person</option>
                <option value="2" selected>2 Persons</option>
                <option value="4">4 Persons</option>
                <option value="6">Private Table (6)</option>
              </select>
            </div>
          </div>

          <div class="form-field">
            <label for="res-name">Full Name</label>
            <input type="text" id="res-name" placeholder="E.g. Alexander Vance" required />
          </div>

          <button type="submit" class="btn-primary" style="align-self: center; margin-top: 1rem;" data-cursor="BOOK">
            Confirm Booking Request
          </button>
        </form>
      </div>
    </section>

    <!-- ===================================================================
         VIRTUAL PAGE: LOCATION
         =================================================================== -->
    <section id="location" class="virtual-page">
      <div class="section-header">
        <span class="section-subtitle">Sanctuary</span>
        <h2 class="section-title">Find Us</h2>
      </div>

      <div class="location-grid">
        <div class="loc-info-card">
          <div class="loc-block">
            <h4>Address</h4>
            <p>744 Ember Architecture Boulevard<br/>Cultural District, NY 10012</p>
          </div>
          <div class="loc-block">
            <h4>Hours</h4>
            <p>Wednesday — Sunday<br/>18:00 to 00:00</p>
          </div>
          <div class="loc-block">
            <h4>Contact</h4>
            <p>concierge@moha-experience.com<br/>+1 (555) 839-2041</p>
          </div>
        </div>

        <div class="map-representation">
          <div class="map-grid-pattern"></div>
          <div class="map-pin">
            <div class="pin-dot"></div>
            <span style="font-size: 0.75rem; letter-spacing: 0.2em; text-transform: uppercase;">MOHA Location</span>
          </div>
        </div>
      </div>
    </section>

  </main>

  <!-- ==========================================================================
     13. COMPLETE SINGLE-FILE JAVASCRIPT ENGINE
     ========================================================================== -->
  <script>
    /* ==========================================================================
       GLOBAL DATA & STATE MANAGEMENT
       ========================================================================== */
    const MENU_DATA = {
      starters: [
        { name: "Charred Bone Marrow", desc: "Smoked sea salt, pickled shallots, sourdough ash crackers", price: "$32", img: "https://images.unsplash.com/photo-1544025162-d76694265947?q=80&w=2069&auto=format&fit=crop" },
        { name: "Raw Heirloom Tartare", desc: "Hand-cut wagyu, fermented chili emulsion, crispy capers", price: "$38", img: "https://images.unsplash.com/photo-1555396273-367ea4eb4db5?q=80&w=1974&auto=format&fit=crop" },
        { name: "Smoked Burrata & Ember Fig", desc: "Burnt honey glaze, pine nut soil, micro basil", price: "$28", img: "https://images.unsplash.com/photo-1551024709-8f23befc6f87?q=80&w=1957&auto=format&fit=crop" }
      ],
      mains: [
        { name: "Dry-Aged Duck Breast", desc: "Lavender glaze, roasted parsnip puree, dark cherry jus", price: "$68", img: "https://images.unsplash.com/photo-1565299624946-b28f40a0ae38?q=80&w=1981&auto=format&fit=crop" },
        { name: "Wild Turbot in Parchment", desc: "Braised kelp butter, sea fennel, preserved lemon broth", price: "$74", img: "https://images.unsplash.com/photo-1517248135467-4c7edcad34c4?q=80&w=2070&auto=format&fit=crop" },
        { name: "Ember-Baked Celeriac", desc: "Black truffle reduction, toasted hazelnut butter, chervil", price: "$52", img: "https://images.unsplash.com/photo-1507048331197-7d4ac70811cf?q=80&w=1974&auto=format&fit=crop" }
      ],
      desserts: [
        { name: "Smoked Dark Chocolate Sphere", desc: "Salted caramel core, burnt rosemary smoke infusion", price: "$24", img: "https://images.unsplash.com/photo-1551024709-8f23befc6f87?q=80&w=1957&auto=format&fit=crop" },
        { name: "Roasted Fig Tart", desc: "Buckwheat crust, sheep milk gelato, wildflower honey", price: "$22", img: "https://images.unsplash.com/photo-1559339352-11d035aa65de?q=80&w=1974&auto=format&fit=crop" }
      ],
      drinks: [
        { name: "Terracotta Smoke Cocktail", desc: "Mezcal, amaro, burnt orange oil, volcanic salt rim", price: "$26", img: "https://images.unsplash.com/photo-1514362545857-3bc16c4c7d1b?q=80&w=2070&auto=format&fit=crop" },
        { name: "Botanical Elixir No. 4", desc: "Wild gin, pine needle reduction, clarified lime, tonic", price: "$24", img: "https://images.unsplash.com/photo-1514362545857-3bc16c4c7d1b?q=80&w=2070&auto=format&fit=crop" }
      ]
    };

    let currentPage = 'home';
    let isTransitioning = false;
    let lenisEngine = null;
    let threeScene, threeCamera, threeRenderer, threeParticles;

    /* ==========================================================================
       01. INITIALIZATION & LOADER
       ========================================================================== */
    window.addEventListener('DOMContentLoaded', () => {
      initLenis();
      initThreeJS();
      initCustomCursor();
      initMenuSystem();
      initRouting();
      runLoaderSequence();
    });

    function runLoaderSequence() {
      const counterEl = document.getElementById('loader-counter');
      let progress = 0;

      const interval = setInterval(() => {
        progress += Math.floor(Math.random() * 12) + 1;
        if (progress >= 100) {
          progress = 100;
          clearInterval(interval);
          finishLoading();
        }
        counterEl.textContent = progress < 10 ? `0${progress}` : progress;
      }, 60);
    }

    function finishLoading() {
      const loader = document.getElementById('loader');
      gsap.to(loader, {
        opacity: 0,
        duration: 1,
        ease: 'power3.inOut',
        onComplete: () => {
          loader.style.display = 'none';
          // Animate Home Page Hero Entrance
          gsap.from('#home .hero-title', { y: 60, opacity: 0, duration: 1.4, ease: 'power4.out' });
          gsap.from('#home .hero-subtext', { y: 30, opacity: 0, duration: 1, delay: 0.3, ease: 'power3.out' });
        }
      });
    }

    /* ==========================================================================
       02. SMOOTH SCROLLING (LENIS)
       ========================================================================== */
    function initLenis() {
      if (window.matchMedia('(prefers-reduced-motion: reduce)').matches) return;

      lenisEngine = new Lenis({
        duration: 1.2,
        easing: (t) => Math.min(1, 1.001 - Math.pow(2, -10 * t)),
        smoothWheel: true,
        touchMultiplier: 1.5
      });

      function raf(time) {
        lenisEngine.raf(time);
        requestAnimationFrame(raf);
      }
      requestAnimationFrame(raf);
    }

    /* ==========================================================================
       03. THREE.JS AMBIENT BACKGROUND
       ========================================================================== */
    function initThreeJS() {
      if (window.matchMedia('(prefers-reduced-motion: reduce)').matches) return;

      const canvas = document.getElementById('webgl-canvas');
      threeScene = new THREE.Scene();
      threeCamera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
      threeCamera.position.z = 5;

      threeRenderer = new THREE.WebGLRenderer({ canvas, alpha: true, antialias: true });
      threeRenderer.setSize(window.innerWidth, window.innerHeight);
      threeRenderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));

      // Particles Geometry
      const count = 350;
      const positions = new Float32Array(count * 3);
      for (let i = 0; i < count * 3; i++) {
        positions[i] = (Math.random() - 0.5) * 15;
      }

      const geometry = new THREE.BufferGeometry();
      geometry.setAttribute('position', new THREE.BufferAttribute(positions, 3));

      const material = new THREE.PointsMaterial({
        color: 0xbf573f,
        size: 0.035,
        transparent: true,
        opacity: 0.6
      });

      threeParticles = new THREE.Points(geometry, material);
      threeScene.add(threeParticles);

      // Mouse Interaction tracking
      let mouseX = 0, mouseY = 0;
      window.addEventListener('mousemove', (e) => {
        mouseX = (e.clientX / window.innerWidth - 0.5) * 0.5;
        mouseY = (e.clientY / window.innerHeight - 0.5) * 0.5;
      });

      function animateThree() {
        requestAnimationFrame(animateThree);
        threeParticles.rotation.y += 0.001;
        threeParticles.rotation.x += 0.0005;

        threeCamera.position.x += (mouseX - threeCamera.position.x) * 0.05;
        threeCamera.position.y += (-mouseY - threeCamera.position.y) * 0.05;

        threeRenderer.render(threeScene, threeCamera);
      }
      animateThree();

      window.addEventListener('resize', () => {
        threeCamera.aspect = window.innerWidth / window.innerHeight;
        threeCamera.updateProjectionMatrix();
        threeRenderer.setSize(window.innerWidth, window.innerHeight);
      });
    }

    /* ==========================================================================
       04. CUSTOM CURSOR & MAGNETIC INTERACTIONS
       ========================================================================== */
    function initCustomCursor() {
      const follower = document.getElementById('cursor-follower');
      const cursorText = document.getElementById('cursor-text');

      let posX = 0, posY = 0;
      let mouseX = 0, mouseY = 0;

      window.addEventListener('mousemove', (e) => {
        mouseX = e.clientX;
        mouseY = e.clientY;
      });

      gsap.ticker.add(() => {
        posX += (mouseX - posX) * 0.15;
        posY += (mouseY - posY) * 0.15;
        follower.style.transform = `translate(${posX}px, ${posY}px) translate(-50%, -50%)`;
      });

      // Delegate Cursor Hover States
      document.addEventListener('mouseover', (e) => {
        const cursorTarget = e.target.closest('[data-cursor]');
        if (cursorTarget) {
          const text = cursorTarget.getAttribute('data-cursor');
          cursorText.textContent = text || 'VIEW';
          follower.classList.add('hovered');
        }
      });

      document.addEventListener('mouseout', (e) => {
        if (e.target.closest('[data-cursor]')) {
          follower.classList.remove('hovered');
        }
      });
    }

    /* ==========================================================================
       05. MENU INTERACTION SYSTEM
       ========================================================================== */
    function initMenuSystem() {
      const container = document.getElementById('menu-items-container');
      const preview = document.getElementById('menu-floating-preview');
      const previewImg = document.getElementById('menu-preview-img');
      const tabs = document.querySelectorAll('.menu-tab');

      function renderCategory(cat) {
        container.innerHTML = '';
        const items = MENU_DATA[cat] || [];

        items.forEach((item) => {
          const el = document.createElement('div');
          el.className = 'menu-item';
          el.setAttribute('data-cursor', 'VIEW');
          el.innerHTML = `
            <div class="menu-item-info">
              <h4>${item.name}</h4>
              <p>${item.desc}</p>
            </div>
            <div class="menu-item-price">${item.price}</div>
          `;

          // Hover handlers for floating image
          el.addEventListener('mouseenter', () => {
            previewImg.src = item.img;
            preview.style.opacity = '1';
            preview.style.transform = 'translate(-50%, -50%) scale(1)';
          });

          el.addEventListener('mouseleave', () => {
            preview.style.opacity = '0';
            preview.style.transform = 'translate(-50%, -50%) scale(0.8)';
          });

          container.appendChild(el);
        });
      }

      // Track cursor position for floating image
      window.addEventListener('mousemove', (e) => {
        if (preview.style.opacity === '1') {
          gsap.to(preview, {
            left: e.clientX + 140,
            top: e.clientY,
            duration: 0.4,
            ease: 'power2.out'
          });
        }
      });

      // Tab Switching
      tabs.forEach((tab) => {
        tab.addEventListener('click', () => {
          tabs.forEach((t) => t.classList.remove('active'));
          tab.classList.add('active');
          renderCategory(tab.getAttribute('data-category'));
        });
      });

      // Initial Render
      renderCategory('starters');
    }

    /* ==========================================================================
       06. SINGLE-FILE PAGE ENGINE & ROUTER
       ========================================================================== */
    function initRouting() {
      // Mobile Drawer Toggle
      const burgerBtn = document.getElementById('burger-btn');
      const mobileDrawer = document.getElementById('mobile-drawer');

      burgerBtn.addEventListener('click', () => {
        mobileDrawer.classList.toggle('open');
      });

      document.querySelectorAll('.mobile-nav-link').forEach(link => {
        link.addEventListener('click', () => {
          mobileDrawer.classList.remove('open');
        });
      });

      // Hash Navigation Listener
      window.addEventListener('hashchange', handleHashChange);
      window.addEventListener('load', handleHashChange);
    }

    function handleHashChange() {
      const hash = window.location.hash.replace('#', '') || 'home';
      const targetPage = document.getElementById(hash) ? hash : 'home';

      if (targetPage !== currentPage && !isTransitioning) {
        navigateToPage(targetPage);
      }
    }

    function navigateToPage(targetPage) {
      isTransitioning = true;
      const curtain = document.getElementById('transition-curtain');
      const fromPageEl = document.getElementById(currentPage);
      const toPageEl = document.getElementById(targetPage);

      // Select Custom Transition System based on route pair
      const transitionType = getTransitionType(currentPage, targetPage);

      // Execute Outgoing & Curtain Animation
      executeCurtainIn(curtain, transitionType, () => {
        // Switch Virtual Page DOM states
        if (fromPageEl) fromPageEl.classList.remove('active-page');
        if (toPageEl) toPageEl.classList.add('active-page');

        // Reset scroll position
        if (lenisEngine) lenisEngine.scrollTo(0, { immediate: true });
        window.scrollTo(0, 0);

        // Update Active Nav Styles
        updateNavState(targetPage);

        currentPage = targetPage;

        // Execute Curtain Out & Incoming Animation
        executeCurtainOut(curtain, transitionType, () => {
          isTransitioning = false;
          triggerPageAnimations(targetPage);
        });
      });
    }

    function getTransitionType(from, to) {
      if (from === 'home' && to === 'experience') return 'expand-zoom';
      if (from === 'experience' && to === 'menu') return 'horizontal-slice';
      if (from === 'menu' && to === 'gallery') return 'image-morph';
      if (from === 'gallery' && to === 'story') return 'lateral-slide';
      if (from === 'story' && to === 'reservation') return 'calm-center';
      return 'default-wipe';
    }

    function executeCurtainIn(curtain, type, onComplete) {
      if (type === 'expand-zoom') {
        gsap.fromTo(curtain, 
          { clipPath: 'circle(0% at 50% 50%)' },
          { clipPath: 'circle(150% at 50% 50%)', duration: 0.9, ease: 'power4.inOut', onComplete }
        );
      } else if (type === 'horizontal-slice') {
        gsap.fromTo(curtain, 
          { clipPath: 'inset(0 100% 0 0)' },
          { clipPath: 'inset(0 0% 0 0)', duration: 0.8, ease: 'power3.inOut', onComplete }
        );
      } else if (type === 'lateral-slide') {
        gsap.fromTo(curtain, 
          { clipPath: 'inset(100% 0 0 0)' },
          { clipPath: 'inset(0% 0 0 0)', duration: 0.8, ease: 'power3.inOut', onComplete }
        );
      } else {
        gsap.fromTo(curtain, 
          { clipPath: 'inset(0 0 100% 0)' },
          { clipPath: 'inset(0 0 0% 0)', duration: 0.8, ease: 'power3.inOut', onComplete }
        );
      }
    }

    function executeCurtainOut(curtain, type, onComplete) {
      if (type === 'expand-zoom') {
        gsap.to(curtain, { clipPath: 'circle(0% at 50% 50%)', duration: 0.8, ease: 'power4.inOut', onComplete });
      } else if (type === 'horizontal-slice') {
        gsap.to(curtain, { clipPath: 'inset(0 0 0 100%)', duration: 0.8, ease: 'power3.inOut', onComplete });
      } else {
        gsap.to(curtain, { clipPath: 'inset(100% 0 0 0)', duration: 0.8, ease: 'power3.inOut', onComplete });
      }
    }

    function updateNavState(activeHash) {
      document.querySelectorAll('.nav-link').forEach(link => {
        const hash = link.getAttribute('href').replace('#', '');
        if (hash === activeHash) {
          link.classList.add('active');
        } else {
          link.classList.remove('active');
        }
      });
    }

    function triggerPageAnimations(pageId) {
      // Refresh GSAP ScrollTrigger context for the newly active page
      ScrollTrigger.refresh();

      const pageEl = document.getElementById(pageId);
      const title = pageEl.querySelector('.section-title, .hero-title, .story-title, .res-title');

      if (title) {
        gsap.from(title, {
          y: 40,
          opacity: 0,
          duration: 1,
          ease: 'power3.out'
        });
      }
    }
  </script>
</body>
</html>
