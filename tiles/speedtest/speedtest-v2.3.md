<!DOCTYPE html>
<html lang="en">
<head>
<!--
╔════════════════════════════════════════════════════════════════════════════╗
║                        SPEEDTEST MONITOR v2.3                              ║
║            Professional Speed Test Monitor for SharpTools                  ║
║                     with Ookla Speedtest Integration                       ║
╠════════════════════════════════════════════════════════════════════════════╣
║                                                                            ║
║  VERSION 2.3 CHANGELOG:                                                    ║
║  • UPDATED: Date format changed from MMDDHH to YYMMDDDD                    ║
║  • IMPROVED: Chart zero point now uses earliest date across all variables  ║
║  • ENHANCED: Date parsing logic for year support                           ║
║  • FIXED: Chart plotting algorithm for multi-day data                      ║
║  • OPTIMIZED: Global time range calculation                                ║
║                                                                            ║
║  DESCRIPTION:                                                              ║
║  Enterprise-grade speed test monitoring solution providing real-time       ║
║  visualization of network performance metrics across three independent     ║
║  test locations. Designed for seamless integration with Ookla Speedtest    ║
║  CLI data, this tile delivers professional network monitoring capabilities ║
║  within the SharpTools dashboard environment.                              ║
║                                                                            ║
║  KEY FEATURES:                                                             ║
║  • Three independent speed test monitors with synchronized visualization   ║
║  • Historical tracking with multi-day support                              ║
║  • Color-coded performance metrics (Download: Blue, Upload: Red)           ║
║  • SYNCHRONIZED Y-AXIS across all graphs for direct comparison             ║
║  • Real-time status indicators with test validity monitoring               ║
║  • Automatic responsive scaling for all screen sizes                       ║
║  • Dark mode support with automatic detection                              ║
║  • Optional gradient background (horizontal left-to-right)                 ║
║  • Dynamic chart height adjustment based on container dimensions           ║
║  • Single unified codebase - ZERO DUPLICATION                              ║
║                                                                            ║
║  TECHNICAL ARCHITECTURE:                                                   ║
║  • Unified Codebase: Single implementation eliminates code duplication     ║
║  • Dynamic DOM Generation: Programmatic creation of speed blocks           ║
║  • Zero HTML Redundancy: Single template for all speed monitors            ║
║  • Optimized CSS: Consolidated media queries and CSS variables             ║
║  • Component-Based Design: Modular architecture for maintainability        ║
║  • Mobile-First Approach: Optimized for mobile with desktop enhancements   ║
║  • Dynamic Scaling: Automatic adaptation without fixed breakpoints         ║
║  • Efficient Memory Management: Proper subscription handling               ║
║  • Performance Optimized: Debounced resize handlers, minimal reflows       ║
║                                                                            ║
║  VARIABLE FORMAT SPECIFICATION:                                            ║
║  Each SharpTools variable must contain comma-separated values:             ║
║  1. Test validity status: true/false                                       ║
║  2. Download speed (Mbps): decimal value                                   ║
║  3. Upload speed (Mbps): decimal value                                     ║
║  4. Ping latency (ms): decimal value                                       ║
║  5. Server name: string (Ookla server identifier)                          ║
║  6. Timestamp: milliseconds since epoch or ISO 8601 format                 ║
║  7-9. Oldest test data: YYMMDDDD,download,upload                           ║
║  10-N. Historical data triplets: YYMMDDDD,download,upload (max 24 sets)    ║
║                                                                            ║
║  Date Format: YYMMDDDD                                                     ║
║  - YY: Year (2 digits, e.g., 25 for 2025)                                  ║
║  - MM: Month (01-12)                                                       ║
║  - DD: Day (01-31)                                                         ║
║  - HH: Hour (00-23)                                                        ║
║                                                                            ║
║  Example Variable Content:                                                 ║
║  "true,450.5,35.2,12.5,Speedtest.net,1735689600000,25010114,425.1,32.5,   ║
║  25010115,430.2,33.1,25010116,445.8,34.7,25010117,442.3,34.2..."          ║
║                                                                            ║
║  AUTHOR: Wilson Marcolin                                                   ║
║  CONTRIBUTORS: Claude AI Assistant                                         ║
║  VERSION: 2.3 - Year Support & Enhanced Plotting                           ║
║  RELEASE: January 2025                                                     ║
║  LICENSE: MIT                                                              ║
║                                                                            ║
╚════════════════════════════════════════════════════════════════════════════╝
-->

  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=5.0, user-scalable=yes">
  <meta name="apple-mobile-web-app-capable" content="yes">
  <title>SpeedTest Monitor v2.3</title>

  <!-- Tile Settings Schema -->
  <script type="application/json" id="tile-settings">
  {
    "schema": "0.2.0",
    "settings": [
      {
        "type": "VARIABLE",
        "name": "speedVar1",
        "label": "Speed Test Variable 1",
        "filters": {"type": "String"}
      },
      {
        "type": "VARIABLE",
        "name": "speedVar2",
        "label": "Speed Test Variable 2",
        "filters": {"type": "String"}
      },
      {
        "type": "VARIABLE",
        "name": "speedVar3",
        "label": "Speed Test Variable 3",
        "filters": {"type": "String"}
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
    ],
    "name": "SpeedTest Monitor v2.3"
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
       CSS VARIABLES - DESIGN SYSTEM
       ============================================ */
    :root {
      /* Dynamic scale factor for responsive design */
      --scale-factor: 1;
      
      /* Color Palette - Light Theme */
      --color-primary: #2196F3;
      --color-success: #4CAF50;
      --color-warning: #FFA726;
      --color-danger: #EF5350;
      --color-text: #212121;
      --color-text-secondary: #757575;
      --color-background: #FAFAFA;
      --color-surface: #FFFFFF;
      --color-border: #E0E0E0;
      
      /* Speed Test Specific Colors */
      --speed-download: #2196F3;
      --speed-upload: #F44336;
      --speed-ok: #4CAF50;
      --speed-nok: #F44336;
      
      /* Gradient Colors - Horizontal (left to right) */
      --gradient-start: #404040;  /* Dark gray on left */
      --gradient-mid: #606060;    /* Medium gray in middle */
      --gradient-end: #909090;    /* Light gray on right */
      
      /* Typography Scale */
      --font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Oxygen-Sans, Ubuntu, Cantarell, "Helvetica Neue", sans-serif;
      --font-size-xs: calc(0.625rem * var(--scale-factor));
      --font-size-sm: calc(0.75rem * var(--scale-factor));
      --font-size-base: calc(0.875rem * var(--scale-factor));
      --font-size-lg: calc(1rem * var(--scale-factor));
      --font-size-xl: calc(1.125rem * var(--scale-factor));
      
      /* Spacing System */
      --spacing-xs: calc(0.125rem * var(--scale-factor));
      --spacing-sm: calc(0.25rem * var(--scale-factor));
      --spacing-md: calc(0.5rem * var(--scale-factor));
      --spacing-lg: calc(0.75rem * var(--scale-factor));
      --spacing-xl: calc(1rem * var(--scale-factor));
      
      /* Visual Effects */
      --shadow-sm: 0 1px 3px rgba(0,0,0,0.12);
      --shadow-md: 0 2px 6px rgba(0,0,0,0.16);
      --radius: 6px;
      --transition: 300ms ease;
      
      /* Chart dimensions - responsive values */
      --chart-min-height: calc(80px * var(--scale-factor));
      --chart-max-height: calc(160px * var(--scale-factor));
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
       BASE DOCUMENT STYLES
       ============================================ */
    html, body {
      height: 100%;
      overflow: auto;
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
      padding: 1px var(--spacing-lg);  /* Mobile primeiro */
      display: flex;
      flex-direction: column;
      transition: background var(--transition);
      position: relative;
      overflow: hidden;
      box-sizing: border-box;
    }

    /* Desktop mantém mais espaço superior */
    @media (min-width: 768px) {
      .speedtest-container {
        padding: 12px var(--spacing-lg);
      }
    }
    
    /* Horizontal gradient from left (dark) to right (light) */
    .speedtest-container.gradient {
      background: linear-gradient(90deg,     /* 90deg = left to right */
        var(--gradient-start) 0%,            /* Dark gray on left */
        var(--gradient-mid) 40%,             /* Medium gray at 40% */
        var(--gradient-end) 100%);           /* Light gray on right */
    }

    .speedtest-container.gradient * {
      color: white !important;
    }

    /* ============================================
       HEADER SECTION
       ============================================ */
    .header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      padding: var(--spacing-md);
      margin-bottom: var(--spacing-xs);  /* Reduzido para mobile */
      flex-shrink: 0;
    }

    @media (min-width: 768px) {
      .header {
        margin-bottom: var(--spacing-md);  /* Normal no desktop */
      }
    }
    
    .logo-group {
      display: flex;
      align-items: center;
      gap: var(--spacing-sm);
    }

    .logo-icon {
      width: calc(12px * var(--scale-factor));
      height: calc(12px * var(--scale-factor));
      fill: currentColor;
      opacity: 0.8;
    }

    .logo-text {
      font-size: calc(14px * var(--scale-factor));
      font-weight: bold;
      opacity: 0.8;
    }

    /* ============================================
       SPEED BLOCKS CONTAINER
       ============================================ */
    #speedBlocksContainer {
      display: flex;
      flex-direction: column;
      flex: 1 1 auto;
      min-height: 0;
      gap: var(--spacing-sm);
    }

    /* ============================================
       SPEED TEST BLOCK
       ============================================ */
    .speed-block {
      background: var(--color-surface);
      border-radius: var(--radius);
      padding: calc(24px * var(--scale-factor)) calc(12px * var(--scale-factor)) calc(0px * var(--scale-factor)); ;
      box-shadow: var(--shadow-sm);
      
  display: flex;
  flex-direction: column;
  flex: 1 1 0;  /* Isso faz os blocos dividirem o espaço igualmente */
  min-height: 0;
      
      overflow: hidden;
    }

    .speed-block:last-child {
      margin-bottom: 0;
    }
    
    .speedtest-container.gradient .speed-block {
      background: rgba(255, 255, 255, 0.1);
      backdrop-filter: blur(10px);
    }

    /* ============================================
       MAIN CONTENT LAYOUT
       ============================================ */
    #mainContent {
      display: flex;
      flex-direction: column;
      flex: 1 1 auto;
      min-height: 0;
      overflow: hidden;
    }

    /* ============================================
       STATUS ROW - 3 COLUMN LAYOUT
       ============================================ */
    .status-row {
      display: flex;
      align-items: center;
      width: 100%;
      gap: 0;
      margin-bottom: var(--spacing-md);
      flex: 0 0 auto;
      position: relative;
      min-height: calc(40px * var(--scale-factor));
    }

    /* Column positioning */
    .status-column {
      position: absolute;
      left: 10px;
      top: 50%;
      transform: translateY(-50%);
      display: flex;
      align-items: center;
      justify-content: flex-start;
    }

    .info-column {
      position: absolute;
      left: 100px;
      right: 120px;
      top: 50%;
      transform: translateY(-50%);
      display: flex;
      flex-direction: column;
      gap: calc(var(--spacing-xs) * 0.5);
      justify-content: center;
    }

    .time-column {
      position: absolute;
      right: 10px;
      top: 50%;
      transform: translateY(-50%);
      text-align: center;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
    }

    /* Status Badge */
    .status-badge {
      padding: calc(var(--spacing-xs) * 1.5) calc(var(--spacing-sm) * 1.5);
      border-radius: 15px;
      font-weight: 600;
      font-size: calc(0.9rem * var(--scale-factor));
      white-space: nowrap;
      min-width: calc(60px * var(--scale-factor));
      text-align: center;
      display: inline-block;
    }
    
    .status-badge.ok {
      background: var(--speed-ok);
      color: white;
    }

    .status-badge.nok {
      background: var(--speed-nok);
      color: white;
    }

    /* Info Lines */
    .info-line {
      display: flex;
      align-items: center;
      gap: calc(var(--spacing-lg) * 1.5);
      flex-wrap: nowrap;
      overflow: hidden;
    }

    .info-line > svg {
      margin-right: calc(var(--spacing-xs) * 0.5);
    }

    /* Time Display */
    .test-time {
      font-size: calc(0.7rem * var(--scale-factor));
      opacity: 0.7;
      white-space: nowrap;
      line-height: 1.2;
      margin-left: auto;
    }

    /* ============================================
       SPEED ICONS AND VALUES
       ============================================ */
    .speed-icon {
      width: calc(20px * var(--scale-factor));
      height: calc(20px * var(--scale-factor));
      fill: currentColor;
      flex-shrink: 0;
    }

    .download-icon {
      fill: var(--speed-download) !important;
    }
    
    .upload-icon {
      fill: var(--speed-upload) !important;
    }

    .speed-value {
      font-size: calc(0.9rem * var(--scale-factor));
      font-weight: 600;
      white-space: nowrap;
      margin-left: calc(var(--spacing-xs) * 0.25);
      margin-right: calc(var(--spacing-lg) * 1.5);
    }

    .download-value {
      color: var(--speed-download);
    }

    .upload-value {
      color: var(--speed-upload);
    }

    .ping-value {
      font-size: calc(0.9rem * var(--scale-factor));
      font-weight: 600;
      opacity: 0.9;
      white-space: nowrap;
      margin-left: calc(var(--spacing-xs) * 0.25);
      margin-right: calc(var(--spacing-lg) * 1.5);
    }

    .server-name {
      font-size: calc(0.8rem * var(--scale-factor));
      font-weight: 600;
      opacity: 0.8;
      overflow: hidden;
      text-overflow: ellipsis;
      white-space: nowrap;
      max-width: calc(150px * var(--scale-factor));
      margin-left: calc(var(--spacing-xs) * 0.25);
    }

    /* ============================================
       CHART CONTAINER
       ============================================ */
    .chart-container {
      position: relative;
      width: 100%;
      flex: 1 1 auto;
      display: flex;
      flex-direction: column;
      margin-top: var(--spacing-sm);
    }
    
    /* Dynamic height adjustments based on number of charts */
    .speedtest-container[data-chart-count="1"] .chart-container {
      min-height: calc(100px * var(--scale-factor));
      max-height: calc(200px * var(--scale-factor));
    }

    .speedtest-container[data-chart-count="2"] .chart-container {
      min-height: calc(90px * var(--scale-factor));
      max-height: calc(180px * var(--scale-factor));
    }

    .speedtest-container[data-chart-count="3"] .chart-container {
      min-height: var(--chart-min-height);
      max-height: var(--chart-max-height);
    }

    .chart-canvas {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
    }

    /* ============================================
       RESPONSIVE ADJUSTMENTS - CONSOLIDATED
       ============================================ */
    
    /* Mobile devices */
    @media (max-width: 768px) {
      :root {
        --chart-min-height: calc(105px * var(--scale-factor));
        --chart-max-height: calc(220px * var(--scale-factor));
      }
      
      .speedtest-container[data-chart-count="1"] .chart-container {
        min-height: calc(115px * var(--scale-factor));
        max-height: calc(230px * var(--scale-factor));
      }
  
      .speedtest-container[data-chart-count="2"] .chart-container {
        min-height: calc(110px * var(--scale-factor));
        max-height: calc(200px * var(--scale-factor));
      }
    }
    
    /* Small mobile devices */
    @media (max-width: 480px) {
      .status-column { left: 10px; }
      .info-column { left: 70px; right: 90px; }
      .time-column { right: 8px; text-align: center; }
      
      .status-column,
      .info-column,
      .time-column {
        width: 100%;
      }
      
      .info-line {
        justify-content: flex-start;
      }
    }

    /* Desktop devices */
    @media (min-width: 1024px) {
      :root {
        --chart-min-height: calc(85px * var(--scale-factor));
        --chart-max-height: calc(170px * var(--scale-factor));
      }
      
      .speedtest-container[data-chart-count="1"] .chart-container {
        min-height: calc(175px * var(--scale-factor));
        max-height: calc(350px * var(--scale-factor));
        margin-top: calc(30px * var(--scale-factor));
        padding-top: calc(10px * var(--scale-factor));
      }
  
      .speedtest-container[data-chart-count="2"] .chart-container {
        min-height: calc(95px * var(--scale-factor));
        max-height: calc(190px * var(--scale-factor));
        margin-top: calc(30px * var(--scale-factor));
        padding-top: calc(10px * var(--scale-factor));
      }
  
      .speedtest-container[data-chart-count="3"] .chart-container {
        margin-top: calc(30px * var(--scale-factor));
        padding-top: calc(10px * var(--scale-factor));
      }
  
      .status-badge {
        padding: calc(var(--spacing-xs) * 3) calc(var(--spacing-sm) * 3);
        font-size: calc(1.8rem * var(--scale-factor));
        border-radius: 24px;
        min-width: calc(100px * var(--scale-factor));
      }
    
      .speed-icon {
        width: calc(40px * var(--scale-factor));
        height: calc(40px * var(--scale-factor));
      }
  
      .speed-value,
      .ping-value {
        font-size: calc(1.8rem * var(--scale-factor));
        margin-left: calc(var(--spacing-xs) * 0.5);
        margin-right: calc(var(--spacing-lg) * 3);
      }
  
      .server-name {
        font-size: calc(1.6rem * var(--scale-factor));
        max-width: calc(300px * var(--scale-factor));
      }
  
      .test-time {
        font-size: calc(1.2rem * var(--scale-factor));
      }
  
      .status-row {
        min-height: 80px;
      }
  
      .status-column { left: 20px; }
      .info-column { left: 230px; right: 200px; }
      .time-column { right: 25px; }
    }
    
    /* ============================================
       LOADING STATE
       ============================================ */
    .loading {
      display: flex;
      align-items: center;
      justify-content: center;
      height: 100vh;
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
    <div id="loadingView" class="loading">
      <div class="spinner"></div>
    </div>

    <!-- Main Content Container - Dynamically Populated -->
    <div id="mainContent" class="hidden">
      <!-- Header with Branding -->
      <div class="header">
        <!-- Speedtest Logo with Icon -->
        <div class="logo-group">
          <svg class="logo-icon" viewBox="0 0 24 24">
            <path d="M 12 16 A 3 3 0 0 1 9 13 C 9 11.88 9.61 10.9 10.5 10.39 L 20.21 4.77 L 14.68 14.35 C 14.18 15.33 13.17 16 12 16 M 12 3 C 13.81 3 15.5 3.5 16.97 4.32 L 14.87 5.53 C 14 5.19 13 5 12 5 A 8 8 0 0 0 4 13 C 4 15.21 4.89 17.21 6.34 18.65 H 6.35 C 6.74 19.04 6.74 19.67 6.35 20.06 C 5.96 20.45 5.32 20.45 4.93 20.07 V 20.07 C 3.12 18.26 2 15.76 2 13 A 10 10 0 0 1 12 3 M 22 13 C 22 15.76 20.88 18.26 19.07 20.07 V 20.07 C 18.68 20.45 18.05 20.45 17.66 20.06 C 17.27 19.67 17.27 19.04 17.66 18.65 V 18.65 C 19.11 17.2 20 15.21 20 13 C 20 12 19.81 11 19.46 10.1 L 20.67 8 C 21.5 9.5 22 11.18 22 13 Z" />
          </svg>
          <span class="logo-text">SpeedTest</span>
        </div>
        
        <!-- Ookla Branding -->
        <span class="logo-text">Ookla</span>
      </div>

      <!-- Speed Blocks Container - Dynamically Generated -->
      <div id="speedBlocksContainer"></div>
    </div>
  </div>

  <script>
  (function() {
    'use strict';

    // ============================
    // SVG Icons Library
    // ============================
    const Icons = {
      download: 'M 6.5 20 Q 4.22 20 2.61 18.43 Q 1 16.85 1 14.58 Q 1 12.63 2.17 11.1 Q 3.35 9.57 5.25 9.15 Q 5.83 7.13 7.39 5.75 Q 8.95 4.38 11 4.08 V 12.15 L 9.4 10.6 L 8 12 L 12 16 L 16 12 L 14.6 10.6 L 13 12.15 V 4.08 Q 15.58 4.43 17.29 6.39 Q 19 8.35 19 11 Q 20.73 11.2 21.86 12.5 Q 23 13.78 23 15.5 Q 23 17.38 21.69 18.69 Q 20.38 20 18.5 20 Z',
      upload: 'M 11 20 H 6.5 Q 4.22 20 2.61 18.43 Q 1 16.85 1 14.58 Q 1 12.63 2.17 11.1 Q 3.35 9.57 5.25 9.15 Q 5.88 6.85 7.75 5.43 Q 9.63 4 12 4 Q 14.93 4 16.96 6.04 Q 19 8.07 19 11 Q 20.73 11.2 21.86 12.5 Q 23 13.78 23 15.5 Q 23 17.38 21.69 18.69 Q 20.38 20 18.5 20 H 13 V 12.85 L 14.6 14.4 L 16 13 L 12 9 L 8 13 L 9.4 14.4 L 11 12.85 Z',
      ping: 'M 18.5 14 C 19.9 14 21 15.1 21 16.5 C 21 17.9 19.9 19 18.5 19 C 17.1 19 16 17.9 16 16.5 C 16 15.1 17.1 14 18.5 14 M 7 15 C 7 15 8 16 8 17 V 20.5 C 8 21.3 8.7 22 9.5 22 C 10.3 22 11 21.3 11 20.5 V 17 C 11 16 12 15 12 15 H 7 M 8 14 H 11 C 11 14 16 14 16 9 C 16 4 12 2 9.5 2 C 7 2 3 4 3 9 C 3 14 8 14 8 14 Z',
      server: 'M16.36,14C16.44,13.34 16.5,12.68 16.5,12C16.5,11.32 16.44,10.66 16.36,10H19.74C19.9,10.64 20,11.31 20,12C20,12.69 19.9,13.36 19.74,14M14.59,19.56C15.19,18.45 15.65,17.25 15.97,16H18.92C17.96,17.65 16.43,18.93 14.59,19.56M14.34,14H9.66C9.56,13.34 9.5,12.68 9.5,12C9.5,11.32 9.56,10.65 9.66,10H14.34C14.43,10.65 14.5,11.32 14.5,12C14.5,12.68 14.43,13.34 14.34,14M12,19.96C11.17,18.76 10.5,17.43 10.09,16H13.91C13.5,17.43 12.83,18.76 12,19.96M8,8H5.08C6.03,6.34 7.57,5.06 9.4,4.44C8.8,5.55 8.35,6.75 8,8M5.08,16H8C8.35,17.25 8.8,18.45 9.4,19.56C7.57,18.93 6.03,17.65 5.08,16M4.26,14C4.1,13.36 4,12.69 4,12C4,11.31 4.1,10.64 4.26,10H7.64C7.56,10.66 7.5,11.32 7.5,12C7.5,12.68 7.56,13.34 7.64,14M12,4.03C12.83,5.23 13.5,6.57 13.91,8H10.09C10.5,6.57 11.17,5.23 12,4.03M18.92,8H15.97C15.65,6.75 15.19,5.55 14.59,4.44C16.43,5.07 17.96,6.34 18.92,8M12,2C6.47,2 2,6.5 2,12A10,10 0 0,0 12,22A10,10 0 0,0 22,12A10,10 0 0,0 12,2Z'
    };

    // ============================
    // Configuration Module
    // ============================
    const Config = {
      variables: {},
      display: {
        useGradient: true,
        tileHasLabel: false
      }
    };

    // ============================
    // State Management Module
    // ============================
    const State = {
      data: {},
      charts: {},
      subscriptions: [],
      globalTimeRange: null
    };

    // ============================
    // DOM References Module
    // ============================
    const DOM = {
      elements: {},
      
      init() {
        this.container = document.getElementById('speedtestContainer');
        this.loadingView = document.getElementById('loadingView');
        this.mainContent = document.getElementById('mainContent');
        this.speedBlocksContainer = document.getElementById('speedBlocksContainer');
      },

      createSpeedBlock(index) {
        const block = document.createElement('div');
        block.className = 'speed-block';
        block.id = `speedBlock${index}`;
        block.innerHTML = `
          <div class="status-row">
            <!-- Column 1: Status Badge -->
            <div class="status-column">
              <div class="status-badge" id="status${index}">--</div>
            </div>
            
            <!-- Column 2: Speed Info (2 lines) -->
            <div class="info-column">
              <!-- Line 1: Download and Upload -->
              <div class="info-line">
                <svg class="speed-icon download-icon" viewBox="0 0 24 24">
                  <path d="${Icons.download}" />
                </svg>
                <span class="speed-value download-value" id="download${index}">-- Mbps</span>
                <svg class="speed-icon upload-icon" viewBox="0 0 24 24">
                  <path d="${Icons.upload}" />
                </svg>
                <span class="speed-value upload-value" id="upload${index}">-- Mbps</span>
              </div>
              <!-- Line 2: Ping and Server -->
              <div class="info-line">
                <svg class="speed-icon" viewBox="0 0 24 24">
                  <path d="${Icons.ping}" />
                </svg>
                <span class="ping-value" id="ping${index}">-- ms</span>
                <svg class="speed-icon" viewBox="0 0 24 24">
                  <path d="${Icons.server}" />
                </svg>
                <span class="server-name" id="server${index}">--</span>
              </div>
            </div>
            
            <!-- Column 3: Time -->
            <div class="time-column">
              <div class="test-time" id="time${index}">--</div>
            </div>
          </div>
          
          <div class="chart-container">
            <canvas class="chart-canvas" id="chart${index}"></canvas>
          </div>
        `;
        
        this.speedBlocksContainer.appendChild(block);
        
        // Store references to elements
        this.elements[`block${index}`] = block;
        this.elements[`status${index}`] = document.getElementById(`status${index}`);
        this.elements[`download${index}`] = document.getElementById(`download${index}`);
        this.elements[`upload${index}`] = document.getElementById(`upload${index}`);
        this.elements[`ping${index}`] = document.getElementById(`ping${index}`);
        this.elements[`time${index}`] = document.getElementById(`time${index}`);
        this.elements[`server${index}`] = document.getElementById(`server${index}`);
        this.elements[`chart${index}`] = document.getElementById(`chart${index}`);
        
        return block;
      },

      cleanup() {
        // Clear all element references to prevent memory leaks
        this.elements = {};
        if (this.speedBlocksContainer) {
          this.speedBlocksContainer.innerHTML = '';
        }
      }
    };

    // ============================
    // Data Parser Module - UPDATED FOR YYMMDDDD
    // ============================
    const DataParser = {
      parse(variableString) {
        if (!variableString) return null;
        
        const parts = variableString.split(',');
        if (parts.length < 9) return null;
        
        // Parse timestamp - handle ISO 8601 format
        let timestamp = parts[5];
        if (timestamp && timestamp.includes('T')) {
          timestamp = new Date(timestamp).getTime();
        } else {
          timestamp = parseInt(timestamp) || Date.now();
        }
        
        const data = {
          isValid: parts[0] === 'true',
          lastDownload: parseFloat(parts[1]) || 0,
          lastUpload: parseFloat(parts[2]) || 0,
          ping: parseFloat(parts[3]) || 0,
          server: parts[4] || 'Unknown',
          timestamp: timestamp,
          history: []
        };
        
        // Parse historical data with new YYMMDDDD format
        for (let i = 6; i < parts.length; i += 3) {
          if (i + 2 < parts.length) {
            const dateStr = parts[i];
            const download = parseFloat(parts[i + 1]) || 0;
            const upload = parseFloat(parts[i + 2]) || 0;
            
            // Parse YYMMDDDD format
            if (dateStr && dateStr.length === 8) {
              const year = 2000 + parseInt(dateStr.substr(0, 2));  // Convert YY to full year
              const month = parseInt(dateStr.substr(2, 2));
              const day = parseInt(dateStr.substr(4, 2));
              const hour = parseInt(dateStr.substr(6, 2));
              
              // Create a Date object for this data point
              const pointDate = new Date(year, month - 1, day, hour, 0, 0);
              
              data.history.push({
                year,
                month,
                day,
                hour,
                timestamp: pointDate.getTime(),  // Store timestamp for easier comparison
                download,
                upload
              });
            }
          }
        }
        
        // Sort history by timestamp to ensure proper ordering
        data.history.sort((a, b) => a.timestamp - b.timestamp);
        
        return data;
      }
    };

    // ============================
    // Time Utilities Module
    // ============================
    const TimeUtils = {
      formatTimestamp(timestamp) {
        if (!timestamp) return '<div>--</div><div>--</div>';
        
        const date = new Date(parseInt(timestamp));
        const month = String(date.getMonth() + 1).padStart(2, '0');
        const day = String(date.getDate()).padStart(2, '0');
        const year = String(date.getFullYear()).slice(-2);
        const hours = date.getHours();
        const minutes = String(date.getMinutes()).padStart(2, '0');
        const ampm = hours >= 12 ? 'PM' : 'AM';
        const displayHours = hours % 12 || 12;
        
        return `<div>${month}/${day}/${year}</div><div>${displayHours}:${minutes} ${ampm}</div>`;
      }
    };

    // ============================
    // Dynamic Sizing Module
    // ============================
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
        const height = window.innerHeight;
        const minDimension = Math.min(width, height);

        let scaleFactor;
        if (minDimension < 400) {
          scaleFactor = 0.7;
        } else if (minDimension < 600) {
          scaleFactor = 0.9;
        } else if (minDimension < 800) {
          scaleFactor = 1.1;
        } else if (minDimension < 1000) {
          scaleFactor = 1.3;
        } else {
          scaleFactor = 1.5;
        }

        document.documentElement.style.setProperty('--scale-factor', scaleFactor);
        ChartManager.updateAllCharts();
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
      },

      cleanup() {
        if (this.resizeTimeout) {
          clearTimeout(this.resizeTimeout);
          this.resizeTimeout = null;
        }
      }
    };

    // ============================
    // Chart Manager Module - UPDATED FOR GLOBAL TIME RANGE
    // ============================
    const ChartManager = {
      calculateGlobalTimeRange() {
        let minTimestamp = Infinity;
        let maxTimestamp = -Infinity;
        
        // Find the earliest and latest timestamps across all data
        Object.keys(State.data).forEach(key => {
          const data = State.data[key];
          if (data && data.history && data.history.length > 0) {
            const firstTimestamp = data.history[0].timestamp;
            const lastTimestamp = data.history[data.history.length - 1].timestamp;
            
            if (firstTimestamp < minTimestamp) {
              minTimestamp = firstTimestamp;
            }
            if (lastTimestamp > maxTimestamp) {
              maxTimestamp = lastTimestamp;
            }
          }
        });
        
        if (minTimestamp === Infinity || maxTimestamp === -Infinity) {
          return null;
        }
        
        // Store the global time range
        State.globalTimeRange = {
          startTimestamp: minTimestamp,
          endTimestamp: maxTimestamp,
          totalHours: Math.ceil((maxTimestamp - minTimestamp) / (1000 * 60 * 60)) + 1
        };
        
        return State.globalTimeRange;
      },
      
      getGlobalScaleRange() {
        let allSpeeds = [];
        
        Object.keys(State.data).forEach(key => {
          const data = State.data[key];
          if (data && data.history) {
            data.history.forEach(point => {
              allSpeeds.push(point.download);
              allSpeeds.push(point.upload);
            });
            allSpeeds.push(data.lastDownload);
            allSpeeds.push(data.lastUpload);
          }
        });
        
        if (allSpeeds.length === 0) {
          return { min: 0, max: 100 };
        }
        
        const minSpeed = Math.min(...allSpeeds);
        const maxSpeed = Math.max(...allSpeeds);
        
        const min = Math.floor(minSpeed / 50) * 50;
        const max = Math.ceil(maxSpeed / 50) * 50;
        
        return { 
          min: Math.max(0, min), 
          max: Math.max(max, min + 50)
        };
      },
      
      getYAxisLabels(min, max) {
        const step = 50;
        const labels = [];
        
        for (let value = min; value <= max; value += step) {
          labels.push(value);
        }
        
        return labels;
      },
      
      drawChart(canvas, data, useGlobalRange = true) {
        if (!canvas || !data) return;
        
        // Calculate global time range if not already done
        if (!State.globalTimeRange) {
          this.calculateGlobalTimeRange();
        }
        
        if (!State.globalTimeRange) return;
        
        const ctx = canvas.getContext('2d');
        const rect = canvas.getBoundingClientRect();
        const scaleFactor = parseFloat(getComputedStyle(document.documentElement).getPropertyValue('--scale-factor')) || 1;
        
        canvas.width = rect.width * window.devicePixelRatio;
        canvas.height = rect.height * window.devicePixelRatio;
        ctx.scale(window.devicePixelRatio, window.devicePixelRatio);
        
        const width = rect.width;
        const height = rect.height;
        
        ctx.clearRect(0, 0, width, height);
        
        const { min, max } = useGlobalRange 
          ? this.getGlobalScaleRange()
          : { min: 0, max: 100 };
        
        const yLabels = this.getYAxisLabels(min, max);
        
        const leftPadding = 50 * scaleFactor;
        const rightPadding = 40 * scaleFactor;
        const topPadding = 10 * scaleFactor;
        const bottomPadding = 35 * scaleFactor;
        
        const chartWidth = width - leftPadding - rightPadding;
        const chartHeight = height - topPadding - bottomPadding;
        
        // Draw Y-axis
        ctx.strokeStyle = 'rgba(0, 0, 0, 0.2)';
        ctx.lineWidth = 1;
        ctx.fillStyle = 'rgba(0, 0, 0, 0.8)';
        ctx.font = `bold ${10 * scaleFactor}px sans-serif`;
        ctx.textAlign = 'right';

        yLabels.forEach(value => {
          const y = topPadding + chartHeight - ((value - min) / (max - min)) * chartHeight;
          
          ctx.beginPath();
          ctx.moveTo(leftPadding, y);
          ctx.lineTo(width - rightPadding, y);
          ctx.stroke();
          
          if (value % 100 === 0) {
            ctx.fillStyle = 'rgba(0, 0, 0, 0.8)';
            ctx.fillText(value.toString(), leftPadding - 10, y + 3);
          }
        });
        
        // Draw X-axis labels based on global time range
        const startDate = new Date(State.globalTimeRange.startTimestamp);
        const totalHours = State.globalTimeRange.totalHours;
        const pointSpacing = chartWidth / Math.max(totalHours - 1, 1);
        
        ctx.textAlign = 'center';
        ctx.fillStyle = 'rgba(0, 0, 0, 0.8)';
        
        // Draw hour labels - apenas hora, de 3 em 3 pontos
        for (let i = 0; i < totalHours; i++) {
          const x = leftPadding + i * pointSpacing;
          const currentDate = new Date(State.globalTimeRange.startTimestamp + i * 60 * 60 * 1000);
          const hour = currentDate.getHours();
  
          // Mostrar label a cada 3 horas sempre
          if (i % 3 === 0) {
            ctx.fillText(hour.toString(), x, height - 15 * scaleFactor);
          }
        }
        
        // Plot data points
        const downloadPoints = [];
        const uploadPoints = [];
        
        data.history.forEach((point) => {
          // Calculate position based on timestamp relative to global start
          const hoursFromStart = (point.timestamp - State.globalTimeRange.startTimestamp) / (1000 * 60 * 60);
          
          if (hoursFromStart >= 0 && hoursFromStart < totalHours) {
            const x = leftPadding + (hoursFromStart * pointSpacing);
            const yDown = topPadding + chartHeight - ((point.download - min) / (max - min)) * chartHeight;
            const yUp = topPadding + chartHeight - ((point.upload - min) / (max - min)) * chartHeight;
            
            downloadPoints.push({ x, y: yDown, timestamp: point.timestamp });
            uploadPoints.push({ x, y: yUp, timestamp: point.timestamp });
          }
        });
        
        // Sort points by timestamp (should already be sorted, but ensure)
        downloadPoints.sort((a, b) => a.timestamp - b.timestamp);
        uploadPoints.sort((a, b) => a.timestamp - b.timestamp);
        
        // Draw lines
        this.drawLine(ctx, downloadPoints, getComputedStyle(document.documentElement).getPropertyValue('--speed-download'), scaleFactor);
        this.drawLine(ctx, uploadPoints, getComputedStyle(document.documentElement).getPropertyValue('--speed-upload'), scaleFactor);
      },

      drawLine(ctx, points, color, scaleFactor) {
        if (points.length === 0) return;
        
        ctx.strokeStyle = color;
        ctx.lineWidth = 2 * scaleFactor;
        ctx.beginPath();
        points.forEach((point, index) => {
          if (index === 0) {
            ctx.moveTo(point.x, point.y);
          } else {
            ctx.lineTo(point.x, point.y);
          }
        });
        ctx.stroke();
        
        // Draw points
        ctx.fillStyle = color;
        points.forEach(point => {
          ctx.beginPath();
          ctx.arc(point.x, point.y, 3 * scaleFactor, 0, Math.PI * 2);
          ctx.fill();
        });
      },

      updateAllCharts() {
        // Recalculate global time range
        this.calculateGlobalTimeRange();
        
        // Update all charts
        Object.keys(State.data).forEach(key => {
          const index = key.replace('speed', '');
          const canvas = DOM.elements[`chart${index}`];
          const data = State.data[key];
          if (canvas && data) {
            this.drawChart(canvas, data, true);
          }
        });
      }
    };

    // ============================
    // Display Module
    // ============================
    const Display = {
      updateSpeedBlock(index, data) {
        const block = DOM.elements[`block${index}`];
        if (!block) return;
  
        if (!data) {
          block.style.display = 'none';
          return;
        }
  
        block.style.display = 'flex';
  
        DOM.elements[`status${index}`].textContent = data.isValid ? 'OK' : 'NO OK';
        DOM.elements[`status${index}`].className = `status-badge ${data.isValid ? 'ok' : 'nok'}`;
        DOM.elements[`download${index}`].textContent = `${Math.round(data.lastDownload)} Mbps`;  // Número inteiro
        DOM.elements[`upload${index}`].textContent = `${Math.round(data.lastUpload)} Mbps`;      // Número inteiro
        DOM.elements[`ping${index}`].textContent = `${Math.round(data.ping)} ms`;
        DOM.elements[`time${index}`].innerHTML = TimeUtils.formatTimestamp(data.timestamp);
        DOM.elements[`server${index}`].textContent = data.server;
  
        ChartManager.updateAllCharts();
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

    // ============================
    // Variable Manager Module
    // ============================
    const VariableManager = {
      subscribeVariable(variable, index) {
        if (!variable) return;
        
        DOM.createSpeedBlock(index);
        
        if (variable.value) {
          const data = DataParser.parse(variable.value);
          State.data[`speed${index}`] = data;
          Display.updateSpeedBlock(index, data);
        }
        
        const sub = variable.onValue((value) => {
          const data = DataParser.parse(value);
          State.data[`speed${index}`] = data;
          Display.updateSpeedBlock(index, data);
        });
        
        State.subscriptions.push(sub);
      },

      setup() {
        let activeChartCount = 0;
    
        // Dynamically create blocks only for configured variables
        ['speedVar1', 'speedVar2', 'speedVar3'].forEach((varName, idx) => {
          const index = idx + 1;
          if (Config.variables[varName]) {
            this.subscribeVariable(Config.variables[varName], index);
            activeChartCount++;
          }
        });
    
        // Set data attribute for CSS to use
        if (activeChartCount > 0) {
          DOM.container.setAttribute('data-chart-count', activeChartCount);
        }
      },

      cleanup() {
        // Unsubscribe all subscriptions
        State.subscriptions.forEach(sub => {
          if (sub && typeof sub.unsubscribe === 'function') {
            sub.unsubscribe();
          }
        });
        State.subscriptions = [];
      }
    };
    
    // ============================
    // Application Controller
    // ============================
    const App = {
      init() {
        DOM.init();
        DynamicSizing.init();
        
        if (typeof stio === 'undefined') {
          setTimeout(() => this.init(), 200);
          return;
        }
        
        stio.ready((data) => {
          // Clean up any previous instances
          this.cleanup();
          
          Config.variables = {
            speedVar1: data.settings.speedVar1,
            speedVar2: data.settings.speedVar2,
            speedVar3: data.settings.speedVar3
          };
          
          Config.display.useGradient = data.settings.useGradient !== false;
          Config.display.tileHasLabel = data.settings.tileHasLabel === true;

          Display.applyTheme();
          VariableManager.setup();
          Display.showMain();
          
          setTimeout(() => {
            DynamicSizing.updateScale();
          }, 250);
        });
      },

      cleanup() {
        VariableManager.cleanup();
        DynamicSizing.cleanup();
        DOM.cleanup();
        State.data = {};
        State.charts = {};
        State.globalTimeRange = null;
      }
    };

    // ============================
    // Initialize Application
    // ============================
    if (document.readyState === 'loading') {
      document.addEventListener('DOMContentLoaded', () => App.init());
    } else {
      App.init();
    }

  })();
  </script>
</body>
</html>