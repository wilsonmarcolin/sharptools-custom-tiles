<!--
╔════════════════════════════════════════════════════════════════════════════╗
║                      UPS INFORMATION v2.2 (Mini)                           ║
║              Professional UPS Monitor for SharpTools - Compact Edition     ║
╠════════════════════════════════════════════════════════════════════════════╣
║                                                                            ║
║  VERSION 2.2 CHANGELOG:                                                   ║
║  • OPTIMIZATION: Removed code duplication and consolidated functions       ║
║  • REFACTORING: Unified gauge animation system                            ║
║  • IMPROVEMENT: Simplified CSS structure                                  ║
║  • CLEANUP: Removed redundant variables and comments                      ║
║  • PERFORMANCE: Reduced file size by ~35%                                 ║
║                                                                            ║
║  FEATURES:                                                                 ║
║  • Dual voltage gauges (Input/Output)                                      ║
║  • Real-time UPS status display                                           ║
║  • Visual alerts during power failures                                    ║
║  • Color-coded status indicators                                          ║
║  • Automatic scaling for all screen sizes                                 ║
║                                                                            ║
║  REQUIRED INPUTS:                                                          ║
║  • Input Voltage Device (voltageMeasurement)                               ║
║  • Output Voltage Device (voltageMeasurement)                              ║
║  • UPS Status Data Device                                                  ║
║                                                                            ║
║  AUTHOR: Wilson Marcolin                                                   ║
║  CONTRIBUTORS: Claude AI Assistant                                         ║
║  VERSION: 2.2.0                                                            ║
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
<title>UPS Information v2.2 (Mini)</title>

<!-- Tile Settings Schema -->
<script type="application/json" id="tile-settings">
{
  "schema": "0.2.0",
  "name": "UPS Information v2.2 (Mini)",
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
      "name": "upsStatusData",
      "label": "UPS Status Data Device"
    },
    {
      "type": "BOOLEAN",
      "name": "useGradient",
      "label": "Use Gradient Background",
      "default": true
    },
    {
      "type": "BOOLEAN",
      "name": "tileHasLabel",
      "label": "Reserve space for tile label?",
      "default": false
    }
  ]
}
</script>

<!-- External Libraries -->
<script src="https://cdn.sharptools.io/js/custom-tiles/0.2.1/stio.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/gauge.js/1.3.9/gauge.min.js"></script>

