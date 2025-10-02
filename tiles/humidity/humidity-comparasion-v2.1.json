<!--
╔════════════════════════════════════════════════════════════════════════════╗
║                    HUMIDITY COMPARISON TILE v2.1                           ║
║                   Professional SharpTools Custom Tile                      ║
╠════════════════════════════════════════════════════════════════════════════╣
║                                                                            ║
║  VERSION 2.1 CHANGELOG:                                                   ║
║  • OPTIMIZATION: Removed code duplication and consolidated functions       ║
║  • REFACTORING: Unified subscription handlers                             ║
║  • IMPROVEMENT: Simplified CSS variable system                            ║
║  • CLEANUP: Consolidated API methods                                      ║
║  • PERFORMANCE: Reduced file size by ~25%                                 ║
║                                                                            ║
║  KEY FEATURES:                                                             ║
║  • Dual data sources: SharpTools Things and Variables                      ║
║  • OpenWeatherMap API integration (2.5/3.0)                               ║
║  • Smart window recommendations (OPEN/CLOSE/OK)                            ║
║  • Visual flow indicators with animated arrows                             ║
║  • High humidity alerts (>60%)                                            ║
║  • Gradient backgrounds indicating humidity flow                           ║
║  • Fully responsive with automatic scaling                                 ║
║                                                                            ║
║  AUTHOR: Wilson Marcolin                                                   ║
║  CONTRIBUTORS: Claude AI (Architecture & Optimization)                     ║
║  VERSION: 2.1.0                                                            ║
║  RELEASE: January 2025                                                     ║
║  LICENSE: MIT                                                              ║
║                                                                            ║
╚════════════════════════════════════════════════════════════════════════════╝
-->

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=5.0, user-scalable=yes">
  <meta name="apple-mobile-web-app-capable" content="yes">
  <meta name="description" content="Humidity Comparison Tile v2.1 - Optimized humidity monitoring for SharpTools">
  <title>Humidity Comparison v2.1</title>

  <!-- Tile Settings Configuration -->
  <script type="application/json" id="tile-settings">
  {
    "schema": "0.2.0",
    "settings": [
      {
        "type": "STRING",
        "label": "OpenWeather API Key (Optional)",
        "name": "apiKey",
        "required": false
      },
      {
        "type": "STRING",
        "name": "location",
        "default": "3703443",
        "placeholder": "City ID or lat,lon",
        "label": "Location for Weather API"
      },
      {
        "type": "NUMERIC",
        "name": "refreshInterval",
        "default": 30,
        "label": "Refresh Interval (minutes)"
      },
      {
        "type": "STRING",
        "name": "apiPreference",
        "default": "3-0onecall",
        "label": "API Version (2-5multi or 3-0onecall)"
      },
      {
        "type": "BOOLEAN",
        "name": "useGradient",
        "default": true,
        "label": "Use Gradient Background"
      },
      {
        "type": "THING",
        "name": "indoorDevice",
        "label": "Indoor Humidity Device",
        "filters": {"capabilities": ["relativeHumidityMeasurement"]}
      },
      {
        "type": "THING",
        "name": "outdoorDevice",
        "label": "Outdoor Humidity Device",
        "filters": {"capabilities": ["relativeHumidityMeasurement"]}
      },
      {
        "type": "BOOLEAN",
        "name": "useOpenWeather",
        "label": "Use OpenWeather API for Outdoor",
        "default": false
      },
      {
        "type": "VARIABLE",
        "name": "avgHumidityIN",
        "label": "Indoor Humidity Variable (Optional)",
        "filters": {"type": "Number"}
      },
      {
        "type": "BOOLEAN",
        "name": "useVariableForIndoor",
        "label": "Use Variable instead of Device for Indoor",
        "default": false
      }
    ],
    "name": "Humidity Comparison 2.1"
  }
  </script>

  <!-- External Dependencies -->
  <script src="https://cdn.jsdelivr.net/npm/axios@0.27.2/dist/axios.min.js"></script>
  <script src="https://cdn.sharptools.io/js/custom-tiles/0.2.1/stio.js"></script>

  <style>
  /* ============================================
     RESET & BASE STYLES
     ============================================ */
  * {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
    -webkit-tap-highlight-color: transparent;
    user-select: none;
  }

  /* ============================================
     CSS VARIABLES - UNIFIED SYSTEM
     ============================================ */
  :root {
    /* Single scale multiplier */
    --scale: 1;
    
    /* Base unit for all calculations */
    --base: calc(1rem * var(--scale));
    
    /* Typography using base multipliers */
    --font-value: calc(var(--base) * 2.5);
    --font-unit: calc(var(--base) * 1);
    --font-icon: calc(var(--base) * 2);
    --font-recommendation: calc(var(--base) * 2.7);
    
    /* Spacing using base */
    --gap: calc(var(--base) * 0.4);
    --padding: calc(var(--base) * 0.8);
    
    /* Colors */
    --violet-start: #7B2CBF;
    --violet-mid: #9D4EDD;
    --violet-end: #C77DFF;
    --text: #FFFFFF;
    --text-dim: rgba(255, 255, 255, 0.9);
    --surface: rgba(255, 255, 255, 0.15);
    --alert: #FFE66D;
    
    /* Effects */
    --shadow: 0 2px 8px rgba(0, 0, 0, 0.3);
    --radius: 8px;
    --transition: 350ms cubic-bezier(0.4, 0, 0.2, 1);
  }

  /* ============================================
     GLOBAL STYLES
     ============================================ */
  html, body {
    height: 100%;
    overflow: hidden;
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
    font-weight: 400;
    line-height: 1.5;
    -webkit-font-smoothing: antialiased;
  }

  body {
    display: flex;
    align-items: center;
    justify-content: center;
  }

  /* ============================================
     CONTAINER & LAYOUT
     ============================================ */
  .container {
    width: 100%;
    height: 100%;
    position: relative;
    color: var(--text);
    padding: var(--padding);
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    transition: background var(--transition);
  }

  /* Gradient states */
  .container.gradient-left {
    background: linear-gradient(90deg, var(--violet-start) 0%, var(--violet-mid) 50%, var(--violet-end) 100%);
  }

  .container.gradient-right {
    background: linear-gradient(270deg, var(--violet-start) 0%, var(--violet-mid) 50%, var(--violet-end) 100%);
  }

  .container.gradient-neutral {
    background: linear-gradient(135deg, var(--violet-start) 0%, var(--violet-mid) 50%, var(--violet-end) 100%);
  }

  .container.loading { background: var(--violet-mid); }
  .container.error { background: #E11111; }

  /* Main content wrapper */
  .content {
    width: 100%;
    height: 100%;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: space-between;
    max-width: 100%;
    overflow: hidden;
  }

  /* ============================================
     HUMIDITY DISPLAY GRID
     ============================================ */
  .grid {
    width: 100%;
    display: grid;
    grid-template-columns: minmax(0, 1fr) auto minmax(0, 1fr) auto minmax(0, 1fr);
    align-items: center;
    justify-items: center;
    gap: calc(var(--gap) * 1.5);
    padding: 0 calc(var(--padding) * 0.5);
    flex: 1;
  }

  /* Value display */
  .value-group {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    gap: 0;
    min-width: 0;
  }

  .value {
    font-size: var(--font-value);
    font-weight: 400;
    line-height: 1;
    letter-spacing: -0.02em;
    text-shadow: var(--shadow);
    transition: all var(--transition);
  }

  .unit {
    font-size: calc(var(--font-value) * 0.25);
    font-weight: 400;
    opacity: 0.8;
    text-transform: uppercase;
    letter-spacing: 0.05em;
    margin-top: calc(var(--gap) * 0.25);
  }

  /* Alert state */
  .value-group.alert .value,
  .value-group.alert .unit {
    animation: pulse 2s ease-in-out infinite;
  }

  @keyframes pulse {
    0%, 100% { 
      color: var(--text);
      transform: scale(1);
    }
    50% { 
      color: var(--alert);
      transform: scale(1.05);
    }
  }

  /* Icons */
  .icon {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    transition: transform var(--transition);
    flex-shrink: 0;
  }

  .icon svg {
    width: var(--font-icon);
    height: var(--font-icon);
    fill: currentColor;
    filter: drop-shadow(var(--shadow));
  }

  .icon-label {
    font-size: calc(var(--font-value) * 0.25);
    font-weight: 400;
    opacity: 0.8;
    margin-top: calc(var(--gap) * 0.5);
    letter-spacing: 0.1em;
  }

  /* Arrow animation */
  .arrow {
    animation: pulse 2s ease-in-out infinite;
  }

  /* ============================================
     RECOMMENDATION TEXT
     ============================================ */
  .recommendation {
    font-size: var(--font-recommendation);
    font-weight: 200;
    text-transform: uppercase;
    letter-spacing: 0.15em;
    text-align: center;
    white-space: nowrap;
    position: absolute;
    bottom: calc(var(--padding) * 2);
    left: 50%;
    transform: translateX(-50%);
    text-shadow: var(--shadow);
    opacity: 0.95;
  }

  .recommendation.alert {
    animation: recommendPulse 3s ease-in-out infinite;
  }

  @keyframes recommendPulse {
    0%, 100% { 
      opacity: 0.95;
      transform: translateX(-50%) scale(1);
    }
    50% { 
      opacity: 1;
      transform: translateX(-50%) scale(1.05);
    }
  }

  /* ============================================
     STATUS STATES
     ============================================ */
  .status {
    text-align: center;
    font-size: calc(var(--base) * 1.2);
    opacity: 0.8;
  }

  .spinner {
    width: calc(var(--base) * 3);
    height: calc(var(--base) * 3);
    border: 6px solid var(--text-dim);
    border-top-color: var(--text);
    border-radius: 50%;
    animation: spin 1s linear infinite;
  }

  @keyframes spin {
    to { transform: rotate(360deg); }
  }

  /* ============================================
     UTILITY CLASSES
     ============================================ */
  .hidden { display: none !important; }

  /* Screen reader only */
  .sr-only {
    position: absolute;
    width: 1px;
    height: 1px;
    padding: 0;
    margin: -1px;
    overflow: hidden;
    clip: rect(0, 0, 0, 0);
    white-space: nowrap;
    border-width: 0;
  }

  /* ============================================
     RESPONSIVE & ACCESSIBILITY
     ============================================ */
  @media (hover: hover) and (pointer: fine) {
    .value { font-size: calc(var(--font-value) * 1.1); }
    .icon svg { width: calc(var(--font-icon) * 1.2); height: calc(var(--font-icon) * 1.2); }
  }

  @media only screen and (max-width: 768px) {
    .grid { gap: calc(var(--gap) * 0.8); }
    .recommendation { font-size: calc(var(--font-recommendation) * 0.8); }
  }

  @media only screen and (max-width: 480px) {
    .grid { gap: calc(var(--gap) * 0.6); }
    .recommendation { 
      font-size: calc(var(--font-recommendation) * 0.7);
      bottom: calc(var(--padding) * 1.5);
    }
  }

  @media (prefers-reduced-motion: reduce) {
    * {
      animation-duration: 0.01ms !important;
      transition-duration: 0.01ms !important;
    }
  }

  @media (prefers-contrast: high) {
    .container { border: 2px solid var(--text); }
  }
  </style>
</head>
<body>
  <div id="container" class="container" role="main" aria-label="Humidity comparison display">
    <!-- Loading -->
    <div id="loading" class="status hidden" aria-live="polite">
      <div class="spinner" role="status" aria-label="Loading"></div>
      <span class="sr-only">Loading humidity data...</span>
    </div>
    
    <!-- Error -->
    <div id="error" class="status hidden" role="alert" aria-live="assertive">
      <div id="errorMsg">Configuration Required</div>
    </div>
    
    <!-- Main Content -->
    <div id="main" class="content hidden" aria-live="polite">
      <!-- Humidity Grid -->
      <div class="grid">
        <!-- Indoor Icon -->
        <div class="icon" aria-label="Indoor">
          <svg viewBox="0 0 24 24">
            <path d="M 19 8 C 20.11 8 21 8.9 21 10 V 16.76 C 21.61 17.31 22 18.11 22 19 C 22 20.66 20.66 22 19 22 C 17.34 22 16 20.66 16 19 C 16 18.11 16.39 17.31 17 16.76 V 10 C 17 8.9 17.9 8 19 8 M 19 9 C 18.45 9 18 9.45 18 10 V 11 H 20 V 10 C 20 9.45 19.55 9 19 9 M 12 5.69 L 7 10.19 V 18 H 14.1 L 14 19 L 14.1 20 H 5 V 12 H 2 L 12 3 L 16.4 6.96 C 15.89 7.4 15.5 7.97 15.25 8.61 L 12 5.69 Z"/>
          </svg>
          <span class="icon-label">IN</span>
        </div>

        <!-- Indoor Value -->
        <div id="indoorGroup" class="value-group">
          <span id="indoorValue" class="value">--</span>
          <span class="unit">%RH</span>
        </div>

        <!-- Arrow -->
        <div id="arrow" class="icon arrow" aria-label="Flow direction">
          <!-- Arrow SVG inserted dynamically -->
        </div>

        <!-- Outdoor Value -->
        <div id="outdoorGroup" class="value-group">
          <span id="outdoorValue" class="value">--</span>
          <span class="unit">%RH</span>
        </div>

        <!-- Outdoor Icon -->
        <div class="icon" aria-label="Outdoor">
          <svg viewBox="0 0 24 24">
            <path d="M 20 14 H 22 V 16 H 20 C 18.62 16 17.26 15.65 16 15 C 13.5 16.3 10.5 16.3 8 15 C 6.74 15.65 5.37 16 4 16 H 2 V 14 H 4 C 5.39 14 6.78 13.53 8 12.67 C 10.44 14.38 13.56 14.38 16 12.67 C 17.22 13.53 18.61 14 20 14 M 20 20 H 22 V 22 H 20 C 18.62 22 17.26 21.65 16 21 C 13.5 22.3 10.5 22.3 8 21 C 6.74 21.65 5.37 22 4 22 H 2 V 20 H 4 C 5.39 20 6.78 19.53 8 18.67 C 10.44 20.38 13.56 20.38 16 18.67 C 17.22 19.53 18.61 20 20 20 M 7 2 L 3 6 H 6 V 11 H 8 V 6 H 11 M 17 2 L 13 6 H 16 V 11 H 18 V 6 H 21"/>
          </svg>
          <span class="icon-label">OUT</span>
        </div>
      </div>
      
      <!-- Recommendation -->
      <div id="recommendation" class="recommendation"></div>
    </div>
  </div>

  <script>
  (function() {
    'use strict';

    /* ============================================
       HUMIDITY COMPARISON v2.1 - Optimized
       ============================================ */

    // Configuration
    const Config = {
      api: {
        key: '',
        baseUrl: 'https://api.openweathermap.org/data/',
        version: '3.0',
        retries: 3
      },
      location: { lat: 8.9823, lon: -79.5199, cityId: null },
      devices: { indoor: null, outdoor: null },
      variable: null,
      display: { gradient: true, alertThreshold: 60 },
      refresh: { interval: 1800000, timer: null },
      options: { useVariable: false, useApi: false }
    };

    // State
    const State = {
      indoor: null,
      outdoor: null,
      subs: [],
      initialized: false
    };

    // DOM References
    const DOM = {};

    // Icons
    const Icons = {
      left: '<svg viewBox="0 0 24 24"><path d="M20,9V15H12L12,19L4,12L12,5V9H20Z"/></svg>',
      right: '<svg viewBox="0 0 24 24"><path d="M4,15V9H12V5L20,12L12,19V15H4Z"/></svg>'
    };

    // Initialize DOM
    function initDOM() {
      const ids = ['container', 'loading', 'error', 'main', 'errorMsg',
                   'indoorValue', 'outdoorValue', 'indoorGroup', 'outdoorGroup',
                   'arrow', 'recommendation'];
      
      ids.forEach(id => {
        DOM[id] = document.getElementById(id);
      });
      
      return DOM.container !== null;
    }

    // Dynamic Sizing
    const Sizing = {
      init() {
        this.update();
        window.addEventListener('resize', this.debounce(this.update, 250));
      },
      
      update() {
        if (!DOM.container) return;
        
        const size = Math.min(DOM.container.offsetWidth, DOM.container.offsetHeight);
        const aspect = DOM.container.offsetWidth / DOM.container.offsetHeight;
        
        // Calculate scale
        let scale = size < 150 ? 0.5 :
                   size < 200 ? 0.65 :
                   size < 250 ? 0.8 :
                   size < 500 ? size / 350 :
                   size < 900 ? size / 450 :
                   2.5;
        
        // Adjust for aspect ratio
        if (aspect > 3.5) scale *= 0.7;
        else if (aspect > 2.5) scale *= 0.85;
        else if (aspect > 2) scale *= 0.95;
        
        document.documentElement.style.setProperty('--scale', scale);
      },
      
      debounce(fn, ms) {
        let timer;
        return (...args) => {
          clearTimeout(timer);
          timer = setTimeout(() => fn.apply(this, args), ms);
        };
      }
    };

    // API Module
    const API = {
      async fetch() {
        if (!Config.api.key) return null;
        
        const { lat, lon, cityId } = Config.location;
        const v = Config.api.version;
        let url;
        
        // Build URL based on version and location
        if (v === '3.0') {
          url = `${Config.api.baseUrl}3.0/onecall?lat=${lat}&lon=${lon}&appid=${Config.api.key}&exclude=minutely,hourly,daily,alerts`;
        } else if (cityId) {
          url = `${Config.api.baseUrl}2.5/weather?id=${cityId}&appid=${Config.api.key}`;
        } else {
          url = `${Config.api.baseUrl}2.5/weather?lat=${lat}&lon=${lon}&appid=${Config.api.key}`;
        }
        
        try {
          const response = await axios.get(url);
          
          // Extract humidity based on version
          if (v === '3.0') {
            return response.data?.current?.humidity;
          } else {
            // For 2.5, also update coordinates if using city ID
            if (cityId && response.data?.coord) {
              Config.location.lat = response.data.coord.lat;
              Config.location.lon = response.data.coord.lon;
            }
            return response.data?.main?.humidity;
          }
        } catch (error) {
          return null;
        }
      }
    };

    // Device Control - unified subscription
    const Device = {
      clear() {
        State.subs.forEach(sub => sub?.off?.());
        State.subs = [];
      },
      
      subscribe(source, type) {
        if (!source) return false;
        
        try {
          // Handle Variable
          if (type === 'variable') {
            const value = source.value;
            if (value != null) State.indoor = parseFloat(value);
            
            const sub = source.onValue(v => {
              if (v != null) {
                State.indoor = parseFloat(v);
                Display.update();
              }
            });
            
            if (sub) State.subs.push(sub);
            return true;
          }
          
          // Handle Device
          const attr = source.attributes?.humidity || source.attributes?.relativeHumidity;
          if (!attr) return false;
          
          const attrName = source.attributes.humidity ? 'humidity' : 'relativeHumidity';
          if (source.subscribe) source.subscribe(attrName);
          
          const value = attr.value;
          if (value != null) {
            State[type] = parseFloat(value);
          }
          
          const sub = attr.onValue(v => {
            if (v != null) {
              State[type] = parseFloat(v);
              Display.update();
            }
          });
          
          if (sub) State.subs.push(sub);
          return true;
          
        } catch (error) {
          return false;
        }
      }
    };

    // Display Control
    const Display = {
      update() {
        if (State.indoor == null || State.outdoor == null) return;
        
        const indoor = State.indoor;
        const outdoor = State.outdoor;
        
        // Update values
        DOM.indoorValue.textContent = Math.round(indoor);
        DOM.outdoorValue.textContent = Math.round(outdoor);
        
        // Handle alert
        DOM.indoorGroup.classList.toggle('alert', indoor > Config.display.alertThreshold);
        
        // Update arrow
        const isHigherIndoor = indoor > outdoor * 0.95;
        DOM.arrow.innerHTML = isHigherIndoor ? Icons.right : Icons.left;
        DOM.arrow.setAttribute('aria-label', isHigherIndoor ? 'Higher indoors' : 'Higher outdoors');
        
        // Update recommendation
        let text = '';
        let alert = false;
        
        if (outdoor > indoor) {
          text = 'CLOSE WINDOWS';
        } else if (indoor > outdoor) {
          text = 'OPEN WINDOWS';
          alert = indoor > Config.display.alertThreshold;
        } else {
          text = 'WINDOWS OK';
        }
        
        DOM.recommendation.textContent = text;
        DOM.recommendation.classList.toggle('alert', alert);
        
        // Update gradient
        this.updateGradient(indoor, outdoor);
        
        // Show main view
        this.showView('main');
      },
      
      updateGradient(indoor, outdoor) {
        if (!Config.display.gradient) {
          DOM.container.className = 'container';
          return;
        }
        
        DOM.container.className = 'container';
        
        const diff = indoor - outdoor * 0.95;
        if (Math.abs(diff) < 2) {
          DOM.container.classList.add('gradient-neutral');
        } else if (diff > 0) {
          DOM.container.classList.add('gradient-left');
        } else {
          DOM.container.classList.add('gradient-right');
        }
      },
      
      showView(name) {
        ['loading', 'error', 'main'].forEach(v => {
          DOM[v]?.classList.toggle('hidden', v !== name);
        });
        
        DOM.container.classList.toggle('loading', name === 'loading');
        DOM.container.classList.toggle('error', name === 'error');
      },
      
      showError(msg) {
        if (DOM.errorMsg) DOM.errorMsg.textContent = msg || 'An error occurred';
        this.showView('error');
      }
    };

    // Application Controller
    const App = {
      async init() {
        if (!initDOM()) return;
        
        Sizing.init();
        Display.showView('loading');
        
        if (typeof stio === 'undefined') {
          setTimeout(() => this.init(), 200);
          return;
        }
        
        try {
          stio.ready(data => {
            if (this.configure(data.settings)) {
              this.start();
            }
          });
        } catch (e) {
          Display.showError('Failed to initialize');
        }
      },
      
      configure(settings) {
        // Devices and variables
        Config.devices.indoor = settings.indoorDevice;
        Config.devices.outdoor = settings.outdoorDevice;
        Config.variable = settings.avgHumidityIN;
        
        // Options
        Config.options.useVariable = settings.useVariableForIndoor === true;
        Config.options.useApi = settings.useOpenWeather === true;
        
        // API settings
        Config.api.key = settings.apiKey || '';
        Config.api.version = settings.apiPreference?.includes('3.0') ? '3.0' : '2.5';
        
        // Validation
        if (Config.options.useApi && !Config.api.key) {
          Display.showError('API Key required');
          return false;
        }
        
        if (!Config.devices.indoor && !Config.variable) {
          Display.showError('Indoor source required');
          return false;
        }
        
        if (!Config.devices.outdoor && !Config.api.key) {
          Display.showError('Outdoor source required');
          return false;
        }
        
        // Location
        if (Config.api.key) {
          const loc = settings.location || '3703443';
          if (/^\d+$/.test(loc)) {
            Config.location.cityId = loc;
          } else if (/^(-?\d+\.?\d*),\s*(-?\d+\.?\d*)$/.test(loc)) {
            const [lat, lon] = loc.split(',').map(n => parseFloat(n.trim()));
            Config.location.lat = lat;
            Config.location.lon = lon;
          }
        }
        
        // Display
        Config.display.gradient = settings.useGradient !== false;
        
        // Refresh
        const mins = Math.max(settings.refreshInterval || 30, 1);
        Config.refresh.interval = mins * 60000;
        
        return true;
      },
      
      async start() {
        try {
          Device.clear();
          
          // Setup indoor
          let indoorOk = false;
          if (Config.options.useVariable && Config.variable) {
            indoorOk = Device.subscribe(Config.variable, 'variable');
          } else if (Config.devices.indoor) {
            indoorOk = Device.subscribe(Config.devices.indoor, 'indoor');
          } else if (Config.variable) {
            indoorOk = Device.subscribe(Config.variable, 'variable');
          }
          
          if (!indoorOk) {
            Display.showError('Indoor setup failed');
            return;
          }
          
          // Setup outdoor
          let outdoorOk = false;
          if (Config.options.useApi && Config.api.key) {
            outdoorOk = true;
          } else if (Config.devices.outdoor) {
            outdoorOk = Device.subscribe(Config.devices.outdoor, 'outdoor');
          } else if (Config.api.key) {
            outdoorOk = true;
          }
          
          if (!outdoorOk) {
            Display.showError('Outdoor setup failed');
            return;
          }
          
          // Initial refresh
          await this.refresh();
          
          // Start timer
          this.startTimer();
          
          State.initialized = true;
        } catch (error) {
          Display.showError('Failed to load data');
        }
      },
      
      async refresh() {
        try {
          // Fetch API data if needed
          if ((Config.options.useApi || !Config.devices.outdoor) && Config.api.key) {
            const humidity = await API.fetch();
            if (humidity != null) {
              State.outdoor = humidity;
            }
          }
          
          // Update display
          if (State.indoor != null && State.outdoor != null) {
            Display.update();
          } else {
            // Retry after delay
            setTimeout(() => {
              if (State.indoor != null && State.outdoor != null) {
                Display.update();
              }
            }, 2000);
          }
        } catch (error) {
          // Silent fail
        }
      },
      
      startTimer() {
        if (Config.refresh.timer) {
          clearInterval(Config.refresh.timer);
        }
        
        Config.refresh.timer = setInterval(() => {
          this.refresh().catch(() => {});
        }, Config.refresh.interval);
      },
      
      destroy() {
        if (Config.refresh.timer) {
          clearInterval(Config.refresh.timer);
          Config.refresh.timer = null;
        }
        Device.clear();
      }
    };

    // Initialize
    if (document.readyState === 'loading') {
      document.addEventListener('DOMContentLoaded', () => App.init());
    } else {
      App.init();
    }
    
    // Cleanup
    window.addEventListener('beforeunload', () => App.destroy());

  })();
  </script>
</body>
</html>