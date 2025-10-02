<!--
╔════════════════════════════════════════════════════════════════════════════╗
║                      SUN AND MOON INFORMATION v1.2                         ║
║              Professional Astronomical Display for SharpTools.io           ║
╠════════════════════════════════════════════════════════════════════════════╣
║                                                                            ║
║  DESCRIPTION:                                                              ║
║  Professional tile displaying real-time astronomical information including ║
║  current moon phase with visual representation, sun position arc showing   ║
║  daylight progression, and solar radiation metrics from local sensors.     ║
║                                                                            ║
║  KEY FEATURES:                                                             ║
║  • Left Panel: Moon phase visualization with SVG icon, phase name in       ║
║    English and Portuguese, and illumination percentage                     ║
║  • Right Panel: Dynamic sun position gauge with animated icon moving       ║
║    along arc throughout the day, sunrise/sunset times with animated        ║
║    horizon icon                                                            ║
║  • Solar Metrics Panel: Three sensor readings with color-coded display:    ║
║    - LUX: Ambient light level (formatted with thousands separator)         ║
║    - Solar Radiation: W/m² measurement                                     ║
║    - UV Index (UVI): Color-coded from 0-11+ (white/gold/red/black)         ║
║  • Fully responsive design with optimized mobile layout                    ║
║  • Gradient background option                                              ║
║  • Real-time sensor integration via SharpTools                             ║
║                                                                            ║
║  UV INDEX COLOR CODING:                                                    ║
║  The UVI icon changes color based on UV intensity levels:                  ║
║    • White  (0-2):  Low - Minimal sun protection needed                    ║
║    • Gold   (3-5):  Moderate - Sun protection recommended                  ║
║    • Red    (6-7):  High - Sun protection essential                        ║
║    • Black  (8+):   Very High/Extreme - Maximum protection required        ║
║                                                                            ║
║  RESPONSIVE FEATURES:                                                      ║
║  • Mobile: Abbreviated text ("Illum." instead of "Illuminated"),           ║
║    optimized spacing, reduced font sizes for better readability            ║
║  • Desktop: Full text display, larger metrics, enhanced spacing            ║
║  • Automatic text scaling based on tile dimensions                         ║
║  • Touch-optimized interface for mobile devices                            ║
║                                                                            ║
║  SENSOR REQUIREMENTS:                                                      ║
║  • OpenWeather API key (required for sun/moon data)                        ║
║  • Optional local sensors via SharpTools device integration:               ║
║    - Solar Lux sensor (attribute: illuminance)                             ║
║    - Solar Radiation sensor (attribute: state)                             ║
║    - UV Index sensor (attribute: state)                                    ║
║                                                                            ║
║  CONFIGURATION:                                                            ║
║  • API Key: Free OpenWeather API key from openweathermap.org               ║
║  • Location: Latitude,Longitude coordinates (default: Panama City)         ║
║  • Gradient: Toggle gradient vs solid background                           ║
║  • Sensors: Map up to 3 local sensor devices                               ║
║  • Refresh: Configurable interval (default: 30 minutes, minimum: 1)        ║
║                                                                            ║
║  CHANGELOG v1.2:                                                           ║
║  • Added UV Index color coding system (white/gold/red/black)               ║
║  • Implemented LUX value formatting with thousands separator               ║
║  • Reduced Solar Radiation unit font size by 30%                           ║
║  • Added animated horizon icon between sunrise/sunset times                ║
║  • Optimized mobile layout with refined spacing and positioning            ║
║  • Consolidated responsive CSS (removed duplicate media queries)           ║
║  • Enhanced text readability (doubled base font sizes)                     ║
║  • Improved metric icons sizing (50% larger)                               ║
║  • Fixed sun icon positioning (now precisely on arc line)                  ║
║  • Added mobile text abbreviations for better space utilization            ║
║                                                                            ║
║  CHANGELOG v1.1:                                                           ║
║  • Fixed sun icon positioning - now runs ON TOP of the arc                 ║
║  • Removed debug console.log statements                                    ║
║  • Optimized polar coordinate calculations                                 ║
║  • Improved SVG structure and organization                                 ║
║  • Simplified responsive scaling system                                    ║
║  • Added sensor value validation                                           ║
║  • Better code organization and comments                                   ║
║                                                                            ║
║  TECHNICAL DETAILS:                                                        ║
║  • Moon phase calculation using Julian Day algorithm                       ║
║  • Sun position computed via polar coordinates on semicircular arc         ║
║  • Real-time updates from local sensors via SharpTools API                 ║
║  • CSS Grid layout with dynamic scaling variables                          ║
║  • SVG-based graphics for crisp rendering at any size                      ║
║                                                                            ║
║  BROWSER COMPATIBILITY:                                                    ║
║  • Chrome/Edge 90+, Firefox 88+, Safari 14+                                ║
║  • iOS Safari 14+, Android Chrome 90+                                      ║
║  • Requires ES6 JavaScript support                                         ║
║                                                                            ║
║  AUTHORS: Wilson Marcolin & Claude.AI                                      ║
║  VERSION: 1.2                                                              ║
║  RELEASE: January 2025                                                     ║
║  LICENSE: MIT                                                              ║
║  SUPPORT: https://community.sharptools.io                                  ║
║                                                                            ║
╚════════════════════════════════════════════════════════════════════════════╝
-->
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=5.0, user-scalable=yes">
  <meta name="apple-mobile-web-app-capable" content="yes">
  <title>Sun and Moon Information v1.1</title>

  <script type="application/json" id="tile-settings">
{
  "schema": "0.2.0",
  "settings": [
    {
      "type": "STRING",
      "label": "OpenWeather API Key",
      "name": "apiKey",
      "required": true
    },
    {
      "type": "STRING",
      "name": "location",
      "default": "8.9823,-79.5199",
      "placeholder": "lat,lon",
      "label": "Location (Latitude,Longitude)"
    },
    {
      "type": "BOOLEAN",
      "name": "useGradient",
      "default": true,
      "label": "Use Gradient Background"
    },
    {
      "type": "THING",
      "name": "solarLuxThing",
      "label": "Solar Lux Sensor",
      "required": false
    },
    {
      "type": "THING",
      "name": "solarRadiationThing",
      "label": "Solar Radiation Sensor",
      "required": false
    },
    {
      "type": "THING",
      "name": "uvIndexThing",
      "label": "UV Index Sensor",
      "required": false
    },
    {
      "type": "NUMBER",
      "name": "refreshInterval",
      "default": 30,
      "label": "Refresh Interval (minutes)"
    }
  ],
  "name": "Sun and Moon Information v1.1"
}
</script>

  <script src="https://cdn.jsdelivr.net/npm/axios@0.27.2/dist/axios.min.js"></script>
  <script src="https://cdn.sharptools.io/js/custom-tiles.js"></script>

  <style>
  * {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
    -webkit-tap-highlight-color: transparent;
    user-select: none;
  }

  :root {
    --scale: 1;
    --text-xl: calc(2.8rem * var(--scale));
    --text-lg: calc(2.2rem * var(--scale));
    --text-md: calc(1.9rem * var(--scale));
    --text-sm: calc(1.6rem * var(--scale));
    --gap: calc(0.6rem * var(--scale));
    --pad: calc(0.8rem * var(--scale));
    
    /* Theme colors */
    --bg-start: #006400;
    --bg-end: #90EE90;
    --text: #FFFFFF;
    --text-dim: rgba(255,255,255,0.85);
    --surface: rgba(255,255,255,0.1);
    --moon: #F5F5DC;
    --sun: #FFD700;
    
    /* Effects */
    --shadow: 0 2px 8px rgba(0,0,0,0.3);
    --glow-sun: drop-shadow(0 0 8px var(--sun));
    --glow-moon: drop-shadow(0 0 6px var(--moon));
    --radius: 8px;
    --trans: 300ms ease;
  }

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

  /* =================================================================
     CONTAINER & LAYOUT
     ================================================================= */

  .container {
    width: 100%;
    height: 100%;
    position: relative;
    color: var(--text);
    padding: calc(var(--pad) * 4) var(--pad) var(--pad) var(--pad);
    display: flex;
    transition: background var(--trans);
    background: #008000;
  }
    
  .container.gradient {
    background: linear-gradient(135deg, var(--bg-start) 0%, var(--bg-end) 100%);
  }

  .container.loading,
  .container.error {
    align-items: center;
    justify-content: center;
  }

  .container.error {
    background: #E11111;
  }

  .layout {
    width: 100%;
    height: 100%;
    display: grid;
    grid-template-columns: 37% 60%;
    column-gap: 3%;
    align-items: stretch;
  }

  .left-panel,
  .right-panel {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    gap: calc(var(--gap) * 0.8);
  }

  /* =================================================================
     MOON SECTION
     ================================================================= */

  .moon-container {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: var(--gap);
  }

  .moon-icon {
    width: calc(7rem * var(--scale));
    height: calc(7rem * var(--scale));
    filter: var(--glow-moon);
  }

  .moon-icon svg {
    width: 100%;
    height: 100%;
  }

  .moon-phase-name {
    font-size: var(--text-lg);
    font-weight: 400;
    text-align: center;
    text-shadow: var(--shadow);
    line-height: 1.2;
  }

  .moon-illumination {
    font-size: var(--text-sm);
    opacity: 0.9;
    text-align: center;
    line-height: 1.3;
  }

  /* =================================================================
     SUN GAUGE SECTION
     ================================================================= */

  .sun-gauge-container {
    width: 100%;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: calc(var(--gap) * 0);
  }

  .sun-gauge {
    width: 100%;
    max-width: calc(16rem * var(--scale));
    height: calc(8rem * var(--scale));
    position: relative;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .arc-svg {
    width: 100%;
    height: 100%;
  }

  .arc-background {
    stroke: rgba(255,255,255,0.5);
    stroke-width: 4;
    fill: none;
    stroke-linecap: round;
  }

  .sun-times {
    width: 100%;
    display: flex;
    justify-content: space-between;
    align-items: center;
    gap: calc(var(--gap) * 0.1);
    font-size: var(--text-sm);
    opacity: 0.9;
    padding: 0 calc(var(--gap) * 10);
  }
    
  .sun-times .sunrise,
  .sun-times .sunset {
    display: flex;
    align-items: center;
    gap: calc(var(--gap) * 0.3);
  }

  .sun-times img {
    width: 2em;
    height: 2em;
    animation: pulse 3s ease-in-out infinite;
  }

  /* =================================================================
     SOLAR METRICS SECTION
     ================================================================= */

  .solar-metrics {
    width: 100%;
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: calc(var(--gap) * 0.8);
    padding: calc(var(--pad) * 1.5);
    background: var(--surface);
    border-radius: var(--radius);
    min-height: calc(9rem * var(--scale));
    align-items: center;
  }

  .metric-item {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    gap: calc(var(--gap) * 0.5);
    text-align: center;
  }

  .metric-label {
    font-size: calc(var(--text-sm) * 0.9);
    opacity: 0.8;
    text-transform: uppercase;
    letter-spacing: 0.05em;
    line-height: 1.2;
  }

  .metric-icon {
    width: calc(3.75rem * var(--scale));
    height: calc(3.75rem * var(--scale));
    flex-shrink: 0;
  }

  .metric-icon svg {
    width: 100%;
    height: 100%;
    fill: var(--text);
    filter: drop-shadow(0 2px 4px rgba(0,0,0,0.2));
  }

  .metric-value {
    font-size: calc(var(--text-sm) * 1.3);
    font-weight: 400;
    line-height: 1.1;
  }

  /* =================================================================
     LOADING & ERROR STATES
     ================================================================= */

  .status {
    text-align: center;
    font-size: var(--text-lg);
    opacity: 0.7;
  }

  .spinner {
    width: calc(3rem * var(--scale));
    height: calc(3rem * var(--scale));
    border: 3px solid var(--text-dim);
    border-top-color: var(--text);
    border-radius: 50%;
    animation: spin 1s linear infinite;
  }

  .hidden { 
    display: none !important; 
  }

  @keyframes spin {
    to { transform: rotate(360deg); }
  }

  /* =================================================================
     RESPONSIVE BREAKPOINTS
     ================================================================= */

  /* Desktop/Tablet hover devices */
  @media (hover: hover) and (pointer: fine) {
    .moon-icon {
      width: calc(6.5rem * var(--scale));
      height: calc(6.5rem * var(--scale));
    }

    .moon-phase-name {
      font-size: calc(var(--text-md) * 1.1);
    }

    .metric-icon {
      width: calc(3.3rem * var(--scale));
      height: calc(3.3rem * var(--scale));
    }

    .metric-label {
      font-size: calc(var(--text-sm) * 0.8);
    }

    .metric-value {
      font-size: var(--text-md);
    }

    .metric-value .unit-small {
      font-size: 0.7em;
    }
  }

  /* Mobile devices */
  @media only screen and (max-width: 768px) and (pointer: coarse) {
    .container {
      padding-top: calc(var(--pad) * 4);
    }

    .layout {
      column-gap: 2%;
    }

    .moon-container {
      margin-top: calc(var(--gap) * 0);
    }

    .moon-icon {
      width: calc(5.5rem * var(--scale));
      height: calc(5.5rem * var(--scale));
    }

    .moon-phase-name {
      font-size: calc(var(--text-sm) * 1.1);
    }

    .moon-illumination {
      font-size: calc(var(--text-sm) * 1.1);
      line-height: 0.9;
    }

    .phase-pt {
      font-size: calc(var(--text-sm) * 0.7);
    }

    .illuminated-text::after {
      content: 'Illum.';
    }

    .sun-gauge {
      height: calc(6.5rem * var(--scale));
    }

    .sun-times {
      padding: 0 calc(var(--gap) * 3);
      font-size: calc(var(--text-sm) * 0.7);
      margin-top: calc(var(--gap) * -1);
    }

    .solar-metrics {
      padding: var(--pad);
      min-height: calc(8rem * var(--scale));
      margin-top: calc(var(--gap) * -1);
    }

    .metric-icon {
      width: calc(3rem * var(--scale));
      height: calc(3rem * var(--scale));
    }
  }
    
  /* Small mobile devices */
  @media only screen and (max-width: 480px) {
    .moon-icon {
      width: calc(4.5rem * var(--scale));
      height: calc(4.5rem * var(--scale));
    }

    .moon-phase-name {
      font-size: var(--text-sm);
    }

    .sun-gauge {
      height: calc(5rem * var(--scale));
    }

    .solar-metrics {
      padding: calc(var(--pad) * 0.8);
      min-height: calc(7rem * var(--scale));
    }

    .metric-icon {
      width: calc(2.7rem * var(--scale));
      height: calc(2.7rem * var(--scale));
    }

    .metric-label {
      font-size: calc(var(--text-sm) * 0.7);
    }

    .metric-value {
      font-size: var(--text-sm);
    }
 
    .metric-value .unit-small {
      font-size: 0.7em;
    }
  }

  /* =================================================================
     ACCESSIBILITY
     ================================================================= */

  .sr-only {
    position: absolute;
    width: 1px;
    height: 1px;
    padding: 0;
    margin: -1px;
    overflow: hidden;
    clip: rect(0,0,0,0);
    white-space: nowrap;
    border-width: 0;
  }

  @media (prefers-reduced-motion: reduce) {
    * {
      animation-duration: 0.01ms !important;
      transition-duration: 0.01ms !important;
    }
  }

  /* Default: mostrar completo */
  .illuminated-text::after {
    content: 'Illuminated';
  }

  @keyframes pulse {
    0%, 100% { opacity: 0.8; transform: scale(1); }
    50% { opacity: 1; transform: scale(1.1); }
  }

  .icon-uvi-wrapper {
    display: block;
    width: 100%;
    height: 100%;
  }

  .icon-uvi-wrapper svg {
    width: 100%;
    height: 100%;
  }

  .icon-uvi-wrapper.uvi-white svg path {
    fill: #FFFFFF;
  }

  .icon-uvi-wrapper.uvi-gold svg path {
    fill: #FFD700;
  }

  .icon-uvi-wrapper.uvi-red svg path {
    fill: #FF0000;
  }

  .icon-uvi-wrapper.uvi-black svg path {
    fill: #000000;
  }

  .metric-value .unit-small {
    font-size: 0.7em;
  }

  </style>
</head>
<body>
  <div id="container" class="container" role="main">
    <div id="loading" class="status hidden">
      <div class="spinner" role="status"></div>
      <span class="sr-only">Loading...</span>
    </div>
    
    <div id="error" class="status hidden" role="alert">
      <div id="errorMsg">Configuration Required</div>
    </div>
    
    <div id="content" class="layout hidden"></div>
  </div>

  <script>
  (function() {
    'use strict';

    // =================================================================
    // CONFIGURATION
    // =================================================================

    const Config = {
      api: {
        key: '',
        base: 'https://api.openweathermap.org/data/2.5/'
      },
      location: { 
        lat: 8.9823, 
        lon: -79.5199
      },
      display: {
        useGradient: true
      },
      sensors: {
        solarLux: null,
        solarRadiation: null,
        uvIndex: null
      },
      refresh: { 
        interval: 1800000,
        timer: null 
      }
    };

    // =================================================================
    // STATE MANAGEMENT
    // =================================================================

    const State = {
      moonPhase: null,
      moonIllumination: 0,
      sunrise: null,
      sunset: null,
      currentTime: null,
      solarLux: null,
      solarRadiation: null,
      uvIndex: null,
      isDaytime: false
    };

    const DOM = {};

    // =================================================================
    // MOON PHASE DATA
    // =================================================================

    const MoonPhases = {
      'moon-new': {
        name: 'New Moon',
        namePt: 'Lua Nova',
        path: 'M 12 20 A 8 8 0 1 1 20 12 A 8 8 0 0 1 12 20 M 12 2 A 10 10 0 1 0 22 12 A 10 10 0 0 0 12 2 Z'
      },
      'moon-waxing-crescent': {
        name: 'Waxing Crescent',
        namePt: 'Lua Crescente',
        path: 'M 12 2 A 9.91 9.91 0 0 0 9 2.46 A 10 10 0 0 1 9 21.54 A 10 10 0 1 0 12 2 Z'
      },
      'moon-first-quarter': {
        name: 'First Quarter',
        namePt: 'Quarto Crescente',
        path: 'M 12 2 V 22 A 10 10 0 0 0 12 2 Z'
      },
      'moon-waxing-gibbous': {
        name: 'Waxing Crescent', // Gibbous
        namePt: 'Quase Cheia', // Gibosa
        path: 'M 6 12 C 6 7.5 7.93 3.26 12 2 A 10 10 0 0 1 12 22 C 7.93 20.74 6 16.5 6 12 Z'
      },
      'moon-full': {
        name: 'Full Moon',
        namePt: 'Lua Cheia',
        path: 'M 12 2 A 10 10 0 1 1 2 12 A 10 10 0 0 1 12 2 Z'
      },
      'moon-waning-gibbous': {
        name: 'Waning Gibbous',
        namePt: 'Minguante', // Gibosa
        path: 'M 18 12 C 18 7.5 16.08 3.26 12 2 A 10 10 0 0 0 12 22 C 16.08 20.74 18 16.5 18 12 Z'
      },
      'moon-last-quarter': {
        name: 'Last Quarter',
        namePt: 'Quarto Minguante',
        path: 'M 12 2 A 10 10 0 0 0 12 22 Z'
      },
      'moon-waning-crescent': {
        name: 'Waning Crescent',
        namePt: 'Quase Nova',
        path: 'M 2 12 A 10 10 0 0 0 15 21.54 A 10 10 0 0 1 15 2.46 A 10 10 0 0 0 2 12 Z'
      }
    };

    // =================================================================
    // SOLAR ICONS
    // =================================================================

    const SolarIcons = {
      lux: 'M 12 7 A 5 5 0 0 1 17 12 A 5 5 0 0 1 12 17 A 5 5 0 0 1 7 12 A 5 5 0 0 1 12 7 M 12 9 A 3 3 0 0 0 9 12 A 3 3 0 0 0 12 15 A 3 3 0 0 0 15 12 A 3 3 0 0 0 12 9 M 12 2 L 14.39 5.42 C 13.65 5.15 12.84 5 12 5 C 11.16 5 10.35 5.15 9.61 5.42 L 12 2 M 3.34 7 L 7.5 6.65 C 6.9 7.16 6.36 7.78 5.94 8.5 C 5.5 9.24 5.25 10 5.11 10.79 L 3.34 7 M 3.36 17 L 5.12 13.23 C 5.26 14 5.53 14.78 5.95 15.5 C 6.37 16.24 6.91 16.86 7.5 17.37 L 3.36 17 M 20.65 7 L 18.88 10.79 C 18.74 10 18.47 9.23 18.05 8.5 C 17.63 7.78 17.1 7.15 16.5 6.64 L 20.65 7 M 20.64 17 L 16.5 17.36 C 17.09 16.85 17.62 16.22 18.04 15.5 C 18.46 14.77 18.73 14 18.87 13.21 L 20.64 17 M 12 22 L 9.59 18.56 C 10.33 18.83 11.14 19 12 19 C 12.82 19 13.63 18.83 14.37 18.56 L 12 22 Z',
      radiation: 'M 11 1 L 13.39 4.42 C 12.65 4.15 11.84 4 11 4 S 9.35 4.15 8.61 4.42 L 11 1 M 2.34 6 L 6.5 5.65 C 5.9 6.16 5.36 6.78 4.94 7.5 C 4.5 8.24 4.25 9 4.11 9.79 L 2.34 6 M 2.36 16 L 4.12 12.23 C 4.26 13 4.53 13.78 4.95 14.5 C 5.37 15.24 5.91 15.86 6.5 16.37 L 2.36 16 M 19.65 6 L 17.88 9.79 C 17.74 9 17.47 8.23 17.05 7.5 C 16.63 6.78 16.1 6.15 15.5 5.64 L 19.65 6 M 23 13 H 21 C 21 15.05 20.22 17.1 18.66 18.66 C 17.09 20.23 15.05 21 13 21 V 23 C 15.56 23 18.12 22 20.07 20.07 S 23 15.56 23 13 M 19 13 H 17 C 17 14 16.61 15.05 15.83 15.83 C 15.05 16.61 14 17 13 17 V 19 C 14.54 19 16.08 18.41 17.25 17.24 C 18.41 16.08 19 14.54 19 13 M 11 8 C 12.65 8 14 9.35 14 11 S 12.65 14 11 14 S 8 12.65 8 11 S 9.35 8 11 8 M 11 6 C 8.24 6 6 8.24 6 11 S 8.24 16 11 16 S 16 13.76 16 11 S 13.76 6 11 6 Z',
      uv: 'M 12 9 A 3 3 0 0 0 9 12 A 3 3 0 0 0 12 15 A 3 3 0 0 0 15 12 A 3 3 0 0 0 12 9 M 12 17 A 5 5 0 0 1 7 12 A 5 5 0 0 1 12 7 A 5 5 0 0 1 17 12 A 5 5 0 0 1 12 17 M 12 4.5 C 7 4.5 2.73 7.61 1 12 C 2.73 16.39 7 19.5 12 19.5 C 17 19.5 21.27 16.39 23 12 C 21.27 7.61 17 4.5 12 4.5 Z'
    };

    // =================================================================
    // DOM INITIALIZATION
    // =================================================================

    function initDOM() {
      DOM.container = document.getElementById('container');
      DOM.loading = document.getElementById('loading');
      DOM.error = document.getElementById('error');
      DOM.content = document.getElementById('content');
      DOM.errorMsg = document.getElementById('errorMsg');
    }

    // =================================================================
    // MOON CALCULATIONS
    // =================================================================

    const MoonCalc = {
      calculate(lat, lon, date) {
        const jd = this.julianDay(date);
        const phase = this.phaseValue(jd);
        const illumination = Math.round(phase * 100);
        
        let phaseName;
        if (phase < 0.0625) phaseName = 'moon-new';
        else if (phase < 0.1875) phaseName = 'moon-waxing-crescent';
        else if (phase < 0.3125) phaseName = 'moon-first-quarter';
        else if (phase < 0.4375) phaseName = 'moon-waxing-gibbous';
        else if (phase < 0.5625) phaseName = 'moon-full';
        else if (phase < 0.6875) phaseName = 'moon-waning-gibbous';
        else if (phase < 0.8125) phaseName = 'moon-last-quarter';
        else if (phase < 0.9375) phaseName = 'moon-waning-crescent';
        else phaseName = 'moon-new';
        
        return { phaseName, illumination };
      },
      
      julianDay(date) {
        const a = Math.floor((14 - (date.getMonth() + 1)) / 12);
        const y = date.getFullYear() + 4800 - a;
        const m = (date.getMonth() + 1) + 12 * a - 3;
        return date.getDate() + 
               Math.floor((153 * m + 2) / 5) + 
               365 * y + 
               Math.floor(y / 4) - 
               Math.floor(y / 100) + 
               Math.floor(y / 400) - 
               32045 + 
               (date.getHours() - 12) / 24;
      },
      
      phaseValue(jd) {
        const synodicMonth = 29.53058867;
        const knownNewMoon = 2451550.1;
        const daysSinceNew = jd - knownNewMoon;
        const newMoons = daysSinceNew / synodicMonth;
        const phase = newMoons - Math.floor(newMoons);
        return phase;
      }
    };

    // =================================================================
    // RENDERING COMPONENTS
    // =================================================================

    const Components = {

      renderMoon() {
        if (!State.moonPhase) return '';
  
        const phaseData = MoonPhases[State.moonPhase];
        // Detectar se é mobile
        const isMobile = window.innerWidth <= 768 && window.matchMedia("(pointer: coarse)").matches;
        const illuminatedText = isMobile ? 'Illum.' : 'Illuminated';
  
        return `
          <div class="moon-container">
            <div class="moon-icon">
              <svg viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                <path d="${phaseData.path}" fill="#F5F5DC"/>
              </svg>
            </div>
            <div class="moon-phase-name">${phaseData.name}</div>
            <div class="moon-illumination">${State.moonIllumination}% ${illuminatedText}<br/><span class="phase-pt">(${phaseData.namePt})</span></div>
          </div>
        `;
      },
      
      renderSunGauge() {
        const width = 240;
        const height = 130;
        const centerX = width / 2;
        const centerY = height - 10;
        const radius = 100;
        
        // Create 180 degree arc path (semicircle)
        const startX = centerX - radius;
        const startY = centerY;
        const endX = centerX + radius;
        const endY = centerY;
        
        const arcPath = `M ${startX} ${startY} A ${radius} ${radius} 0 0 1 ${endX} ${endY}`;
        
        let sunIconHtml = '';
        
        if (State.isDaytime && State.sunrise && State.sunset) {
          const totalDaylight = State.sunset - State.sunrise;
          const elapsed = State.currentTime - State.sunrise;
          const percentage = Math.max(0, Math.min(1, elapsed / totalDaylight));
          
          // Map percentage to angle: 180° (sunrise/left) to 0° (sunset/right)
          // CRITICAL: Angle calculation for arc position
          const angle = 180 - (percentage * 180);
          const angleRad = (angle * Math.PI) / 180;
          
          // Calculate position ON the arc (not inside)
          // The icon size is 24px, so we use half (12px) for centering
          // We offset by 2px outward so the center of the sun sits ON the arc line
          const sunIconSize = 12;
          const arcOffset = 2; // Small offset to place center on arc line
          
          const sunX = centerX + ((radius + arcOffset) * Math.cos(angleRad));
          const sunY = centerY - ((radius + arcOffset) * Math.sin(angleRad));
          
          sunIconHtml = `
            <g class="sun-position" filter="url(#sunGlow)">
              <!-- Glow effect -->
              <circle cx="${sunX}" cy="${sunY}" r="16" fill="#FFD700" opacity="0.2"/>
              <circle cx="${sunX}" cy="${sunY}" r="10" fill="#FFD700" opacity="0.3"/>
              <!-- Sun icon (properly sized and centered) -->
              <g transform="translate(${sunX - sunIconSize}, ${sunY - sunIconSize}) scale(1)">
                <path fill="#FFD700" d="M 3.55 19.09 L 4.96 20.5 L 6.76 18.71 L 5.34 17.29 M 12 6 C 8.69 6 6 8.69 6 12 S 8.69 18 12 18 S 18 15.31 18 12 C 18 8.68 15.31 6 12 6 M 20 13 H 23 V 11 H 20 M 17.24 18.71 L 19.04 20.5 L 20.45 19.09 L 18.66 17.29 M 20.45 5 L 19.04 3.6 L 17.24 5.39 L 18.66 6.81 M 13 1 H 11 V 4 H 13 M 6.76 5.39 L 4.96 3.6 L 3.55 5 L 5.34 6.81 L 6.76 5.39 M 1 13 H 4 V 11 H 1 M 13 20 H 11 V 23 H 13"/>
              </g>
            </g>
          `;
        }
        
        const sunriseTime = State.sunrise ? this.formatTime(State.sunrise) : '--:--';
        const sunsetTime = State.sunset ? this.formatTime(State.sunset) : '--:--';
        
        return `
          <div class="sun-gauge-container">
            <div class="sun-gauge">
              <svg class="arc-svg" viewBox="0 0 ${width} ${height}" preserveAspectRatio="xMidYMid meet">
                <defs>
                  <filter id="glow">
                    <feGaussianBlur stdDeviation="3" result="coloredBlur"/>
                    <feMerge>
                      <feMergeNode in="coloredBlur"/>
                      <feMergeNode in="SourceGraphic"/>
                    </feMerge>
                  </filter>
                  <filter id="sunGlow">
                    <feGaussianBlur stdDeviation="4" result="coloredBlur"/>
                    <feMerge>
                      <feMergeNode in="coloredBlur"/>
                      <feMergeNode in="coloredBlur"/>
                      <feMergeNode in="SourceGraphic"/>
                    </feMerge>
                  </filter>
                </defs>
                <!-- Arc background -->
                <path class="arc-background" d="${arcPath}" stroke-linecap="round"/>
                <!-- Sun position (rendered on top) -->
                ${sunIconHtml}
              </svg>
            </div>
            <div class="sun-times">
              <span>${sunriseTime}</span>
              <img src="https://basmilius.github.io/weather-icons/production/fill/all/horizon.svg" alt="">
              <span>${sunsetTime}</span>
            </div>
          </div>
        `;
      },
      
      renderSolarMetrics() {
        const metrics = [];
        
        if (State.solarLux !== null) {
          metrics.push(this.renderMetric('LUX', State.solarLux, 'lx', 'lux'));
        }
        
        if (State.solarRadiation !== null) {
          metrics.push(this.renderMetric('Radiation', State.solarRadiation, 'W/m²', 'radiation'));
        }
        
        if (State.uvIndex !== null) {
          metrics.push(this.renderMetric('UVI', State.uvIndex, '', 'uv'));
        }
        
        if (metrics.length === 0) {
          return '<div class="solar-metrics"><div class="metric-item"><div class="metric-label">No sensor data available</div></div></div>';
        }
        
        return `<div class="solar-metrics">${metrics.join('')}</div>`;
      },
      
      renderMetric(label, value, unit, iconType) {
        const iconPath = SolarIcons[iconType];
        let displayValue = typeof value === 'number' ? Math.round(value) : value;
  
        // Formatar LUX com vírgula e sem unidade
        if (iconType === 'lux') {
          displayValue = displayValue.toLocaleString('en-US');
          unit = '';
        }
  
        // Determinar cor do UVI
        let uviClass = '';
        if (iconType === 'uv') {
          const val = typeof value === 'number' ? value : parseFloat(value);
          if (val >= 0 && val <= 2) uviClass = 'uvi-white';
          else if (val >= 3 && val <= 5) uviClass = 'uvi-gold';
          else if (val >= 6 && val <= 7) uviClass = 'uvi-red';
          else if (val >= 8) uviClass = 'uvi-black';
        }
  
        // Renderizar ícone (com wrapper para UVI)
        let iconHtml;
        if (uviClass) {
          iconHtml = `
            <div class="metric-icon">
              <span class="icon-uvi-wrapper ${uviClass}">
                <svg viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                  <path d="${iconPath}"/>
                </svg>
              </span>
            </div>`;
        } else {
          iconHtml = `
            <div class="metric-icon">
              <svg viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                <path d="${iconPath}"/>
              </svg>
            </div>`;
        }
  
        // Formatar unidade com tamanho reduzido para radiation
        const unitHtml = unit ? (iconType === 'radiation' ? '<span class="unit-small">' + unit + '</span>' : unit) : '';
  
        return `
          <div class="metric-item">
            <div class="metric-label">${label}</div>
            ${iconHtml}
            <div class="metric-value">${displayValue}${unitHtml}</div>
          </div>
        `;
      },
      
      
      formatTime(timestamp) {
        const date = new Date(timestamp * 1000);
        return date.toLocaleTimeString('en', {hour:'numeric', minute:'2-digit'});
      }
    };

    // =================================================================
    // DISPLAY MANAGEMENT
    // =================================================================

    const Display = {
      update() {
        const html = `
          <div class="left-panel">
            ${Components.renderMoon()}
          </div>
          <div class="right-panel">
            ${Components.renderSunGauge()}
            ${Components.renderSolarMetrics()}
          </div>
        `;
        
        DOM.content.innerHTML = html;
      },
      
      show(view) {
        ['loading','error','content'].forEach(v => {
          DOM[v]?.classList.toggle('hidden', v !== view);
        });
        DOM.container.classList.toggle('loading', view === 'loading');
        DOM.container.classList.toggle('error', view === 'error');
      },
      
      error(msg) {
        DOM.errorMsg.textContent = msg || 'An error occurred';
        this.show('error');
      }
    };

    // =================================================================
    // API COMMUNICATION
    // =================================================================

    const API = {
      async fetchSunData() {
        try {
          const {lat, lon} = Config.location;
          const url = `${Config.api.base}weather?lat=${lat}&lon=${lon}&appid=${Config.api.key}`;
          
          const res = await axios.get(url);
          State.sunrise = res.data.sys.sunrise;
          State.sunset = res.data.sys.sunset;
          State.currentTime = Math.floor(Date.now() / 1000);
          State.isDaytime = State.currentTime >= State.sunrise && State.currentTime <= State.sunset;
        } catch(e) {
          throw new Error('Failed to fetch sun data');
        }
      }
    };

    // =================================================================
    // SENSOR MANAGEMENT
    // =================================================================

    const Sensors = {
      subscribe() {
        this.subscribeSensor(Config.sensors.solarLux, 'illuminance', 'solarLux');
        this.subscribeSensor(Config.sensors.solarRadiation, 'state', 'solarRadiation');
        this.subscribeSensor(Config.sensors.uvIndex, 'state', 'uvIndex');
      },
      
      subscribeSensor(thing, attrName, stateKey) {
        if (!thing || !thing.attributes) return;
        
        const attr = thing.attributes[attrName];
        if (!attr) return;
        
        thing.subscribe(attrName);
        
        // Initial value
        if (attr.value != null) {
          const val = this.validateValue(attr.value);
          if (val !== null) {
            State[stateKey] = val;
            Display.update();
          }
        }
        
        // Listen for updates
        attr.onValue(value => {
          if (value != null) {
            const val = this.validateValue(value);
            if (val !== null) {
              State[stateKey] = val;
              Display.update();
            }
          }
        });
      },
      
      validateValue(value) {
        const val = parseFloat(value);
        if (isNaN(val)) return null;
        return Math.round(val * 10) / 10;
      }
    };

    // =================================================================
    // RESPONSIVE SIZING
    // =================================================================

    const Sizing = {
      init() {
        this.update();
        window.addEventListener('resize', this.debounce(() => this.update(), 250));
      },
      
      update() {
        const w = DOM.container.offsetWidth;
        const h = DOM.container.offsetHeight;
        const size = Math.min(w, h);
        const aspect = w / h;
        
        // Base scale calculation
        let scale = size < 150 ? 0.4 :
                   size < 200 ? 0.5 :
                   size < 250 ? 0.6 :
                   size < 350 ? size / 500 :
                   size < 500 ? size / 550 :
                   size < 900 ? size / 650 :
                   1.8;
        
        // Aspect ratio adjustments
        if (aspect > 3.5) scale *= 0.6;
        else if (aspect > 2.5) scale *= 0.75;
        else if (aspect > 2) scale *= 0.85;
        else if (aspect < 0.8) scale *= 1.05;
        
        document.documentElement.style.setProperty('--scale', scale);
      },
      
      debounce(fn, ms) {
        let timer;
        return function(...args) {
          clearTimeout(timer);
          timer = setTimeout(() => fn.apply(this, args), ms);
        };
      }
    };

    // =================================================================
    // APPLICATION CONTROLLER
    // =================================================================

    const App = {
      async init() {
        initDOM();
        Sizing.init();
        Display.show('loading');
        
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
        } catch(e) {
          Display.error('Initialization failed');
        }
      },
      
      configure(settings) {
        if (!settings.apiKey) {
          Display.error('API Key required');
          return false;
        }
        
        Config.api.key = settings.apiKey;
        
        // Parse location
        const loc = settings.location || '8.9823,-79.5199';
        if (/^(-?\d+\.?\d*),\s*(-?\d+\.?\d*)$/.test(loc)) {
          const [lat, lon] = loc.split(',').map(n => parseFloat(n.trim()));
          Config.location.lat = lat;
          Config.location.lon = lon;
        }
        
        // Display options
        Config.display.useGradient = settings.useGradient !== false;
        DOM.container.classList.toggle('gradient', Config.display.useGradient);
        
        // Sensors
        Config.sensors.solarLux = settings.solarLuxThing || null;
        Config.sensors.solarRadiation = settings.solarRadiationThing || null;
        Config.sensors.uvIndex = settings.uvIndexThing || null;
        
        // Refresh interval
        const mins = Math.max(settings.refreshInterval || 30, 1);
        Config.refresh.interval = mins * 60000;
        
        return true;
      },
      
      async start() {
        try {
          await this.refresh();
          Sensors.subscribe();
          this.startTimer();
          Display.show('content');
        } catch(e) {
          Display.error('Failed to load data');
        }
      },
      
      async refresh() {
        try {
          // Calculate moon phase
          const now = new Date();
          const moonData = MoonCalc.calculate(
            Config.location.lat, 
            Config.location.lon, 
            now
          );
          State.moonPhase = moonData.phaseName;
          State.moonIllumination = moonData.illumination;
          
          // Fetch sun data
          await API.fetchSunData();
          
          // Update display
          Display.update();
        } catch(e) {
          throw e;
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
      }
    };

    // =================================================================
    // APPLICATION STARTUP
    // =================================================================

    if (document.readyState === 'loading') {
      document.addEventListener('DOMContentLoaded', () => App.init());
    } else {
      App.init();
    }
    
    window.addEventListener('beforeunload', () => App.destroy());

  })();
  </script>
</body>
</html>