<style>
  /* ============================================
     RESET & BASE
     ============================================ */
  * {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
    -webkit-tap-highlight-color: transparent;
    user-select: none;
  }

  /* ============================================
     CSS VARIABLES
     ============================================ */
  :root {
    /* Scale */
    --scale: 1;
    
    /* Base unit */
    --base: calc(1rem * var(--scale));
    
    /* Colors */
    --primary: #2196F3;
    --success: #4CAF50;
    --warning: #FFA726;
    --danger: #EF5350;
    --text: #212121;
    --text-dim: #757575;
    --bg: #FAFAFA;
    --surface: #FFFFFF;
    --border: #E0E0E0;
    
    /* Gradients */
    --grad-normal: linear-gradient(135deg, #1976D2 0%, #2196F3 50%, #64B5F6 100%);
    --grad-danger: linear-gradient(135deg, #B71C1C 0%, #EF5350 50%, #E57373 100%);
    
    /* Typography */
    --font: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
    --font-xs: calc(var(--base) * 1.5);
    --font-base: calc(var(--base) * 2.5);
    
    /* Spacing */
    --gap: calc(var(--base) * 0.35);
    
    /* Components */
    --gauge-size: calc(200px * var(--scale));
    
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
    font-family: var(--font);
    font-weight: 400;
    line-height: 1.4;
    -webkit-font-smoothing: antialiased;
    background: var(--bg);
    color: var(--text);
  }

  /* ============================================
     CONTAINER
     ============================================ */
  .container {
    width: 100%;
    height: 100%;
    padding: var(--gap);
    padding-top: calc(var(--gap) * 20);
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    transition: background var(--transition);
  }

  .container.gradient {
    background: var(--grad-normal);
  }

  .container.gradient.failure {
    background: var(--grad-danger);
  }

  .container.gradient * {
    color: white !important;
  }

  .container.gradient canvas {
    filter: brightness(1.2);
  }

  /* ============================================
     LAYOUT
     ============================================ */
  .grid {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: calc(var(--gap) * 2);
    width: 100%;
    max-width: 800px;
  }

  .gauges {
    display: flex;
    justify-content: center;
    align-items: center;
    gap: 5%;
    width: 100%;
  }

  /* ============================================
     GAUGE COMPONENT
     ============================================ */
  .gauge-box {
    display: flex;
    flex-direction: column;
    align-items: center;
    width: 40%;
  }

  .label {
    font-size: var(--font-xs);
    font-weight: 600;
    text-transform: uppercase;
    letter-spacing: 0.5px;
    opacity: 0.7;
    margin-bottom: -15px;
  }

  .gauge-wrap {
    width: 100%;
    height: calc(var(--gauge-size) * 0.84);
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    margin: 10px 0;
  }

  .gauge-wrap canvas {
    width: 100% !important;
    height: auto !important;
  }

  .value {
    display: flex;
    align-items: center;
    gap: calc(var(--gap) * 0.5);
    font-size: var(--font-base);
    font-weight: 600;
    margin-top: -15px;
  }

  .icon {
    width: calc(36px * var(--scale));
    height: calc(36px * var(--scale));
    fill: currentColor;
    opacity: 0.8;
  }
  
  /* ============================================
     STATUS
     ============================================ */
  .status {
    background: var(--surface);
    border-radius: var(--radius);
    padding: 0 var(--gap);
    box-shadow: var(--shadow);
    text-align: center;
    min-width: 80%;
    margin-top: var(--gap);
  }

  .container.gradient .status {
    background: rgba(255,255,255,0.15);
  }

  .status-text {
    font-size: var(--font-base);
    font-weight: 600;
    color: var(--primary);
    line-height: 1.2;
  }

  .container.gradient .status-text {
    color: white !important;
  }

  .status-text.critical { color: var(--danger); }
  .status-text.warning { color: var(--warning); }
  .status-text.good { color: var(--success); }

  /* ============================================
     LOADING
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

  <!-- Main -->
  <div id="main" class="hidden grid">
    
    <!-- Gauges -->
    <div class="gauges">
      <!-- Input -->
      <div class="gauge-box">
        <div class="label">INPUT</div>
        <div class="gauge-wrap">
          <canvas id="inputGauge"></canvas>
        </div>
        <div class="value">
          <svg class="icon" viewBox="0 0 24 24">
            <path d="M5 20H19V18H5M19 9H15V3H9V9H5L12 16L19 9Z"/>
          </svg>
          <span id="inputVal">0 V</span>
        </div>
      </div>

      <!-- Output -->
      <div class="gauge-box">
        <div class="label">OUTPUT</div>
        <div class="gauge-wrap">
          <canvas id="outputGauge"></canvas>
        </div>
        <div class="value">
          <svg class="icon" viewBox="0 0 24 24">
            <path d="M9 16V10H5L12 3L19 10H15V16H9M5 20V18H19V20H5Z"/>
          </svg>
          <span id="outputVal">0 V</span>
        </div>
      </div>
    </div>

    <!-- Status -->
    <div class="status">
      <div class="status-text" id="statusText">Initializing...</div>
    </div>
  </div>
</div>

<script>
(function() {
  'use strict';

  /* ============================================
     UPS INFORMATION v2.2 (Mini) - Optimized
     ============================================ */

  // Configuration
  const Config = {
    devices: {},
    display: { gradient: true, hasLabel: false }
  };

  // State
  const State = {
    data: { inputV: 0, outputV: 0, status: 'OL' },
    subs: [],
    gauges: {}
  };

  // Status Codes
  const StatusCodes = {
    'OL': 'On Line',
    'OB': 'On Battery',
    'LB': 'Low Battery',
    'RB': 'Replace Battery',
    'CHRG': 'Charging',
    'DISCHRG': 'Discharging',
    'BYPASS': 'Bypass',
    'CAL': 'Calibration',
    'OFF': 'Offline',
    'OVER': 'Overload',
    'TRIM': 'SmartTrim',
    'BOOST': 'SmartBoost',
    'FSD': 'Forced Shutdown',
    'ALARM': 'Alarm',
    'COMMOK': 'Communication OK',
    'COMMBAD': 'Communication Lost'
  };
  
  // DOM References
  const DOM = {};

  function initDOM() {
    const ids = ['container', 'loading', 'main', 'inputVal', 'outputVal', 'statusText'];
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
      
      const scale = size < 150 ? 0.25 :
                   size < 200 ? 0.35 :
                   size < 250 ? 0.45 :
                   size < 300 ? 0.55 :
                   size < 400 ? 0.7 :
                   size < 500 ? 0.85 :
                   size < 600 ? 1.0 :
                   size < 800 ? 1.3 :
                   1.6;

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

  // Gauge Manager - unified
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
        strokeColor: 'rgba(255,255,255,0.1)',
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

      State.gauges.input = this.create(document.getElementById('inputGauge'));
      State.gauges.output = this.create(document.getElementById('outputGauge'));
    },

    animate(type, target) {
      const gauge = State.gauges[type];
      const element = DOM[type + 'Val'];
      
      if (!gauge || !element) return;
      
      const start = gauge.displayedValue || 0;
      const delta = target - start;
      const frames = 180;
      let frame = 0;
      
      const step = () => {
        frame++;
        const progress = frame / frames;
        
        // Easing
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

  // Display Updates
  const Display = {
    update() {
      // Update gauges
      Gauges.animate('input', State.data.inputV);
      Gauges.animate('output', State.data.outputV);
      
      // Power status
      const onUtility = State.data.inputV > 90;
      
      if (Config.display.gradient) {
        DOM.container.classList.toggle('failure', !onUtility);
      }

      // Status text
      const status = State.data.status;
      DOM.statusText.textContent = StatusCodes[status] || status;

      // Status color
      DOM.statusText.className = 'status-text';
      
      if (['LB','RB','ALARM','COMMBAD','FSD'].includes(status)) {
        DOM.statusText.classList.add('critical');
      } else if (['OB','BYPASS','OVER','DISCHRG'].includes(status)) {
        DOM.statusText.classList.add('warning');
      } else if (['OL','CHRG','COMMOK'].includes(status)) {
        DOM.statusText.classList.add('good');
      }
    },

    theme() {
      DOM.container.classList.toggle('gradient', Config.display.gradient);
      
      if (Config.display.hasLabel) {
        DOM.container.style.paddingTop = '40px';
      } else {
        DOM.container.style.paddingTop = 'calc(var(--gap) * 10)';
      }
    },

    show() {
      DOM.loading.classList.add('hidden');
      DOM.main.classList.remove('hidden');
    }
  };

  // Device Manager - unified subscription
  const Devices = {
    subscribe(device, attr, key) {
      if (!device?.attributes?.[attr]) return;
      
      const attribute = device.attributes[attr];
      
      // Initial value
      if (attribute.value != null) {
        this.update(key, attribute.value);
      }
      
      // Subscribe
      const sub = attribute.onValue(v => {
        if (v != null) {
          this.update(key, v);
        }
      });
      
      State.subs.push(sub);
    },

    update(key, value) {
      if (key === 'status') {
        State.data.status = value || 'OL';
      } else {
        State.data[key] = parseFloat(value) || 0;
      }
      Display.update();
    },

    setup() {
      this.subscribe(Config.devices.inputVoltage, 'voltage', 'inputV');
      this.subscribe(Config.devices.outputVoltage, 'voltage', 'outputV');
      this.subscribe(Config.devices.upsStatusData, 'status', 'status');
      
      // Periodic refresh
      if (Config.devices.upsStatusData?.attributes?.status) {
        setInterval(() => {
          Config.devices.upsStatusData.attributes.status.refresh();
        }, 10000);
      }
    }
  };

  // Application
  const App = {
    init() {
      initDOM();
      Sizing.init();
      
      if (typeof stio === 'undefined') {
        setTimeout(() => this.init(), 200);
        return;
      }
      
      stio.ready(data => {
        // Configure
        Config.devices = {
          inputVoltage: data.settings.inputVoltage,
          outputVoltage: data.settings.outputVoltage,
          upsStatusData: data.settings.upsStatusData
        };
        
        Config.display.gradient = data.settings.useGradient !== false;
        Config.display.hasLabel = data.settings.tileHasLabel === true;

        // Setup
        Display.theme();
        Devices.setup();
        Display.show();
        
        // Init gauges
        setTimeout(() => {
          Gauges.init();
          Display.update();
        }, 250);
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