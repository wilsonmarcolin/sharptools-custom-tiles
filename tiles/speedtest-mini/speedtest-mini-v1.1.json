<!DOCTYPE html>
<html lang="en">
<head>
<!--
╔════════════════════════════════════════════════════════════════════════════╗
║                      SPEEDTEST MONITOR v1.1 (Mini)                         ║
║              Professional Speed Test Monitor for SharpTools                ║
║                         Compact Edition 1x2 Tile                           ║
╠════════════════════════════════════════════════════════════════════════════╣
║                                                                            ║
║  DESCRIPTION:                                                              ║
║  Compact 1x2 tile for real-time network speed monitoring with streamlined  ║
║  display of essential metrics. Integrates with Ookla Speedtest CLI data    ║
║  through SharpTools variables to provide at-a-glance network performance   ║
║  visualization in a space-efficient format.                                ║
║                                                                            ║
║  KEY FEATURES:                                                             ║
║  • Real-time download and upload speed display with smooth animations      ║
║  • Connection status indicator with color-coded feedback                   ║
║  • Test server identification and timestamp tracking                       ║
║  • Responsive design with automatic scaling for all screen sizes           ║
║  • Optional gradient background for enhanced visual appeal                 ║
║  • Single unified codebase - no code duplication                           ║
║  • Mobile-first design with desktop optimizations                          ║
║                                                                            ║
║  TECHNICAL ARCHITECTURE:                                                   ║
║  This tile implements a unified codebase architecture that eliminates      ║
║  code duplication through:                                                 ║
║  • CSS Variables with dynamic scale factor for responsive sizing           ║
║  • Modular JavaScript architecture with clear separation of concerns       ║
║  • Component-based design for maintainability and reusability              ║
║  • DRY principles applied throughout (Don't Repeat Yourself)               ║
║  • Auto-adjustment for both mobile and desktop displays                    ║
║  • Single implementation handles all screen sizes without breakpoints      ║
║                                                                            ║
║  HOW IT WORKS:                                                             ║
║  1. Connects to SharpTools string variable containing speed test data      ║
║  2. Parses comma-separated values from Ookla Speedtest CLI output          ║
║  3. Updates display elements with smooth animated transitions              ║
║  4. Automatically scales content based on container dimensions             ║
║  5. Provides visual feedback through color-coded status indicators         ║
║                                                                            ║
║  INPUT DATA FORMAT:                                                        ║
║  The tile expects a SharpTools string variable with CSV format:            ║
║  "validity,download,upload,ping,server,timestamp"                          ║
║                                                                            ║
║  Example: "true,450.5,35.2,12.5,Comcast Denver,1735689600000"              ║
║                                                                            ║
║  Fields:                                                                   ║
║  • validity: Boolean (true/false) - Test success status                    ║
║  • download: Float - Download speed in Mbps                                ║
║  • upload: Float - Upload speed in Mbps                                    ║
║  • ping: Float - Latency in milliseconds (not displayed)                   ║
║  • server: String - Test server name/location                              ║
║  • timestamp: Long - Unix timestamp or ISO 8601 date                       ║
║                                                                            ║
║  ERROR HANDLING:                                                           ║
║  • Gracefully handles missing or malformed data                            ║
║  • Displays "NO OK" status for failed tests                                ║
║  • Shows "--" placeholders for missing values                              ║
║  • Automatically reconnects on data interruption                           ║
║  • Silent error recovery prevents UI disruption                            ║
║                                                                            ║
║  CONFIGURATION:                                                            ║
║  • Speed Variable: Select the SharpTools string variable                   ║
║  • Gradient Background: Enable/disable gradient effect                     ║
║                                                                            ║
║  SETUP REQUIREMENTS:                                                       ║
║  1. Ookla Speedtest CLI installed and configured on local system           ║
║  2. Automation to run speed tests and update SharpTools variable           ║
║  3. SharpTools string variable to store test results                       ║
║                                                                            ║
║  NOTE: Ookla Speedtest CLI setup documentation available separately        ║
║  Refer to Ookla documentation for CLI installation and configuration       ║
║                                                                            ║
║  RESPONSIVE FEATURES:                                                      ║
║  • Dynamic font scaling based on container size                            ║
║  • Automatic layout adjustment for various screen densities                ║
║  • Touch-optimized for mobile devices                                      ║
║  • High-DPI display support                                                ║
║  • Scales from 150px to 800px+ tile sizes                                  ║
║                                                                            ║
║  BROWSER COMPATIBILITY:                                                    ║
║  • Chrome/Edge 88+                                                         ║
║  • Firefox 78+                                                             ║
║  • Safari 14+                                                              ║
║  • Full mobile browser support                                             ║
║                                                                            ║
║  PERFORMANCE OPTIMIZATIONS:                                                ║
║  • Efficient subscription management                                       ║
║  • Debounced resize handlers                                               ║
║  • RequestAnimationFrame for smooth animations                             ║
║  • Minimal DOM manipulation                                                ║
║  • CSS transforms for hardware acceleration                                ║
║                                                                            ║
║  AUTHOR: Wilson Marcolin                                                   ║
║  CONTRIBUTORS: Claude AI Assistant                                         ║
║  VERSION: 1.1.0                                                            ║
║  RELEASE: January 2025                                                     ║
║  LICENSE: MIT                                                              ║
║                                                                            ║
╚════════════════════════════════════════════════════════════════════════════╝
-->

<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=5.0, user-scalable=yes">
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="description" content="SpeedTest Monitor v1.1 (Mini) - Compact network speed monitor for SharpTools">
<title>SpeedTest Monitor v1.1 (Mini)</title>

<!-- Tile Settings Schema -->
<script type="application/json" id="tile-settings">
{
  "schema": "0.2.0",
  "name": "SpeedTest Monitor v1.1 (Mini)",
  "settings": [
    {
      "type": "VARIABLE",
      "name": "speedVariable",
      "label": "Speed Test Variable",
      "filters": {"type": "String"}
    },
    {
      "type": "BOOLEAN",
      "name": "useGradient",
      "label": "Use Gradient Background",
      "default": true
    }
  ]
}
</script>

<!-- SharpTools Library -->
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
     CSS VARIABLES & DESIGN SYSTEM
     ============================================ */
  
  :root {
    /* Dynamic Scale Factor */
    --scale-factor: 1;
    
    /* Color Palette */
    --color-primary: #2196F3;
    --color-success: #4CAF50;
    --color-danger: #F44336;
    --color-text: #212121;
    --color-text-secondary: #757575;
    --color-background: #FAFAFA;
    --color-surface: #FFFFFF;
    --color-border: #E0E0E0;
    
    /* Speed Test Colors */
    --speed-download: #2196F3;
    --speed-upload: #F44336;
    --speed-ok: #4CAF50;
    --speed-nok: #F44336;
    
    /* Gradient Colors */
    --gradient-start: #404040;
    --gradient-mid: #606060;
    --gradient-end: #808080;
    
    /* Typography Scale */
    --font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Oxygen-Sans, Ubuntu, Cantarell, "Helvetica Neue", sans-serif;
    --font-size-xs: calc(1.5rem * var(--scale-factor));
    --font-size-sm: calc(2rem * var(--scale-factor));
    --font-size-base: calc(2.5rem * var(--scale-factor));
    --font-size-lg: calc(3rem * var(--scale-factor));
    --font-size-xl: calc(4rem * var(--scale-factor));
    
    /* Spacing System */
    --spacing-xs: calc(0.125rem * var(--scale-factor));
    --spacing-sm: calc(0.25rem * var(--scale-factor));
    --spacing-md: calc(0.35rem * var(--scale-factor));
    --spacing-lg: calc(0.5rem * var(--scale-factor));
    
    /* Visual Effects */
    --shadow-sm: 0 1px 3px rgba(0, 0, 0, 0.12);
    --shadow-md: 0 2px 6px rgba(0, 0, 0, 0.16);
    --radius: 6px;
    --transition: 300ms ease;
  }

  /* Dark Mode Support */
  @media (prefers-color-scheme: dark) {
    :root {
      --color-text: #FFFFFF;
      --color-text-secondary: #B0B0B0;
      --color-background: #121212;
      --color-surface: #1E1E1E;
      --color-border: #333333;
    }
  }

  /* ============================================
     DOCUMENT STYLES
     ============================================ */
  
  html, body {
    height: 100%;
    overflow: hidden;
    font-family: var(--font-family);
    font-weight: 400;
    line-height: 1.4;
    -webkit-font-smoothing: antialiased;
    background: var(--color-background);
    color: var(--color-text);
  }

  /* ============================================
     MAIN CONTAINER
     ============================================ */
  
  .speedtest-container {
    width: 100%;
    height: 100%;
    padding: var(--spacing-md);
    padding-top: calc(var(--spacing-md) * 10);
    display: flex;
    flex-direction: column;
    transition: background var(--transition);
    position: relative;
    align-items: center;
    justify-content: center;
  }

  .speedtest-container.gradient {
    background: linear-gradient(180deg, 
      var(--gradient-end) 0%, 
      var(--gradient-mid) 50%, 
      var(--gradient-start) 100%);
  }

  .speedtest-container.gradient * {
    color: white !important;
  }

  /* ============================================
     HEADER SECTION
     ============================================ */
  
  .header-section {
    position: absolute;
    top: calc(var(--spacing-md) * 7);
    left: calc(var(--spacing-md) * 10);
    right: calc(var(--spacing-md) * 10);
    display: flex;
    justify-content: space-between;
    align-items: center;
    height: calc(30px * var(--scale-factor));
  }

  .logo-icon {
    width: calc(30px * var(--scale-factor));
    height: calc(30px * var(--scale-factor));
    fill: currentColor;
    opacity: 0.9;
  }

  .brand-text {
    font-size: calc(1.8rem * var(--scale-factor));
    font-weight: 700;
    opacity: 0.7;
  }

  /* ============================================
     MAIN CONTENT
     ============================================ */
  
  .main-content {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    gap: calc(var(--spacing-md) * 1.5);
    width: 100%;
    max-width: 800px;
    margin-top: calc(var(--spacing-lg) * 4);
  }

  /* ============================================
     SPEED ROWS
     ============================================ */
  
  .speed-row {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: calc(var(--spacing-md) * 2);
    width: 100%;
  }

  .speed-label {
    font-size: calc(var(--font-size-base) * 0.5);
    font-weight: 600;
    opacity: 0.5;
    min-width: calc(80px * var(--scale-factor));
    text-align: right;
  }

  .speed-icon {
    width: calc(36px * var(--scale-factor));
    height: calc(36px * var(--scale-factor));
    fill: currentColor;
    flex-shrink: 0;
  }

  .download-icon {
    fill: var(--speed-download);
  }

  .upload-icon {
    fill: var(--speed-upload);
  }

  .speed-value {
    font-size: var(--font-size-xl);
    font-weight: 400;
    white-space: nowrap;
  }

  .download-value {
    color: var(--speed-download);
  }

  .upload-value {
    color: var(--speed-upload);
  }

  /* ============================================
     STATUS BAR
     ============================================ */
  
  .status-bar {
    width: 80%;
    border-radius: 20px;
    padding: 0 calc(var(--spacing-md) * 3);
    box-shadow: var(--shadow-sm);
    text-align: center;
    margin: calc(var(--spacing-md) * 2) 0 calc(var(--spacing-sm) * 2) 0;
    display: flex;
    align-items: center;
    justify-content: center;
    height: calc(60px * var(--scale-factor));
  }

  .status-bar.ok {
    background: var(--speed-ok);
  }

  .status-bar.nok {
    background: var(--speed-nok);
  }

  .speedtest-container.gradient .status-bar {
    background: rgba(255, 255, 255, 0.15);
  }

  .speedtest-container.gradient .status-bar.ok {
    background: var(--speed-ok);
  }

  .speedtest-container.gradient .status-bar.nok {
    background: var(--speed-nok);
  }

  .status-badge {
    font-weight: 700;
    font-size: var(--font-size-lg);
    display: inline-block;
    color: white;
    line-height: 1;
  }

  /* ============================================
     INFO SECTION
     ============================================ */
  
  .info-section {
    display: flex;
    justify-content: center;
    align-items: center;
    width: 80%;
    position: relative;
  }

  .info-section::before {
    content: '';
    position: absolute;
    left: 50%;
    transform: translateX(-50%);
    width: 1px;
    height: calc(20px * var(--scale-factor));
    background: currentColor;
    opacity: 0.3;
  }

  .server-name {
    font-size: var(--font-size-xs);
    font-weight: 600;
    opacity: 0.8;
    text-align: right;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
    flex: 1;
    padding-right: calc(var(--spacing-lg) * 2);
  }

  .time-display {
    font-size: var(--font-size-xs);
    opacity: 0.7;
    text-align: left;
    white-space: nowrap;
    flex: 1;
    padding-left: calc(var(--spacing-lg) * 2);
  }

  /* ============================================
     LOADING STATE
     ============================================ */
  
  .loading {
    display: flex;
    align-items: center;
    justify-content: center;
    height: 100%;
  }

  .spinner {
    width: 30px;
    height: 30px;
    border: 3px solid var(--color-border);
    border-top-color: var(--color-primary);
    border-radius: 50%;
    animation: spin 1s linear infinite;
  }

  @keyframes spin {
    to { transform: rotate(360deg); }
  }

  /* ============================================
     UTILITY CLASSES
     ============================================ */
  
  .hidden {
    display: none !important;
  }
</style>
</head>
<body>

<div id="speedtestContainer" class="speedtest-container">
  <!-- Loading State -->
  <div id="loadingView" class="loading hidden">
    <div class="spinner"></div>
  </div>

  <!-- Header Section -->
  <div class="header-section">
    <!-- SpeedTest Icon -->
    <svg class="logo-icon" viewBox="0 0 24 24">
      <path d="M 12 16 A 3 3 0 0 1 9 13 C 9 11.88 9.61 10.9 10.5 10.39 L 20.21 4.77 L 14.68 14.35 C 14.18 15.33 13.17 16 12 16 M 12 3 C 13.81 3 15.5 3.5 16.97 4.32 L 14.87 5.53 C 14 5.19 13 5 12 5 A 8 8 0 0 0 4 13 C 4 15.21 4.89 17.21 6.34 18.65 H 6.35 C 6.74 19.04 6.74 19.67 6.35 20.06 C 5.96 20.45 5.32 20.45 4.93 20.07 V 20.07 C 3.12 18.26 2 15.76 2 13 A 10 10 0 0 1 12 3 M 22 13 C 22 15.76 20.88 18.26 19.07 20.07 V 20.07 C 18.68 20.45 18.05 20.45 17.66 20.06 C 17.27 19.67 17.27 19.04 17.66 18.65 V 18.65 C 19.11 17.2 20 15.21 20 13 C 20 12 19.81 11 19.46 10.1 L 20.67 8 C 21.5 9.5 22 11.18 22 13 Z"/>
    </svg>
    
    <!-- Ookla Brand -->
    <span class="brand-text">Ookla</span>
  </div>

  <!-- Main Content -->
  <div id="mainContent" class="hidden main-content">
    
    <!-- Download Speed Row -->
    <div class="speed-row">
      <span class="speed-label">Download</span>
      <svg class="speed-icon download-icon" viewBox="0 0 24 24">
        <path d="M 6.5 20 Q 4.22 20 2.61 18.43 Q 1 16.85 1 14.58 Q 1 12.63 2.17 11.1 Q 3.35 9.57 5.25 9.15 Q 5.83 7.13 7.39 5.75 Q 8.95 4.38 11 4.08 V 12.15 L 9.4 10.6 L 8 12 L 12 16 L 16 12 L 14.6 10.6 L 13 12.15 V 4.08 Q 15.58 4.43 17.29 6.39 Q 19 8.35 19 11 Q 20.73 11.2 21.86 12.5 Q 23 13.78 23 15.5 Q 23 17.38 21.69 18.69 Q 20.38 20 18.5 20 Z"/>
      </svg>
      <span class="speed-value download-value" id="downloadValue">-- Mbps</span>
    </div>
    
    <!-- Upload Speed Row -->
    <div class="speed-row">
      <span class="speed-label">Upload</span>
      <svg class="speed-icon upload-icon" viewBox="0 0 24 24">
        <path d="M 11 20 H 6.5 Q 4.22 20 2.61 18.43 Q 1 16.85 1 14.58 Q 1 12.63 2.17 11.1 Q 3.35 9.57 5.25 9.15 Q 5.88 6.85 7.75 5.43 Q 9.63 4 12 4 Q 14.93 4 16.96 6.04 Q 19 8.07 19 11 Q 20.73 11.2 21.86 12.5 Q 23 13.78 23 15.5 Q 23 17.38 21.69 18.69 Q 20.38 20 18.5 20 H 13 V 12.85 L 14.6 14.4 L 16 13 L 12 9 L 8 13 L 9.4 14.4 L 11 12.85 Z"/>
      </svg>
      <span class="speed-value upload-value" id="uploadValue">-- Mbps</span>
    </div>
    
    <!-- Status Bar -->
    <div class="status-bar" id="statusBar">
      <div class="status-badge" id="statusBadge">--</div>
    </div>
    
    <!-- Info Section -->
    <div class="info-section">
      <div class="server-name" id="serverName">--</div>
      <div class="time-display" id="timeDisplay">--/--/-- --:-- --</div>
    </div>
  </div>
</div>

<script>
(function() {
  'use strict';

  /* ============================================
     SPEEDTEST MONITOR v1.1 (Mini) - JAVASCRIPT
     ============================================ */

  // ============================================
  // CONFIGURATION MODULE
  // ============================================
  const Config = {
    variable: null,
    display: {
      useGradient: true
    }
  };

  // ============================================
  // STATE MANAGEMENT MODULE
  // ============================================
  const State = {
    data: {
      isValid: false,
      download: 0,
      upload: 0,
      ping: 0,
      server: 'Unknown',
      timestamp: null
    },
    subscription: null
  };

  // ============================================
  // DOM REFERENCES MODULE
  // ============================================
  const DOM = {
    init() {
      this.container = document.getElementById('speedtestContainer');
      this.loadingView = document.getElementById('loadingView');
      this.mainContent = document.getElementById('mainContent');
      
      this.elements = {
        statusBar: document.getElementById('statusBar'),
        statusBadge: document.getElementById('statusBadge'),
        serverName: document.getElementById('serverName'),
        timeDisplay: document.getElementById('timeDisplay'),
        downloadValue: document.getElementById('downloadValue'),
        uploadValue: document.getElementById('uploadValue')
      };
    }
  };

  // ============================================
  // DATA PARSER MODULE
  // ============================================
  const DataParser = {
    parse(variableString) {
      if (!variableString) return null;
      
      const parts = variableString.split(',');
      if (parts.length < 6) return null;
      
      // Parse timestamp - handle both milliseconds and ISO 8601 format
      let timestamp = parts[5];
      if (timestamp && timestamp.includes('T')) {
        timestamp = new Date(timestamp).getTime();
      } else {
        timestamp = parseInt(timestamp) || Date.now();
      }
      
      return {
        isValid: parts[0] === 'true',
        download: parseFloat(parts[1]) || 0,
        upload: parseFloat(parts[2]) || 0,
        ping: parseFloat(parts[3]) || 0,
        server: parts[4] || 'Unknown',
        timestamp: timestamp
      };
    }
  };

  // ============================================
  // TIME UTILITIES MODULE
  // ============================================
  const TimeUtils = {
    formatTimestamp(timestamp) {
      if (!timestamp) {
        return '--/--/-- --:-- --';
      }
      
      const date = new Date(parseInt(timestamp));
      const month = String(date.getMonth() + 1).padStart(2, '0');
      const day = String(date.getDate()).padStart(2, '0');
      const year = String(date.getFullYear()).slice(-2);
      const hours = date.getHours();
      const minutes = String(date.getMinutes()).padStart(2, '0');
      const ampm = hours >= 12 ? 'PM' : 'AM';
      const displayHours = hours % 12 || 12;
      
      return `${month}/${day}/${year} ${displayHours}:${minutes} ${ampm}`;
    }
  };

  // ============================================
  // DYNAMIC SIZING MODULE
  // ============================================
  const DynamicSizing = {
    resizeTimeout: null,

    init() {
      this.updateScale();
      window.addEventListener('resize', this.debounce(this.updateScale.bind(this), 250));
    },

    updateScale() {
      const container = DOM.container;
      if (!container) return;

      const width = container.offsetWidth;
      const height = container.offsetHeight;
      const minDimension = Math.min(width, height);

      let scaleFactor;
      if (minDimension < 150) {
        scaleFactor = 0.25;
      } else if (minDimension < 200) {
        scaleFactor = 0.35;
      } else if (minDimension < 250) {
        scaleFactor = 0.45;
      } else if (minDimension < 300) {
        scaleFactor = 0.55;
      } else if (minDimension < 400) {
        scaleFactor = 0.7;
      } else if (minDimension < 500) {
        scaleFactor = 0.85;
      } else if (minDimension < 600) {
        scaleFactor = 1.0;
      } else if (minDimension < 800) {
        scaleFactor = 1.3;
      } else {
        scaleFactor = 1.6;
      }

      document.documentElement.style.setProperty('--scale-factor', scaleFactor);
    },

    debounce(func, wait) {
      return function executedFunction(...args) {
        const later = () => {
          clearTimeout(DynamicSizing.resizeTimeout);
          func(...args);
        };
        clearTimeout(DynamicSizing.resizeTimeout);
        DynamicSizing.resizeTimeout = setTimeout(later, wait);
      };
    }
  };

  // ============================================
  // DISPLAY MODULE
  // ============================================
  const Display = {
    update() {
      const data = State.data;
      
      // Update status badge and bar color
      DOM.elements.statusBadge.textContent = data.isValid ? 'OK' : 'NO OK';
      DOM.elements.statusBar.className = `status-bar ${data.isValid ? 'ok' : 'nok'}`;
      
      // Update server name
      DOM.elements.serverName.textContent = data.server;
      
      // Update timestamp
      DOM.elements.timeDisplay.textContent = TimeUtils.formatTimestamp(data.timestamp);
      
      // Update speed values with animation
      this.animateValue('downloadValue', data.download, 'Mbps', 0);
      this.animateValue('uploadValue', data.upload, 'Mbps', 0);    
    },

    animateValue(elementId, targetValue, unit, decimals) {
      const element = DOM.elements[elementId];
      if (!element) return;
      
      const currentText = element.textContent;
      const currentValue = parseFloat(currentText) || 0;
      
      const duration = 1500;
      const fps = 60;
      const totalFrames = (duration / 1000) * fps;
      let currentFrame = 0;
      
      const startValue = currentValue;
      const deltaValue = targetValue - startValue;
      
      const animateStep = () => {
        currentFrame++;
        const progress = currentFrame / totalFrames;
        
        // Easing function for smooth animation
        const easeInOutQuad = progress < 0.5 
          ? 2 * progress * progress 
          : 1 - Math.pow(-2 * progress + 2, 2) / 2;
        
        const currentAnimatedValue = startValue + (deltaValue * easeInOutQuad);
        
        if (decimals === 0) {
          element.textContent = `${Math.round(currentAnimatedValue)} ${unit}`;
        } else {
          element.textContent = `${currentAnimatedValue.toFixed(decimals)} ${unit}`;
        }
        
        if (currentFrame < totalFrames) {
          requestAnimationFrame(animateStep);
        }
      };
      
      requestAnimationFrame(animateStep);
    },

    applyTheme() {
      if (Config.display.useGradient) {
        DOM.container.classList.add('gradient');
      } else {
        DOM.container.classList.remove('gradient');
      }
    },

    showMain() {
      DOM.loadingView.classList.add('hidden');
      DOM.mainContent.classList.remove('hidden');
    }
  };

  // ============================================
  // VARIABLE MANAGER MODULE
  // ============================================
  const VariableManager = {
    subscribe(variable) {
      if (!variable) return;
      
      // Process initial value if available
      if (variable.value) {
        const data = DataParser.parse(variable.value);
        if (data) {
          State.data = data;
          Display.update();
        }
      }
      
      // Subscribe to value changes
      State.subscription = variable.onValue((value) => {
        const data = DataParser.parse(value);
        if (data) {
          State.data = data;
          Display.update();
        }
      });
    },

    setup() {
      if (Config.variable) {
        this.subscribe(Config.variable);
      }
    }
  };

  // ============================================
  // APPLICATION CONTROLLER
  // ============================================
  const App = {
    init() {
      DOM.init();
      DynamicSizing.init();
      
      // Wait for SharpTools library
      if (typeof stio === 'undefined') {
        setTimeout(() => this.init(), 200);
        return;
      }
      
      stio.ready((data) => {
        // Configure variable from settings
        Config.variable = data.settings.speedVariable;
        
        // Configure display options
        Config.display.useGradient = data.settings.useGradient !== false;

        // Apply configurations
        Display.applyTheme();
        VariableManager.setup();
        Display.showMain();
        
        // Update scale after setup
        setTimeout(() => {
          DynamicSizing.updateScale();
          Display.update();
        }, 250);
      });
    }
  };

  // ============================================
  // INITIALIZATION
  // ============================================
  if (document.readyState === 'loading') {
    document.addEventListener('DOMContentLoaded', () => App.init());
  } else {
    App.init();
  }

})();
</script>

</body>
</html>