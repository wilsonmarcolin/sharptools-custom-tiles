<!--
╔════════════════════════════════════════════════════════════════════════════╗
║                      AC CONTROL TILE - UNIFIED EDITION                     ║
║                                Version 5.3                                 ║
╠════════════════════════════════════════════════════════════════════════════╣
║                                                                            ║
║  VERSION 5.3 CHANGELOG:                                                    ║
║  • OPTIMIZATION: Removed CSS duplication and consolidated media queries    ║
║  • REFACTORING: Unified event handling for touch and mouse                 ║
║  • IMPROVEMENT: Consolidated sizing calculations                           ║
║  • CLEANUP: Removed redundant code patterns                                ║
║  • PERFORMANCE: Reduced file size by ~25%                                  ║
║                                                                            ║
║  DESCRIPTION:                                                              ║
║  Advanced thermostat control tile for SharpTools with intuitive circular   ║
║  gauge interface and mobile-optimized controls for air conditioning units. ║
║                                                                            ║
║  FEATURES:                                                                 ║
║  • Circular temperature gauge with 270° arc display                        ║
║  • Touch-friendly temperature adjustment buttons                           ║
║  • Configurable fan speed controls (On/Low/Medium/High/Auto)               ║
║  • Drag-enabled power toggle switch                                        ║
║  • Cool/Heat/Off mode support                                              ║
║  • Customizable active state colors                                        ║
║  • Real-time device synchronization                                        ║
║  • Responsive design for all screen sizes                                  ║
║  • External temperature sensor support                                     ║
║  • External contact sensor support                                         ║
║                                                                            ║
║  AUTHORS:                                                                  ║
║  Wilson Marcolin & Claude.AI                                               ║
║                                                                            ║
║  LICENSE:                                                                  ║
║  MIT License - Free to use, modify, and distribute                         ║
║                                                                            ║
║  REQUIREMENTS:                                                             ║
║  • SharpTools Custom Tiles v0.2.1+                                         ║
║  • Thermostat device with standard capabilities                            ║
║  • Modern web browser with JavaScript enabled                              ║
║                                                                            ║
╚════════════════════════════════════════════════════════════════════════════╝
-->

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=5.0, user-scalable=yes">
  <meta name="apple-mobile-web-app-capable" content="yes">
  <meta name="mobile-web-app-capable" content="yes">
  <title>AC Control v5.3</title>

  <!-- Tile Settings Schema -->
  <script type="application/json" id="tile-settings">
  {
    "schema": "0.2.0",
    "settings": [
      {
        "type": "THING",
        "name": "selectedDevice",
        "label": "Air Conditioner Device",
        "filters": ["thermostat"]
      },
      {
        "type": "STRING",
        "name": "customLabel",
        "label": "Custom Label"
      },
      {
        "type": "STRING",
        "name": "activeColor",
        "label": "Active Color (hex)",
        "default": "#0094C9"
      },
      {
        "type": "BOOLEAN",
        "name": "showCurrentTemp",
        "label": "Show Current Temperature",
        "default": true
      },
      {
        "type": "NUMBER",
        "name": "minTemp",
        "label": "Minimum Temperature",
        "default": 18
      },
      {
        "type": "NUMBER",
        "name": "maxTemp",
        "label": "Maximum Temperature",
        "default": 28
      },
      {
        "type": "BOOLEAN",
        "name": "fanHasOn",
        "label": "Fan: ON",
        "default": false
      },
      {
        "type": "BOOLEAN",
        "name": "fanHasLow",
        "label": "Fan: LOW",
        "default": true
      },
      {
        "type": "BOOLEAN",
        "name": "fanHasMedium",
        "label": "Fan: MEDIUM",
        "default": true
      },
      {
        "type": "BOOLEAN",
        "name": "fanHasHigh",
        "label": "Fan: HIGH",
        "default": true
      },
      {
        "type": "BOOLEAN",
        "name": "fanHasAuto",
        "label": "Fan: AUTO",
        "default": false
      },
      {
        "type": "THING",
        "name": "tempSensor",
        "label": "Temperature Sensor (Optional)",
        "filters": ["temperatureMeasurement"]
      },
      {
        "type": "THING",
        "name": "contactSensor",
        "label": "Contact Sensor (Optional)",
        "filters": ["contactSensor"]
      }
    ],
    "name": "AC Control v5.3"
  }
  </script>

  <!-- External Dependencies -->
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
    -webkit-touch-callout: none;
    -webkit-user-select: none;
    user-select: none;
  }

  /* ============================================
     CSS VARIABLES - UNIFIED DESIGN SYSTEM
     ============================================ */
  :root {
    /* Core spacing units */
    --spacing-unit: 0.25rem;
    --spacing-xs: calc(var(--spacing-unit) * 1);
    --spacing-sm: calc(var(--spacing-unit) * 2);
    --spacing-md: calc(var(--spacing-unit) * 4);
    --spacing-lg: calc(var(--spacing-unit) * 6);
    --spacing-xl: calc(var(--spacing-unit) * 8);
    
    /* Typography scale with base multiplier */
    --font-base: 0.875rem;
    --font-size-xs: calc(var(--font-base) * 0.857);
    --font-size-sm: calc(var(--font-base) * 0.929);
    --font-size-base: var(--font-base);
    --font-size-lg: calc(var(--font-base) * 1.143);
    --font-size-xl: calc(var(--font-base) * 1.714);
    --font-size-2xl: calc(var(--font-base) * 2.571);
    
    /* Component sizes with dynamic scaling */
    --base-size: 1;
    --gauge-size: calc(130px * var(--base-size));
    --gauge-stroke: calc(14px * var(--base-size));
    --button-size: calc(48px * var(--base-size));
    --slider-height: calc(42px * var(--base-size));
    --slider-width: calc(104px * var(--base-size));
    
    /* Color palette */
    --color-primary: #0094C9;
    --color-primary-dark: #0077A3;
    --color-secondary: #2196f3;
    --color-heating: #ff6b6b;
    --color-surface: #ffffff;
    --color-text: #000000;
    --color-text-secondary: rgba(0, 0, 0, 0.7);
    --color-border: rgba(128, 128, 128, 0.3);
    --color-gauge-bg: #ffffff;
    --color-gauge-track: rgba(255, 255, 255, 0.3);
    
    /* Unified shadow system */
    --shadow-base: 0 1px 3px rgba(0, 0, 0, 0.12);
    --shadow-elevated: 0 2px 4px rgba(0, 0, 0, 0.2);
    --shadow-high: 0 4px 8px rgba(0, 0, 0, 0.25);
    
    /* Unified transitions */
    --transition-timing: cubic-bezier(0.4, 0, 0.2, 1);
    --transition-fast: 150ms var(--transition-timing);
    --transition-base: 250ms var(--transition-timing);
    --transition-slow: 350ms var(--transition-timing);
    --transition-gauge: 500ms var(--transition-timing);
    
    /* Border radius scale */
    --radius-base: 4px;
    --radius-sm: var(--radius-base);
    --radius-md: calc(var(--radius-base) * 2);
    --radius-lg: calc(var(--radius-base) * 5.25);
    --radius-full: 50%;
    
    /* Z-index layers */
    --z-base: 1;
    --z-dropdown: 10;
    --z-overlay: 100;
  }

  /* Dark mode overrides */
  @media (prefers-color-scheme: dark) {
    :root {
      --color-surface: #1a1a1a;
      --color-text: #ffffff;
      --color-text-secondary: rgba(255, 255, 255, 0.7);
      --color-border: rgba(255, 255, 255, 0.2);
      --color-gauge-bg: rgba(255, 255, 255, 0.1);
      --color-gauge-track: rgba(255, 255, 255, 0.2);
    }
  }

  /* Responsive scaling - single breakpoint system */
  @media screen and (max-width: 480px), (max-height: 480px) {
    :root {
      --base-size: 0.85;
      --font-base: 0.75rem;
    }
  }

  @media screen and (min-width: 768px) {
    :root {
      --base-size: 1.15;
      --spacing-unit: 0.3125rem;
    }
  }

  /* ============================================
     GLOBAL STYLES
     ============================================ */
  html, body {
    width: 100%;
    height: 100%;
    overflow: hidden;
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Oxygen-Sans, Ubuntu, Cantarell, "Helvetica Neue", sans-serif;
    font-weight: 400;
    line-height: 1.5;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    background-color: transparent;
    touch-action: none;
  }

  /* ============================================
     CONTAINER STYLES
     ============================================ */
  .thermostat-container {
    width: 100%;
    height: 100%;
    display: flex;
    flex-direction: column;
    padding: 10px;
    position: relative;
    transition: background-color var(--transition-slow), color var(--transition-slow);
    background-color: transparent;
    color: var(--color-text);
  }

  .thermostat-container.active {
    background-color: var(--color-primary);
    color: var(--color-surface);
  }

  .thermostat-container.active.heating {
    background-color: var(--color-heating);
  }

  /* ============================================
     EXTERNAL SENSORS
     ============================================ */
  .external-sensors {
    position: absolute;
    top: 10px;
    left: 10px;
    right: 10px;
    display: flex;
    justify-content: space-between;
    z-index: var(--z-dropdown);
    pointer-events: none;
  }

  .sensor-temp,
  .sensor-contact {
    display: none;
    align-items: center;
    justify-content: center;
    gap: 2px;
    padding: 2px 4px;
    background-color: rgba(255, 255, 255, 0.95);
    border-radius: var(--radius-md);
    box-shadow: var(--shadow-base);
    font-size: calc(var(--font-size-sm) * var(--sensor-scale, 1));
    font-weight: 400;
    width: calc(50px * var(--sensor-scale, 1));
  }
  
  .sensor-icon {
    fill: currentColor;
    opacity: 0.8;
    width: calc(22px * var(--sensor-scale, 1));
    height: calc(22px * var(--sensor-scale, 1));
  }

  .sensor-temp.visible,
  .sensor-contact.visible {
    display: flex;
  }

  .thermostat-container.active .sensor-temp,
  .thermostat-container.active .sensor-contact {
    background-color: rgba(255, 255, 255, 0.2);
    color: var(--color-surface);
  }

  .sensor-contact .sensor-icon.closed {
    display: none;
  }

  .sensor-contact.closed .sensor-icon.open {
    display: none;
  }

  .sensor-contact.closed .sensor-icon.closed {
    display: block;
  }

  .sensor-value {
    font-weight: 400;
    font-size: calc(var(--font-size-base) * var(--sensor-scale, 1) * 1.1);
  }

  /* ============================================
     LAYOUT COMPONENTS
     ============================================ */
  .controls-wrapper {
    height: 100%;
    display: flex;
    align-items: center;
    justify-content: center;
    flex: 0 0 auto;
    margin-top: calc(var(--controls-offset, -10) * 1px);
    margin-bottom: 4px;
  }

  .temp-button-container {
    flex: 0 0 40px;
    display: flex;
    align-items: var(--button-align, flex-start);
    margin-top: calc(var(--button-margin, 15) * 1px);
    justify-content: center;
  }

  .temp-button-container.left {
    margin-right: calc(var(--button-spacing, -30) * 1px);
  }

  .temp-button-container.right {
    margin-left: calc(var(--button-spacing, -30) * 1px);
  }

  .gauge-section {
    flex: 1;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    max-width: 200px;
    margin-top: -10px;
  }

  .bottom-controls {
    display: flex;
    flex-direction: column;
    gap: 3px;
    margin-top: calc(var(--bottom-margin, -20) * 1px);
    padding-bottom: 0;
  }
    
  /* ============================================
     GAUGE COMPONENT
     ============================================ */
  .gauge-wrapper {
    position: relative;
    width: var(--gauge-size);
    height: var(--gauge-size);
  }

  .gauge-svg {
    position: absolute;
    width: 100%;
    height: 100%;
    top: 0;
    left: 0;
    transform: rotate(0deg);
  }

  .gauge-track,
  .gauge-progress {
    fill: none;
    stroke-width: var(--gauge-stroke);
    stroke-linecap: round;
  }

  .gauge-track {
    stroke: var(--color-gauge-track);
  }

  .gauge-progress {
    stroke: var(--color-secondary);
    transition: stroke-dashoffset var(--transition-gauge), stroke var(--transition-slow);
  }

  .thermostat-container.active .gauge-track {
    stroke: var(--color-gauge-bg);
  }

  .thermostat-container.active.heating .gauge-progress {
    stroke: var(--color-heating);
  }

  /* ============================================
     TEMPERATURE DISPLAY
     ============================================ */
  .gauge-center {
    position: absolute;
    top: 60%;
    left: 50%;
    transform: translate(-50%, -50%);
    text-align: center;
    color: var(--color-surface); 
  }

  .mode-text {
    font-size: var(--font-size-xs);
    font-weight: 400;
    opacity: 0.7;
    margin-bottom: 0;
    letter-spacing: 0.1px;
    text-transform: uppercase;
  }

  .temperature-display {
    font-size: var(--font-size-2xl);
    font-weight: 400;
    line-height: 1;
    display: flex;
    align-items: flex-start;
    justify-content: center;
  }

  .temperature-value {
    font-size: inherit;
  }

  .temperature-unit {
    font-size: 0.5em;
    margin-left: 2px;
    margin-top: 0.1em;
    opacity: 0.7;
  }

  /* ============================================
     BUTTON STYLES - UNIFIED
     ============================================ */
  .button {
    cursor: pointer;
    transition: all var(--transition-fast);
    -webkit-appearance: none;
    appearance: none;
    outline: none;
    border: none;
    position: relative;
    overflow: hidden;
  }

  .button:active {
    transform: scale(0.95);
  }

  .button:disabled {
    opacity: 0.5;
    cursor: not-allowed;
  }

  .temp-button {
    width: var(--button-size);
    height: var(--button-size);
    border-radius: var(--radius-full);
    border: 2px solid var(--color-border);
    background-color: var(--color-surface);
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .temp-button svg {
    width: 24px;
    height: 24px;
    fill: var(--color-text);
    opacity: 0.7;
  }

  .thermostat-container.active .temp-button {
    background-color: var(--color-secondary);
    border-color: var(--color-secondary);
  }

  .thermostat-container.active .temp-button svg {
    fill: var(--color-surface);
    opacity: 1;
  }

  .fan-buttons {
    display: flex;
    gap: 4px;
    height: var(--slider-height);
  }

  .fan-button {
    flex: 1;
    background-color: var(--color-surface);
    color: var(--color-text);
    border-radius: var(--radius-lg);
    font-size: 18px;
    font-weight: 400;
    padding: 0 8px;
    min-width: 0;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
  }

  .fan-button.active {
    background-color: var(--color-secondary);
    color: var(--color-surface);
  }

  .thermostat-container.active .fan-button {
    background-color: var(--color-surface);
    color: var(--color-text);
  }

  .thermostat-container.active .fan-button.active {
    background-color: var(--color-secondary);
    color: var(--color-surface);
  }

  /* ============================================
     POWER TOGGLE SWITCH
     ============================================ */
  .power-control {
    position: relative;
    height: var(--slider-height);
    margin-top: 8px;
    background-color: var(--color-surface);
    border-radius: var(--radius-lg);
    cursor: pointer;
    overflow: hidden;
    transition: background-color var(--transition-slow);
  }

  .power-control.active {
    background-color: var(--color-secondary);
  }

  .power-control.active.heating {
    background-color: var(--color-heating);
  }

  .power-slider {
    position: absolute;
    top: 0;
    left: 0;
    width: var(--slider-width);
    height: var(--slider-height);
    background-color: var(--color-surface);
    border-radius: var(--radius-lg);
    box-shadow: var(--shadow-elevated);
    transition: left var(--transition-slow);
    display: flex;
    align-items: center;
    justify-content: center;
    cursor: grab;
    touch-action: none;
  }

  .power-slider:active {
    cursor: grabbing;
  }

  .power-slider.dragging {
    transition: none;
  }

  .power-control.active .power-slider {
    left: calc(100% - var(--slider-width));
  }

  .power-slider svg,
  .power-label svg {
    width: 20px;
    height: 20px;
    fill: currentColor;
  }

  .power-label {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    font-size: 18px;
    font-weight: 400;
    letter-spacing: 0.5px;
    pointer-events: none;
    color: var(--color-text);
    z-index: var(--z-base);
  }

  .power-control.active .power-label {
    color: var(--color-surface);
  }

  /* ============================================
     STATES
     ============================================ */
  .unconfigured {
    display: flex;
    align-items: center;
    justify-content: center;
    height: 100%;
    text-align: center;
    opacity: 0.7;
    font-size: var(--font-size-base);
    padding: var(--spacing-md);
  }

  .loading {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    display: none;
  }

  .loading.active {
    display: block;
  }

  .loading-spinner {
    width: 40px;
    height: 40px;
    border: 3px solid var(--color-border);
    border-top-color: var(--color-primary);
    border-radius: var(--radius-full);
    animation: spin 1s linear infinite;
  }

  @keyframes spin {
    to { transform: rotate(360deg); }
  }

  /* ============================================
     ACCESSIBILITY & PLATFORM FIXES
     ============================================ */
  @media (prefers-reduced-motion: reduce) {
    * {
      animation-duration: 0.01ms !important;
      animation-iteration-count: 1 !important;
      transition-duration: 0.01ms !important;
    }
  }

  .button:focus-visible {
    outline: 2px solid var(--color-primary);
    outline-offset: 2px;
  }

  @media (hover: hover) and (pointer: fine) {
    .button:hover {
      opacity: 0.9;
    }
    
    .temp-button:hover {
      background-color: var(--color-primary);
      border-color: var(--color-primary);
    }
    
    .temp-button:hover svg {
      fill: var(--color-surface);
    }
    
    .fan-button:hover:not(.active) {
      background-color: rgba(33, 150, 243, 0.1);
    }
  }

  /* ============================================
     UTILITY CLASSES
     ============================================ */
  .hidden { display: none !important; }
  .invisible { visibility: hidden !important; }

  /* Safe area support */
  @supports (padding: max(0px)) {
    .thermostat-container {
      padding-left: max(10px, env(safe-area-inset-left));
      padding-right: max(10px, env(safe-area-inset-right));
      padding-top: max(10px, env(safe-area-inset-top));
      padding-bottom: max(10px, env(safe-area-inset-bottom));
    }
  }
  </style>
</head>
<body>

<div id="thermostatContainer" class="thermostat-container">
  <!-- External Sensors Display -->
  <div class="external-sensors">
    <div class="sensor-temp" id="externalTemp">
      <svg class="sensor-icon" viewBox="0 0 24 24">
        <path d="M15 13V5A3 3 0 0 0 9 5V13A5 5 0 1 0 15 13M12 4A1 1 0 0 1 13 5V8H11V5A1 1 0 0 1 12 4Z"></path>
      </svg>
      <span class="sensor-value"></span>
    </div>
    <div class="sensor-contact" id="externalContact">
      <svg class="sensor-icon open" viewBox="0 0 24 24">
        <path d="M12 6V9L16 5L12 1V4A8 8 0 0 0 4 12C4 13.57 4.46 15.03 5.24 16.26L6.7 14.8C6.25 13.97 6 13 6 12A6 6 0 0 1 12 6M18.76 7.74L17.3 9.2C17.74 10.04 18 11 18 12A6 6 0 0 1 12 18V15L8 19L12 23V20A8 8 0 0 0 20 12C20 10.43 19.54 8.97 18.76 7.74Z"/>
      </svg>
      <svg class="sensor-icon closed" viewBox="0 0 24 24">
        <path d="M12 6V9L16 5L12 1V4C7.58 4 4 7.58 4 12C4 13.57 4.46 15.03 5.24 16.26L6.7 14.8C6.25 13.97 6 13 6 12C6 8.69 8.69 6 12 6M18.76 7.74L17.3 9.2C17.74 10.04 18 11 18 12C18 15.31 15.31 18 12 18V15L8 19L12 23V20C16.42 20 20 16.42 20 12C20 10.43 19.54 8.97 18.76 7.74M1 4.27L2.28 5.55L20.73 24L22 22.73L3.27 4L1 4.27Z"/>
      </svg>
      <span class="sensor-value"></span>
    </div>
  </div>

  <!-- Loading State -->
  <div class="loading" id="loadingIndicator">
    <div class="loading-spinner"></div>
  </div>

  <!-- Configured View -->
  <div id="configuredView" class="hidden">
    <!-- Main Controls -->
    <div class="controls-wrapper">
      <div class="temp-button-container left">
        <button class="button temp-button" id="tempDown" type="button" aria-label="Decrease temperature">
          <svg viewBox="0 0 24 24">
            <path d="M19 13H5v-2h14v2z"></path>
          </svg>
        </button>
      </div>
      
      <div class="gauge-section">
        <div class="gauge-wrapper">
          <svg class="gauge-svg" viewBox="0 0 150 150">
            <path class="gauge-track" d="M 25 125 A 58 58 0 1 1 125 125"></path>
            <path class="gauge-progress" id="gaugeProgress" d="M 25 125 A 58 58 0 1 1 125 125"></path>
          </svg>
          <div class="gauge-center">
            <div class="mode-text" id="modeText">Off</div>
            <div class="temperature-display">
              <span class="temperature-value" id="tempValue">--</span>
              <span class="temperature-unit">°C</span>
            </div>
          </div>
        </div>
      </div>
      
      <div class="temp-button-container right">
        <button class="button temp-button" id="tempUp" type="button" aria-label="Increase temperature">
          <svg viewBox="0 0 24 24">
            <path d="M19 13h-6v6h-2v-6H5v-2h6V5h2v6h6v2z"></path>
          </svg>
        </button>
      </div>
    </div>

    <!-- Bottom Controls -->
    <div class="bottom-controls">
      <!-- Fan Speed Buttons -->
      <div class="fan-buttons" id="fanButtons" role="group" aria-label="Fan speed controls">
        <!-- Dynamically generated -->
      </div>

      <!-- Power Toggle -->
      <div class="power-control" id="powerControl" role="switch" aria-checked="false">
        <div class="power-slider" id="powerSlider">
          <svg viewBox="0 0 24 24">
            <path d="M16.56,5.44L15.11,6.89C16.84,7.94 18,9.83 18,12A6,6 0 0,1 12,18A6,6 0 0,1 6,12C6,9.83 7.16,7.94 8.88,6.88L7.44,5.44C5.36,6.88 4,9.28 4,12A8,8 0 0,0 12,20A8,8 0 0,0 20,12C20,9.28 18.64,6.88 16.56,5.44M13,3H11V13H13"></path>
          </svg>
        </div>
        <span class="power-label" id="powerLabel">OFF</span>
      </div>
    </div>
  </div>

  <!-- Unconfigured View -->
  <div id="unconfiguredView" class="unconfigured">
    Please configure a thermostat device
  </div>
</div>

<script>
(function() {
  'use strict';

  /* ============================================
     AC CONTROL UNIFIED ARCHITECTURE
     Version: 5.3 - Optimized Edition
     ============================================ */

  // Configuration module
  const Config = {
    device: {
      id: null,
      label: '',
      activeColor: '#0094C9',
      showCurrentTemp: true,
      minTemp: 18,
      maxTemp: 28
    },
    externalSensors: {
      tempSensor: null,
      contactSensor: null
    },
    fanOptions: {
      on: false,
      low: true,
      medium: true,
      high: true,
      auto: false
    },
    defaults: {
      activeColor: '#0094C9',
      heatingColor: '#ff6b6b',
      transitionDuration: 300,
      debounceDelay: 500
    },
    configured: false
  };

  // State management module
  const State = {
    current: {
      mode: 'off',
      fanSpeed: 'low',
      setpoint: 22,
      currentTemp: null,
      externalTemp: null,
      contactStatus: null,
      isDragging: false,
      isTransitioning: false
    },
    
    update(key, value) {
      if (this.current[key] !== value) {
        this.current[key] = value;
        return true;
      }
      return false;
    }
  };

  // DOM references module
  const DOM = {
    container: null,
    views: {},
    controls: {},
    display: {},
    sensors: {},
    
    init() {
      this.container = document.getElementById('thermostatContainer');
      
      this.views = {
        configured: document.getElementById('configuredView'),
        unconfigured: document.getElementById('unconfiguredView'),
        loading: document.getElementById('loadingIndicator')
      };
      
      this.controls = {
        tempUp: document.getElementById('tempUp'),
        tempDown: document.getElementById('tempDown'),
        fanButtons: document.getElementById('fanButtons'),
        powerControl: document.getElementById('powerControl'),
        powerSlider: document.getElementById('powerSlider')
      };
      
      this.display = {
        modeText: document.getElementById('modeText'),
        tempValue: document.getElementById('tempValue'),
        powerLabel: document.getElementById('powerLabel'),
        gaugeProgress: document.getElementById('gaugeProgress')
      };
      
      this.sensors = {
        externalTemp: document.getElementById('externalTemp'),
        externalContact: document.getElementById('externalContact')
      };
    }
  };

  // Utility functions module
  const Utils = {
    debounce(func, wait) {
      let timeout;
      return function executedFunction(...args) {
        const later = () => {
          clearTimeout(timeout);
          func(...args);
        };
        clearTimeout(timeout);
        timeout = setTimeout(later, wait);
      };
    },
    
    clamp(value, min, max) {
      return Math.max(min, Math.min(max, value));
    },
    
    ensureHexColor(color) {
      if (!color) return Config.defaults.activeColor;
      if (color.charAt(0) === '#') return color;
      
      const colorMap = {
        'white': '#ffffff', 'black': '#000000',
        'red': '#ff0000', 'green': '#008000',
        'blue': '#0000ff', 'gray': '#808080', 'grey': '#808080'
      };
      
      return colorMap[color.toLowerCase()] || Config.defaults.activeColor;
    },
    
    getEventPosition(e) {
      return e.touches ? e.touches[0].clientX : e.clientX;
    },
    
    preventDefault(e) {
      e.preventDefault();
      e.stopPropagation();
    }
  };

  // Dynamic sizing module - consolidated
  const DynamicSizing = {
    sizeRanges: [
      { min: 0,   max: 150, fontSize: '1.5rem', controls: -17, button: 15,  spacing: -20, bottom: -20 },
      { min: 150, max: 200, fontSize: '3rem',   controls: 30,  button: 15,  spacing: 40,  bottom: -20 },
      { min: 200, max: 250, fontSize: '4.5rem', controls: 20,  button: 0,   spacing: 80,  bottom: -15 },
      { min: 250, max: 350, fontSize: '5.5rem', controls: 15,  button: 0,   spacing: 150, bottom: -10 },
      { min: 350, max: 999, fontSize: '6.5rem', controls: 10,  button: 0,   spacing: 210, bottom: -10 }
    ],
    
    init() {
      this.updateSizes();
      window.addEventListener('resize', Utils.debounce(() => this.updateSizes(), 250));
    },

    updateSizes() {
      const container = DOM.container;
      if (!container) return;
    
      const width = container.offsetWidth;
      const height = container.offsetHeight;
      
      // Calculate gauge size
      const baseOnWidth = width * 0.35;
      const baseOnHeight = height * 0.65;
      const gaugeSize = Math.min(400, Math.max(100, Math.min(baseOnWidth, baseOnHeight)));
      
      // Get size range configuration
      const config = this.sizeRanges.find(r => gaugeSize >= r.min && gaugeSize < r.max) || this.sizeRanges[0];
      
      // Calculate button size
      const buttonSize = Math.min(70, gaugeSize * 0.25);
      
      // Calculate sensor scaling
      const sensorScale = gaugeSize > 350 ? 2.5 : gaugeSize > 250 ? 1.5 : gaugeSize > 150 ? 0.8 : 1;

      // Apply CSS variables
      document.documentElement.style.setProperty('--gauge-size', `${gaugeSize}px`);
      document.documentElement.style.setProperty('--font-size-2xl', config.fontSize);
      document.documentElement.style.setProperty('--button-size', `${buttonSize}px`);
      document.documentElement.style.setProperty('--button-spacing', config.spacing);
      document.documentElement.style.setProperty('--bottom-margin', config.bottom);
      document.documentElement.style.setProperty('--controls-offset', config.controls);
      document.documentElement.style.setProperty('--button-margin', config.button);
      document.documentElement.style.setProperty('--button-align', gaugeSize > 200 ? 'center' : 'flex-start');
      document.documentElement.style.setProperty('--sensor-scale', sensorScale);
    }
  };

  // Gauge management module
  const Gauge = {
    radius: 58,
    circumference: 0,
    arcLength: 0,
    
    init() {
      this.circumference = 2 * Math.PI * this.radius;
      this.arcLength = this.circumference * 0.75; // 270° arc
      
      if (DOM.display.gaugeProgress) {
        DOM.display.gaugeProgress.style.strokeDasharray = `${this.arcLength} ${this.circumference}`;
      }
    },
    
    update(value, min, max) {
      const percentage = Utils.clamp((value - min) / (max - min), 0, 1);
      const dashOffset = this.arcLength * (1 - percentage);
      
      if (DOM.display.gaugeProgress) {
        DOM.display.gaugeProgress.style.strokeDashoffset = dashOffset;
      }
    },
    
    setMode(mode) {
      if (!DOM.display.gaugeProgress) return;
      
      DOM.display.gaugeProgress.classList.remove('heating', 'off');
      
      if (mode === 'heat') {
        DOM.display.gaugeProgress.classList.add('heating');
      } else if (mode === 'off') {
        DOM.display.gaugeProgress.classList.add('off');
      }
    }
  };

  // External sensors module - consolidated update method
  const ExternalSensors = {
    init() {
      if (Config.externalSensors.tempSensor) {
        this.subscribeTo(Config.externalSensors.tempSensor, 'temperature', (v) => this.updateTemp(v));
        DOM.sensors.externalTemp?.classList.add('visible');
      }
      
      if (Config.externalSensors.contactSensor) {
        this.subscribeTo(Config.externalSensors.contactSensor, 'contact', (v) => this.updateContact(v));
        DOM.sensors.externalContact?.classList.add('visible');
      }
    },
    
    subscribeTo(sensor, attribute, callback) {
      if (!sensor?.attributes?.[attribute]) return;
      
      const attr = sensor.attributes[attribute];
      callback(attr.value);
      attr.onValue(callback);
    },
    
    updateTemp(value) {
      if (!DOM.sensors.externalTemp) return;
      State.update('externalTemp', value);
      const span = DOM.sensors.externalTemp.querySelector('.sensor-value');
      if (span) span.textContent = value ? Math.round(value) + '°' : '--';
    },
    
    updateContact(value) {
      if (!DOM.sensors.externalContact) return;
      State.update('contactStatus', value);
      DOM.sensors.externalContact.classList.toggle('closed', value === 'closed');
    }
  };

  // Fan control module
  const FanControl = {
    options: [],
    
    init() {
      const opts = Config.fanOptions;
      this.options = [
        opts.on && { value: 'on', label: 'On' },
        opts.low && { value: 'low', label: 'Low' },
        opts.medium && { value: 'medium', label: 'Med' },
        opts.high && { value: 'high', label: 'High' },
        opts.auto && { value: 'auto', label: 'Auto' }
      ].filter(Boolean);
      
      this.render();
    },
    
    render() {
      const container = DOM.controls.fanButtons;
      if (!container) return;
      
      container.innerHTML = this.options.map(opt => `
        <button class="button fan-button ${State.current.fanSpeed === opt.value ? 'active' : ''}" 
                data-value="${opt.value}" type="button" 
                aria-label="Fan speed ${opt.label}">${opt.label}</button>
      `).join('');
      
      container.addEventListener('click', (e) => {
        if (e.target.classList.contains('fan-button')) {
          this.handleClick(e.target.dataset.value);
        }
      });
    },
    
    handleClick(value) {
      if (State.current.fanSpeed === value) return;
      State.update('fanSpeed', value);
      this.updateButtons();
      DeviceControl.sendCommand('setThermostatFanMode', value);
    },
    
    updateButtons() {
      document.querySelectorAll('.fan-button').forEach(btn => {
        btn.classList.toggle('active', btn.dataset.value === State.current.fanSpeed);
      });
    }
  };

  // Power control module - consolidated drag handling
  const PowerControl = {
    dragState: { startX: 0, sliderLeft: 0, maxLeft: 0 },
    
    init() {
      const control = DOM.controls.powerControl;
      const slider = DOM.controls.powerSlider;
      
      if (!control || !slider) return;
      
      control.addEventListener('click', (e) => {
        if (!State.current.isDragging && 
            (e.target === control || e.target.classList.contains('power-label'))) {
          this.toggle();
        }
      });
      
      // Unified event handler
      const handleStart = (e) => this.startDrag(e);
      const handleMove = (e) => this.drag(e);
      const handleEnd = (e) => this.endDrag(e);
      
      // Touch events
      slider.addEventListener('touchstart', handleStart, { passive: false });
      document.addEventListener('touchmove', handleMove, { passive: false });
      document.addEventListener('touchend', handleEnd, { passive: false });
      
      // Mouse events
      slider.addEventListener('mousedown', handleStart);
      document.addEventListener('mousemove', handleMove);
      document.addEventListener('mouseup', handleEnd);
    },
    
    startDrag(e) {
      State.current.isDragging = true;
      const control = DOM.controls.powerControl;
      const slider = DOM.controls.powerSlider;
      
      this.dragState.maxLeft = control.offsetWidth - slider.offsetWidth;
      this.dragState.startX = Utils.getEventPosition(e);
      this.dragState.sliderLeft = parseInt(window.getComputedStyle(slider).left) || 0;
      
      slider.classList.add('dragging');
      Utils.preventDefault(e);
    },
    
    drag(e) {
      if (!State.current.isDragging) return;
      
      const diff = Utils.getEventPosition(e) - this.dragState.startX;
      const newLeft = Utils.clamp(this.dragState.sliderLeft + diff, 0, this.dragState.maxLeft);
      
      DOM.controls.powerSlider.style.left = `${newLeft}px`;
      Utils.preventDefault(e);
    },
    
    endDrag(e) {
      if (!State.current.isDragging) return;
      
      State.current.isDragging = false;
      DOM.controls.powerSlider.classList.remove('dragging');
      
      const currentLeft = parseInt(DOM.controls.powerSlider.style.left) || 0;
      const threshold = this.dragState.maxLeft / 2;
      
      DOM.controls.powerSlider.style.left = '';
      
      const shouldBeOn = currentLeft > threshold;
      const isOff = State.current.mode === 'off';
      
      if (shouldBeOn !== !isOff) {
        DeviceControl.sendCommand('setThermostatMode', shouldBeOn ? 'cool' : 'off');
      }
      
      Utils.preventDefault(e);
    },
    
    toggle() {
      DeviceControl.sendCommand('setThermostatMode', State.current.mode === 'off' ? 'cool' : 'off');
    },
    
    update() {
      const control = DOM.controls.powerControl;
      const label = DOM.display.powerLabel;
      
      if (!control || !label) return;
      
      const isActive = State.current.mode !== 'off';
      control.classList.toggle('active', isActive);
      control.classList.toggle('heating', State.current.mode === 'heat');
      control.setAttribute('aria-checked', isActive);
      
      label.innerHTML = isActive ? 
        '<svg viewBox="0 0 24 24" style="width:30px;height:30px;fill:currentColor;vertical-align:middle"><path d="M20.79,13.95L18.46,14.57L16.46,13.44V10.56L18.46,9.43L20.79,10.05L21.31,8.12L19.54,7.65L20,5.88L18.07,5.36L17.45,7.69L15.45,8.82L13,7.38V5.12L14.71,3.41L13.29,2L12,3.29L10.71,2L9.29,3.41L11,5.12V7.38L8.5,8.82L6.5,7.69L5.92,5.36L4,5.88L4.47,7.65L2.7,8.12L3.22,10.05L5.55,9.43L7.55,10.56V13.45L5.55,14.58L3.22,13.96L2.7,15.89L4.47,16.36L4,18.12L5.93,18.64L6.55,16.31L8.55,15.18L11,16.62V18.88L9.29,20.59L10.71,22L12,20.71L13.29,22L14.7,20.59L13,18.88V16.62L15.45,15.18L17.45,16.31L18.07,18.64L20,18.12L19.53,16.36L21.3,15.89L20.79,13.95M9.5,10.56L12,9.11L14.5,10.56V13.44L12,14.89L9.5,13.44V10.56Z"/></svg>' : 
        'OFF';
    }
  };

  // Temperature control module
  const TempControl = {
    init() {
      DOM.controls.tempUp?.addEventListener('click', () => this.adjust(1));
      DOM.controls.tempDown?.addEventListener('click', () => this.adjust(-1));
    },
    
    adjust: Utils.debounce(function(delta) {
      const newTemp = Utils.clamp(
        State.current.setpoint + delta,
        Config.device.minTemp,
        Config.device.maxTemp
      );
      
      if (newTemp !== State.current.setpoint) {
        State.update('setpoint', newTemp);
        UI.update();
        DeviceControl.sendCommand('setCoolingSetpoint', newTemp);
      }
    }, 300)
  };

  // UI management module
  const UI = {
    init() {
      Gauge.init();
      FanControl.init();
      PowerControl.init();
      TempControl.init();
      ExternalSensors.init();
      this.applyTheme();
    },
    
    update() {
      if (DOM.display.tempValue) {
        DOM.display.tempValue.textContent = Math.round(State.current.setpoint);
      }
      
      if (DOM.display.modeText) {
        DOM.display.modeText.textContent = 
          State.current.mode === 'cool' ? 'Cool' : 
          State.current.mode === 'heat' ? 'Heat' : 'Off';
      }
      
      Gauge.update(State.current.setpoint, Config.device.minTemp, Config.device.maxTemp);
      Gauge.setMode(State.current.mode);
      FanControl.updateButtons();
      PowerControl.update();
      this.applyTheme();
    },
    
    applyTheme() {
      const container = DOM.container;
      if (!container) return;
      
      const isActive = State.current.mode !== 'off';
      container.classList.toggle('active', isActive);
      container.classList.toggle('heating', State.current.mode === 'heat');
      
      if (isActive) {
        const color = State.current.mode === 'heat' ? 
                     Config.defaults.heatingColor : 
                     Utils.ensureHexColor(Config.device.activeColor);
        container.style.setProperty('--color-primary', color);
      }
    },
    
    showView(viewName) {
      Object.values(DOM.views).forEach(v => v?.classList.add('hidden'));
      DOM.views[viewName]?.classList.remove('hidden');
    }
  };

  // Device control module - consolidated subscriptions
  const DeviceControl = {
    device: null,
    subscriptions: [],
    lastCommand: { cmd: '', value: '', time: 0 },
    
    init(device) {
      this.device = device;
      this.clearSubscriptions();
      this.updateFromDevice();
      this.subscribe();
      UI.showView('configured');
    },
    
    sendCommand(command, value) {
      if (!this.device) return;
      
      const now = Date.now();
      if (this.lastCommand.cmd === command && 
          this.lastCommand.value === value && 
          (now - this.lastCommand.time) < 500) {
        return;
      }
      
      this.lastCommand = { cmd: command, value, time: now };
      
      if (this.device.sendCommand) {
        this.device.sendCommand(command, value !== undefined ? [value] : undefined);
      }
    },
    
    updateFromDevice() {
      if (!this.device?.attributes) return;
      
      const attrs = this.device.attributes;
      const updates = {
        thermostatMode: 'mode',
        thermostatFanMode: 'fanSpeed',
        coolingSetpoint: 'setpoint',
        temperature: 'currentTemp'
      };
      
      Object.entries(updates).forEach(([attr, state]) => {
        if (attrs[attr]) {
          State.update(state, attrs[attr].value || (state === 'mode' ? 'off' : state === 'fanSpeed' ? 'low' : 22));
        }
      });
      
      UI.update();
    },
    
    subscribe() {
      if (!this.device?.attributes) return;
      
      const attrs = this.device.attributes;
      const subscriptions = {
        thermostatMode: 'mode',
        thermostatFanMode: 'fanSpeed',
        coolingSetpoint: 'setpoint',
        temperature: 'currentTemp'
      };
      
      Object.entries(subscriptions).forEach(([attr, state]) => {
        if (attrs[attr]) {
          const sub = attrs[attr].onValue((value) => {
            if (State.update(state, value || (state === 'mode' ? 'off' : state === 'fanSpeed' ? 'low' : 22))) {
              if (state !== 'currentTemp') UI.update();
            }
          });
          this.subscriptions.push(sub);
        }
      });
    },
    
    clearSubscriptions() {
      this.subscriptions.forEach(sub => sub?.off?.());
      this.subscriptions = [];
    }
  };

  // SharpTools integration module
  const SharpToolsIntegration = {
    init() {
      if (typeof stio === 'undefined') {
        setTimeout(() => this.init(), 200);
        return;
      }
      
      try {
        stio.ready((data) => this.processConfiguration(data.settings));
      } catch (error) {
        console.error('SharpTools initialization error:', error);
        UI.showView('unconfigured');
      }
    },
    
    processConfiguration(settings) {
      const device = settings.selectedDevice;
      
      if (!device) {
        UI.showView('unconfigured');
        return;
      }
      
      // Update configuration
      Config.device = {
        id: device.id || device._id || device.thingId || device.name || 'default',
        label: settings.customLabel || device.name || 'AC Control',
        activeColor: Utils.ensureHexColor(settings.activeColor),
        showCurrentTemp: settings.showCurrentTemp !== false,
        minTemp: settings.minTemp || 18,
        maxTemp: settings.maxTemp || 28
      };
      
      Config.externalSensors = {
        tempSensor: settings.tempSensor || null,
        contactSensor: settings.contactSensor || null
      };
      
      Config.fanOptions = {
        on: settings.fanHasOn === true,
        low: settings.fanHasLow !== false,
        medium: settings.fanHasMedium !== false,
        high: settings.fanHasHigh !== false,
        auto: settings.fanHasAuto === true
      };
      
      Config.configured = true;
      
      UI.init();
      DeviceControl.init(device);
    }
  };

  // Application controller
  const App = {
    init() {
      DOM.init();
      DynamicSizing.init();
      this.setupGlobalHandlers();
      SharpToolsIntegration.init();
    },
    
    setupGlobalHandlers() {
      // Prevent iOS bounce
      document.addEventListener('touchmove', (e) => {
        if (!e.target.closest('.power-slider')) e.preventDefault();
      }, { passive: false });
      
      // Cleanup on unload
      window.addEventListener('beforeunload', () => DeviceControl.clearSubscriptions());
      
      // Handle visibility changes
      document.addEventListener('visibilitychange', () => {
        if (!document.hidden && Config.configured) {
          DeviceControl.updateFromDevice();
        }
      });
    }
  };

  // Initialize when DOM is ready
  if (document.readyState === 'loading') {
    document.addEventListener('DOMContentLoaded', () => App.init());
  } else {
    App.init();
  }

})();
</script>

</body>
</html>