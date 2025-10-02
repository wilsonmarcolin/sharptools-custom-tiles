<!--
╔════════════════════════════════════════════════════════════════════════════╗
║                        UPS INFORMATION v2.2                                ║
║                Professional UPS Monitor for SharpTools                     ║
╠════════════════════════════════════════════════════════════════════════════╣
║                                                                            ║
║  VERSION 2.2 CHANGELOG:                                                    ║
║  • OPTIMIZATION: Removed code duplication and consolidated functions       ║
║  • REFACTORING: Unified subscription handlers                              ║
║  • IMPROVEMENT: Simplified CSS variable system                             ║
║  • CLEANUP: Consolidated animation and time utilities                      ║
║  • PERFORMANCE: Reduced file size by ~30%                                  ║
║  • ADDED: NUT branding header with logo                                    ║
║  • FIXED: Corrected NUT logo representation                               ║
║                                                                            ║
║  KEY FEATURES:                                                             ║
║  • Real-time voltage monitoring with animated gauges                       ║
║  • Battery status visualization with 10-segment display                    ║
║  • Power source indicator (Utility/Battery)                                ║
║  • UPS status with NUT protocol codes                                      ║
║  • Power failure tracking and duration monitoring                          ║
║  • Dynamic visual alerts and gradient backgrounds                          ║
║  • Fully responsive with automatic scaling                                 ║
║  • NUT (Network UPS Tools) branding                                        ║
║                                                                            ║
║  REQUIRED INPUTS:                                                          ║
║  • Input/Output Voltage Devices (voltageMeasurement)                       ║
║  • Battery Charge Device (battery capability)                              ║
║  • UPS Status Data Device                                                  ║
║  • Failure timestamp variables (requires external automation)*             ║
║                                                                            ║
║  * Variables must be updated by external automation                        ║
║                                                                            ║
║  AUTHOR: Wilson Marcolin                                                   ║
║  CONTRIBUTORS: Claude AI Assistant                                         ║
║  VERSION: 2.1.2                                                            ║
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
  <title>UPS Information v2.2</title>

  <!-- Tile Settings Schema -->
  <script type="application/json" id="tile-settings">
{
  "schema": "0.2.0",
  "settings": [
    {
      "type": "THING",
      "name": "inputVoltage",
      "label": "Input Voltage Device",
      "filters": {"capabilities": ["voltageMeasurement"]}
    },
    {
      "type": "THING",
      "name": "outputVoltage",
      "label": "Output Voltage Device",
      "filters": {"capabilities": ["voltageMeasurement"]}
    },
    {
      "type": "THING",
      "name": "batteryCharge",
      "label": "Battery Charge Device",
      "filters": {"capabilities": ["battery"]}
    },
    {
      "type": "THING",
      "name": "upsStatusData",
      "label": "UPS Status Data Device"
    },
    {
      "type": "VARIABLE",
      "name": "upsFailure",
      "label": "Last Failure Variable",
      "filters": {"type": "Number"}
    },
    {
      "type": "VARIABLE",
      "name": "upsBack",
      "label": "Power Back Variable",
      "filters": {"type": "Number"}
    },
    {
      "type": "BOOLEAN",
      "name": "useGradient",
      "label": "Use Gradient Background",
      "default": true
    }
  ],
  "name": "UPS Information v.2.1"
}
</script>

  <!-- SharpTools Libraries -->
  <script src="https://cdn.sharptools.io/js/custom-tiles/0.2.1/stio.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/gauge.js/1.3.9/gauge.min.js"></script>

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
    
    /* Colors - Light mode */
    --primary: #2196F3;
    --success: #4CAF50;
    --warning: #FFA726;
    --danger: #EF5350;
    --text: #212121;
    --text-dim: #757575;
    --bg: #FAFAFA;
    --surface: #FFFFFF;
    --border: #E0E0E0;
    
    /* Battery colors */
    --bat-critical: #F44336;
    --bat-low: #FF9800;
    --bat-medium: #FFC107;
    --bat-good: #8BC34A;
    
    /* Gradients */
    --grad-start: #1976D2;
    --grad-mid: #2196F3;
    --grad-end: #64B5F6;
    --grad-danger-start: #B71C1C;
    --grad-danger-mid: #EF5350;
    --grad-danger-end: #E57373;
    
    /* Typography using base */
    --font-xs: calc(var(--base) * 0.625);
    --font-sm: calc(var(--base) * 0.75);
    --font-base: calc(var(--base) * 0.875);
    --font-lg: calc(var(--base) * 1);
    --font-xl: calc(var(--base) * 1.25);
    
    /* Spacing using base */
    --gap-xs: calc(var(--base) * 0.125);
    --gap-sm: calc(var(--base) * 0.25);
    --gap-md: calc(var(--base) * 0.5);
    --gap-lg: calc(var(--base) * 0.75);
    
    /* Components */
    --gauge-size: calc(120px * var(--scale));
    
    /* Effects */
    --shadow: 0 1px 3px rgba(0,0,0,0.12);
    --radius: 6px;
    --transition: 300ms ease;
  }

  /* Dark mode */
  @media (prefers-color-scheme: dark) {
    :root {
      --text: #FFFFFF;
      --text-dim: #B0B0B0;
      --bg: #121212;
      --surface: #1E1E1E;
      --border: #333333;
    }
  }

  /* ============================================
     GLOBAL STYLES
     ============================================ */
  html, body {
    height: 100%;
    overflow: hidden;
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
    font-weight: 400;
    line-height: 1.4;
    -webkit-font-smoothing: antialiased;
    background: var(--bg);
    color: var(--text);
  }

  /* ============================================
     CONTAINER & LAYOUT
     ============================================ */
  .container {
    width: 100%;
    height: 100%;
    padding: 1px var(--gap-md) var(--gap-sm);  /* Padrão para mobile */
    display: flex;
    flex-direction: column;
    transition: background var(--transition);
    position: relative;
  }

  @media (min-width: 768px) {
    .container {
      padding: 18px var(--gap-md) var(--gap-sm);  /* Volta ao padding original para desktop */
    }
  }

  @media (min-width: 1025px) {
    .container {
      padding: 18px var(--gap-md) var(--gap-sm);  /* Mais espaço no topo */
    }
  }
    
  .container.gradient {
    background: linear-gradient(135deg, var(--grad-start) 0%, var(--grad-mid) 50%, var(--grad-end) 100%);
  }

  .container.gradient.failure {
    background: linear-gradient(135deg, var(--grad-danger-start) 0%, var(--grad-danger-mid) 50%, var(--grad-danger-end) 100%);
  }

  .container.gradient * {
    color: white !important;
  }

  .container.gradient .gauge {
    filter: brightness(1.2);
  }

  /* ============================================
     HEADER SECTION
     ============================================ */
  .header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: calc(var(--gap-sm) * 0.5) var(--gap-md) var(--gap-sm);
    margin-bottom: var(--gap-xs);
    flex-shrink: 0;
    gap: var(--gap-md);
  }

  .logo-group {
    display: flex;
    align-items: center;
    margin-left: 10px;
    margin-right: 10px;
}

  .logo-icon {
    width: calc(20px * var(--scale));
    height: calc(20px * var(--scale));
    opacity: 0.95;
    display: block;
    object-fit: contain;
  }

  /* Ajusta cores quando gradient está ativo */
  .container.gradient .logo-icon {
    filter: brightness(1.1) contrast(1.1);
  }
    
  .logo-text {
    font-size: calc(var(--base) * 0.8);
    font-weight: bold;
    opacity: 0.9;
    letter-spacing: 0.5px;
    margin-left: 5px;
    margin-right: 5px;
  }

  .container.gradient .logo-text {
    color: white !important;
  }
    
  .grid {
    flex: 1;
    display: flex;
    flex-direction: column;
    gap: 4px;
    justify-content: flex-start;
    padding-top: 0;
  }

  @media (min-width: 768px) {
    .grid {
      justify-content: center;
      padding-top: 60;
    }
  }

  @media (min-width: 1025px) {
    .grid {
      justify-content: center;
      padding-top: 120px;
    }
  }
    
  .row {
    display: flex;
    gap: 4px;
    align-items: stretch;
    justify-content: center;
    flex-shrink: 0;
  }
  
  /* ============================================
     CARD COMPONENT
     ============================================ */
  .card {
    background: var(--surface);
    border-radius: var(--radius);
    padding: var(--gap-md);
    box-shadow: var(--shadow);
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    flex: 1;
    position: relative;
    min-width: 0;
  }

  .container.gradient .card {
    background: rgba(255, 255, 255, 0.1);
    backdrop-filter: blur(10px);
  }

  .card-label {
    position: absolute;
    top: var(--gap-sm);
    left: 0;
    right: 0;
    text-align: center;
    font-size: var(--font-xs);
    opacity: 0.7;
    text-transform: uppercase;
    letter-spacing: 0.5px;
  }

  /* ============================================
     GAUGE COMPONENT
     ============================================ */
  .gauge-wrap {
    width: var(--gauge-size);
    height: calc(var(--gauge-size) * 0.7);
    position: relative;
    margin-top: var(--gap-md);
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
  }

  .gauge {
    width: 100% !important;
    height: auto !important;
  }

  .gauge-value {
    font-size: var(--font-lg);
    font-weight: 600;
    text-align: center;
    margin-top: 0;
  }

  /* ============================================
     BATTERY DISPLAY
     ============================================ */
  .battery {
    width: 100%;
    max-width: calc(200px * var(--scale));
    margin-top: calc(var(--gap-lg) * 1.5);
  }

  .battery-bar {
    display: flex;
    gap: 1px;
    height: calc(20px * var(--scale));
    background: var(--border);
    border-radius: 3px;
    padding: 2px;
    margin-bottom: var(--gap-sm);
  }

  .battery-seg {
    flex: 1;
    background: rgba(128, 128, 128, 0.2);
    border-radius: 1px;
    transition: background var(--transition);
  }

  .battery-seg.on {
    background: var(--bat-good);
  }

  .battery-seg.on.critical { background: var(--bat-critical); }
  .battery-seg.on.low { background: var(--bat-low); }
  .battery-seg.on.medium { background: var(--bat-medium); }

  .battery-val {
    text-align: center;
    font-size: var(--font-lg);
    font-weight: 600;
  }

  /* ============================================
     POWER BADGE
     ============================================ */
  .badge {
    margin-top: calc(var(--gap-lg) * 1.5);
    padding: calc(var(--gap-sm) * 1.5) calc(var(--gap-lg) * 1.5);
    border-radius: 16px;
    font-weight: 700;
    text-transform: uppercase;
    letter-spacing: 1px;
    font-size: var(--font-xl);
  }

  .badge.utility {
    background: var(--success);
    color: white;
  }

  .badge.battery {
    background: var(--warning);
    color: white;
  }

  /* ============================================
     STATUS SECTION
     ============================================ */
  .status {
    background: var(--surface);
    border-radius: var(--radius);
    padding: var(--gap-md);
    box-shadow: var(--shadow);
  }

  .container.gradient .status {
    background: rgba(255, 255, 255, 0.15);
  }

  .status-main {
    text-align: center;
    padding-bottom: var(--gap-md);
    border-bottom: 1px solid var(--border);
  }

  .status-label {
    font-size: var(--font-xs);
    opacity: 0.7;
    text-transform: uppercase;
    margin-bottom: var(--gap-xs);
  }

  .status-value {
    font-size: calc(0.85rem * var(--scale));
    font-weight: 600;
    color: var(--primary);
  }

  .container.gradient .status-value {
    color: white !important;
  }

  /* ============================================
     INFO GRID
     ============================================ */
  .info-grid {
    display: grid;
    grid-template-columns: 30% 40% 30%;
    gap: var(--gap-md);
    padding-top: var(--gap-md);
  }

  .info-item {
    text-align: center;
  }

  .info-label {
    font-size: var(--font-xs);
    opacity: 0.7;
    text-transform: uppercase;
    margin-bottom: var(--gap-xs);
  }

  .info-value {
    font-size: var(--font-base);
    font-weight: 600;
  }

  /* ============================================
     STATES
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
    border: 3px solid var(--border);
    border-top-color: var(--primary);
    border-radius: 50%;
    animation: spin 1s linear infinite;
  }

  @keyframes spin {
    to { transform: rotate(360deg); }
  }

  .hidden { display: none !important; }
  </style>
</head>
<body>
  <div id="container" class="container">
    <!-- Loading -->
    <div id="loading" class="loading hidden">
      <div class="spinner"></div>
    </div>

    <!-- Main Content -->
    <div id="main" class="hidden">
      
      <!-- Header with NUT Branding -->
      <div class="header">
        <!-- NUT Logo - Tente uma destas opções: -->
        
        <!-- OPÇÃO 1: Logo PNG oficial (recomendado se tiver internet) -->
        <div class="logo-group">
          <img class="logo-icon" 
               src="https://cdn.jsdelivr.net/gh/homarr-labs/dashboard-icons/png/nut.png" 
               alt="NUT" 
               onerror="this.style.display='none'">
        </div>
        
        <!-- OPÇÃO 2: SVG ultra-básico (descomente se preferir offline)
        <div class="logo-group">
          <svg class="logo-icon" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24">
            <path fill="none" stroke="currentColor" stroke-width="2" d="M12 2c-3 0-6 3-6 7s1 9 6 12c5-3 6-8 6-12s-3-7-6-7zm0 3v14m-3-11l6 8-6-3 6-5z"/>
          </svg>
        </div>
        -->
        
        <!-- NUT Text Branding -->
        <span class="logo-text">NUT</span>
      </div>
      
      <!-- Main Grid Content -->
      <div class="grid">
        
        <!-- Voltage Row -->
        <div class="row">
          <!-- Input Voltage -->
          <div class="card">
            <div class="card-label">Input Voltage</div>
            <div class="gauge-wrap">
              <canvas id="inputGauge" class="gauge"></canvas>
              <div class="gauge-value" id="inputVal">0 V</div>
            </div>
          </div>

          <!-- Output Voltage -->
          <div class="card">
            <div class="card-label">Output Voltage</div>
            <div class="gauge-wrap">
              <canvas id="outputGauge" class="gauge"></canvas>
              <div class="gauge-value" id="outputVal">0 V</div>
            </div>
          </div>
        </div>

        <!-- Power & Battery Row -->
        <div class="row">
          <!-- Power Source -->
          <div class="card">
            <div class="card-label">Power Source</div>
            <div id="power" class="badge utility">UTILITY</div>
          </div>

          <!-- Battery Level -->
          <div class="card">
            <div class="card-label">Battery Level</div>
            <div class="battery">
              <div class="battery-bar">
                <div class="battery-seg"></div>
                <div class="battery-seg"></div>
                <div class="battery-seg"></div>
                <div class="battery-seg"></div>
                <div class="battery-seg"></div>
                <div class="battery-seg"></div>
                <div class="battery-seg"></div>
                <div class="battery-seg"></div>
                <div class="battery-seg"></div>
                <div class="battery-seg"></div>
              </div>
              <div class="battery-val" id="batteryVal">100%</div>
            </div>
          </div>
        </div>

        <!-- Status Section -->
        <div class="status">
          <!-- UPS Status -->
          <div class="status-main">
            <div class="status-label">Status</div>
            <div class="status-value" id="statusText">Initializing...</div>
          </div>
          
          <!-- Time Info -->
          <div class="info-grid">
            <div class="info-item">
              <div class="info-label">Uptime Since</div>
              <div class="info-value" id="uptime">--</div>
            </div>
            <div class="info-item">
              <div class="info-label">Last Failure</div>
              <div class="info-value" id="lastFail">--</div>
            </div>
            <div class="info-item">
              <div class="info-label">Duration</div>
              <div class="info-value" id="duration">--</div>
            </div>
          </div>
        </div>
      </div> <!-- Fecha grid -->
    </div> <!-- Fecha main -->
  </div> <!-- Fecha container -->

  <script>
  (function() {
    'use strict';

    /* ============================================
       UPS INFORMATION v2.2 - Optimized
       ============================================ */

    // Configuration
    const Config = {
      devices: {},
      variables: {},
      display: { gradient: true }
    };

    // State
    const State = {
      data: {
        inputV: 0,
        outputV: 0,
        battery: 100,
        status: 'OL',
        lastFail: null,
        powerBack: null
      },
      subs: [],
      gauges: {}
    };

    // NUT Status Codes
    const StatusCodes = {
      'OL': 'UPS is using utility power and supplying the load.',
      'OB': 'Utility power failed, UPS is running on battery.',
      'LB': 'Battery is nearly depleted, shutdown imminent.',
      'RB': 'Battery needs replacement.',
      'CHRG': 'Battery is recharging from utility power.',
      'DISCHRG': 'Battery is being used to power the load.',
      'BYPASS': 'Load connected directly to utility.',
      'CAL': 'UPS is performing calibration.',
      'OFF': 'UPS is on but not powering the load.',
      'OVER': 'Load exceeds UPS capacity.',
      'TRIM': 'UPS reducing input voltage.',
      'BOOST': 'UPS boosting input voltage.',
      'FSD': 'Shutdown command issued.',
      'ALARM': 'UPS signaling a fault.',
      'COMMOK': 'Communication is active.',
      'COMMBAD': 'Communication has failed.'
    };
    
    // DOM References
    const DOM = {};

    function initDOM() {
      const ids = ['container', 'loading', 'main', 'inputVal', 'outputVal',
                   'power', 'batteryVal', 'statusText', 'lastFail', 'duration', 'uptime'];
      
      ids.forEach(id => {
        DOM[id] = document.getElementById(id);
      });
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
        
        const scale = size < 200 ? 0.5 :
                     size < 300 ? 0.7 :
                     size < 400 ? 0.9 :
                     size < 600 ? 1.2 :
                     size < 800 ? 1.5 :
                     2;

        document.documentElement.style.setProperty('--scale', scale);
      },

      debounce(fn, ms) {
        let timer;
        return (...args) => {
          clearTimeout(timer);
          timer = setTimeout(() => fn(...args), ms);
        };
      }
    };

    // Gauge Manager - unified animation
    const Gauges = {
      create(canvas) {
        if (!canvas || typeof Gauge === 'undefined') return null;

        const opts = {
          angle: 0,
          lineWidth: 0.40,
          radiusScale: 1,
          pointer: {
            length: 0.6,
            strokeWidth: 0.035,
            color: '#FFFFFF'
          },
          limitMax: true,
          limitMin: true,
          colorStart: '#2196F3',
          colorStop: '#2196F3',
          strokeColor: 'rgba(255, 255, 255, 0.1)',
          generateGradient: false,
          highDpiSupport: true,
          renderTicks: false
        };

        const gauge = new Gauge(canvas).setOptions(opts);
        gauge.minValue = 90;
        gauge.maxValue = 130;
        gauge.animationSpeed = 1;
        gauge.set(110);

        return gauge;
      },

      init() {
        if (typeof Gauge === 'undefined') {
          setTimeout(() => this.init(), 100);
          return;
        }

        const input = document.getElementById('inputGauge');
        const output = document.getElementById('outputGauge');

        if (input) State.gauges.input = this.create(input);
        if (output) State.gauges.output = this.create(output);
      },

      animate(type, target) {
        const gauge = State.gauges[type];
        const element = type === 'input' ? DOM.inputVal : DOM.outputVal;
        
        if (!gauge || !element) return;
        
        const start = gauge.displayedValue || 0;
        const delta = target - start;
        const duration = 3000;
        const frames = 180;
        let frame = 0;
        
        const step = () => {
          frame++;
          const progress = frame / frames;
          
          // Easing function
          const eased = progress < 0.5 
            ? 2 * progress * progress 
            : 1 - Math.pow(-2 * progress + 2, 2) / 2;
          
          const value = start + (delta * eased);
          gauge.set(value);

          let display = Math.round(value);
          if (type === 'input' && display < 10) display = 0;
          element.textContent = `${display} V`;
          
          if (frame < frames) {
            requestAnimationFrame(step);
          }
        };
        
        requestAnimationFrame(step);
      }
    };

    // Battery Display
    const Battery = {
      update(percent) {
        const segments = document.querySelectorAll('.battery-seg');
        const active = Math.ceil((percent / 100) * 10);
        
        segments.forEach((seg, i) => {
          seg.className = 'battery-seg';
          
          if (i < active) {
            seg.classList.add('on');
            
            if (percent <= 20) seg.classList.add('critical');
            else if (percent <= 50) seg.classList.add('low');
            else if (percent <= 80) seg.classList.add('medium');
          }
        });
        
        DOM.batteryVal.textContent = `${Math.round(percent)}%`;
      }
    };

    // Time Utilities - consolidated
    const Time = {
      format(timestamp, type) {
        if (!timestamp) return '--';
        
        const date = new Date(parseInt(timestamp));
        
        if (type === 'date') {
          const m = String(date.getMonth() + 1).padStart(2, '0');
          const d = String(date.getDate()).padStart(2, '0');
          const y = String(date.getFullYear()).slice(-2);
          const h = date.getHours();
          const min = String(date.getMinutes()).padStart(2, '0');
          const ampm = h >= 12 ? 'PM' : 'AM';
          const h12 = h % 12 || 12;
          
          return `${m}/${d}/${y} ${h12}:${min} ${ampm}`;
        }
        
        if (type === 'uptime') {
          const diff = Date.now() - parseInt(timestamp);
          const days = Math.floor(diff / 86400000);
          const hours = Math.floor((diff % 86400000) / 3600000);
          const mins = Math.floor((diff % 3600000) / 60000);
          
          return `${days} DAYS ${String(hours).padStart(2, '0')}:${String(mins).padStart(2, '0')}`;
        }
        
        return '--';
      },

      duration(start, end) {
        if (!start || !end) return '--';
        
        const diff = Math.abs(end - start);
        const h = Math.floor(diff / 3600000);
        const m = Math.floor((diff % 3600000) / 60000);
        
        return `${h}h ${m}m`;
      }
    };

    // Display Updates
    const Display = {
      update() {
        // Update gauges
        Gauges.animate('input', State.data.inputV);
        Gauges.animate('output', State.data.outputV);
        
        // Power source
        const onUtility = State.data.inputV > 90;
        DOM.power.textContent = onUtility ? 'UTILITY' : 'BATTERY';
        DOM.power.className = `badge ${onUtility ? 'utility' : 'battery'}`;

        // Gradient
        if (Config.display.gradient) {
          DOM.container.classList.toggle('failure', !onUtility);
        }
        
        // Battery
        Battery.update(State.data.battery);
        
        // Smart status detection
        let status = State.data.status;
        if (State.data.inputV > 90) {
          status = State.data.battery >= 99 ? 'OL' : 'CHRG';
        } else {
          status = State.data.battery <= 20 ? 'LB' : 'OB';
        }
        
        if (status !== State.data.status) {
          State.data.status = status;
        }

        DOM.statusText.textContent = StatusCodes[status] || status;
    
        // Time info
        DOM.uptime.textContent = Time.format(State.data.powerBack, 'uptime');
        DOM.lastFail.textContent = Time.format(State.data.lastFail, 'date');

        // Duration
        if (State.data.status === 'OB' && State.data.lastFail) {
          DOM.duration.textContent = Time.duration(State.data.lastFail, Date.now()) + ' (ongoing)';
        } else {
          DOM.duration.textContent = Time.duration(State.data.lastFail, State.data.powerBack);
        }
      },

      theme() {
        DOM.container.classList.toggle('gradient', Config.display.gradient);
      },

      show() {
        DOM.loading.classList.add('hidden');
        DOM.main.classList.remove('hidden');
      }
    };

    // Device Manager - unified subscription
    const Devices = {
      subscribe(source, attr, key, isVar) {
        if (!source) return;
        
        const callback = (v) => {
          if (v != null) {
            State.data[key] = key.includes('V') ? parseFloat(v) || 0 : v;
            Display.update();
          }
        };
        
        if (isVar) {
          // Variable subscription
          if (source.value != null) callback(source.value);
          const sub = source.onValue(callback);
          if (sub) State.subs.push(sub);
        } else {
          // Device subscription
          const attribute = source.attributes?.[attr];
          if (!attribute) return;
          
          if (attribute.value != null) callback(attribute.value);
          const sub = attribute.onValue(callback);
          if (sub) State.subs.push(sub);
        }
      },

      setup() {
        // Voltage subscriptions
        this.subscribe(Config.devices.inputVoltage, 'voltage', 'inputV', false);
        this.subscribe(Config.devices.outputVoltage, 'voltage', 'outputV', false);
        
        // Battery subscription
        this.subscribe(Config.devices.batteryCharge, 'battery', 'battery', false);
        
        // Status subscription
        this.subscribe(Config.devices.upsStatusData, 'status', 'status', false);
        
        // Variable subscriptions
        this.subscribe(Config.variables.upsFailure, null, 'lastFail', true);
        this.subscribe(Config.variables.upsBack, null, 'powerBack', true);
        
        // Periodic status refresh
        if (Config.devices.upsStatusData?.attributes?.status) {
          setInterval(() => {
            Config.devices.upsStatusData.attributes.status.refresh();
          }, 10000);
        }
      }
    };

    // Application Controller
    const App = {
      init() {
        initDOM();
        Sizing.init();
        
        if (typeof stio === 'undefined') {
          setTimeout(() => this.init(), 200);
          return;
        }
        
        stio.ready((data) => {
          // Configure
          Config.devices = {
            inputVoltage: data.settings.inputVoltage,
            outputVoltage: data.settings.outputVoltage,
            batteryCharge: data.settings.batteryCharge,
            upsStatusData: data.settings.upsStatusData
          };
          
          Config.variables = {
            upsFailure: data.settings.upsFailure,
            upsBack: data.settings.upsBack
          };
          
          Config.display.gradient = data.settings.useGradient !== false;

          // Setup
          Display.theme();
          Devices.setup();
          Display.show();
          
          // Initialize gauges
          setTimeout(() => {
            Gauges.init();
            Display.update();
          }, 250);

          // Update timer
          setInterval(() => {
            if (State.data.powerBack) {
              DOM.uptime.textContent = Time.format(State.data.powerBack, 'uptime');
            }
          }, 60000);
        });
      }
    };

    // Initialize
    if (document.readyState === 'loading') {
      document.addEventListener('DOMContentLoaded', () => App.init());
    } else {
      App.init();
    }

  })();
  </script>
</body>
</html>