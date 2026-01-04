<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, viewport-fit=cover">
    <title>Halal Life Scanner - Universal Product Scanner</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <link rel="manifest" href="manifest.json">
    <style>
        :root {
            --halal-green: #1e7b1e;
            --halal-light: #e8f5e9;
            --non-halal-red: #d32f2f;
            --non-halal-light: #ffebee;
            --warning-yellow: #f57c00;
            --warning-light: #fff3e0;
            --primary-bg: #f5f9ff;
            --card-bg: #ffffff;
            --text-dark: #333333;
            --text-light: #666666;
            --shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
        }
        
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }
        
        body {
            background-color: var(--primary-bg);
            color: var(--text-dark);
            line-height: 1.6;
            height: 100vh;
            display: flex;
            flex-direction: column;
            overflow: hidden;
        }
        
        /* App Header */
        .app-header {
            background: linear-gradient(135deg, var(--halal-green) 0%, #0f5a0f 100%);
            color: white;
            padding: 15px 20px;
            display: flex;
            align-items: center;
            justify-content: space-between;
            box-shadow: 0 3px 10px rgba(0, 0, 0, 0.2);
            z-index: 100;
            position: relative;
        }
        
        .app-title {
            display: flex;
            align-items: center;
            gap: 12px;
        }
        
        .app-title h1 {
            font-size: 1.3rem;
            font-weight: 600;
        }
        
        .app-title i {
            font-size: 1.8rem;
        }
        
        .app-subtitle {
            font-size: 0.8rem;
            opacity: 0.9;
            margin-top: 2px;
        }
        
        .app-actions {
            display: flex;
            gap: 10px;
        }
        
        .app-btn {
            background: rgba(255, 255, 255, 0.2);
            border: none;
            color: white;
            width: 40px;
            height: 40px;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            transition: all 0.3s ease;
        }
        
        /* Main Container */
        .app-container {
            flex: 1;
            display: flex;
            flex-direction: column;
            overflow: hidden;
        }
        
        /* Scanner Container */
        .scanner-container {
            flex: 1;
            display: flex;
            flex-direction: column;
            position: relative;
        }
        
        .camera-view {
            flex: 1;
            background-color: #000;
            position: relative;
            overflow: hidden;
        }
        
        #camera-feed {
            width: 100%;
            height: 100%;
            object-fit: cover;
            display: none;
            transform: scaleX(-1); /* Mirror front camera */
        }
        
        .camera-placeholder {
            width: 100%;
            height: 100%;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            color: #aaa;
            text-align: center;
            padding: 20px;
            background-color: #111;
        }
        
        .camera-placeholder i {
            font-size: 4rem;
            margin-bottom: 15px;
            color: var(--halal-green);
        }
        
        .camera-overlay {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            pointer-events: none;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
        }
        
        .scan-frame {
            width: 80%;
            max-width: 400px;
            height: 200px;
            border: 3px solid var(--halal-green);
            border-radius: 12px;
            position: relative;
            box-shadow: 0 0 0 100vmax rgba(0, 0, 0, 0.5);
        }
        
        .scan-frame::before, .scan-frame::after {
            content: '';
            position: absolute;
            width: 30px;
            height: 30px;
            border: 3px solid var(--halal-green);
        }
        
        .scan-frame::before {
            top: -3px;
            left: -3px;
            border-right: none;
            border-bottom: none;
        }
        
        .scan-frame::after {
            bottom: -3px;
            right: -3px;
            border-left: none;
            border-top: none;
        }
        
        .scan-guide {
            position: absolute;
            bottom: 20px;
            left: 0;
            width: 100%;
            text-align: center;
            color: white;
            font-size: 0.9rem;
            text-shadow: 0 2px 4px rgba(0,0,0,0.5);
            padding: 0 20px;
        }
        
        .scan-animation {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 3px;
            background: linear-gradient(90deg, transparent, var(--halal-green), transparent);
            animation: scanning 2s linear infinite;
            display: none;
        }
        
        @keyframes scanning {
            0% { top: 0; }
            50% { top: 100%; }
            100% { top: 0; }
        }
        
        /* Camera Controls */
        .camera-controls {
            position: absolute;
            bottom: 20px;
            left: 0;
            width: 100%;
            display: flex;
            justify-content: center;
            gap: 15px;
            z-index: 10;
        }
        
        .control-btn {
            width: 60px;
            height: 60px;
            border-radius: 50%;
            border: none;
            background: rgba(255, 255, 255, 0.2);
            backdrop-filter: blur(10px);
            color: white;
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            transition: all 0.3s ease;
            font-size: 1.5rem;
        }
        
        .control-btn.primary {
            background: var(--halal-green);
            width: 70px;
            height: 70px;
        }
        
        .control-btn:hover {
            transform: scale(1.1);
            background: rgba(255, 255, 255, 0.3);
        }
        
        .control-btn.primary:hover {
            background: #0f5a0f;
        }
        
        /* Scanner Results Panel */
        .results-panel {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: white;
            transform: translateY(100%);
            transition: transform 0.4s ease;
            z-index: 1000;
            display: flex;
            flex-direction: column;
            overflow: hidden;
        }
        
        .results-panel.active {
            transform: translateY(0);
        }
        
        .results-header {
            padding: 20px;
            background: linear-gradient(135deg, var(--halal-green) 0%, #0f5a0f 100%);
            color: white;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }
        
        .results-content {
            flex: 1;
            overflow-y: auto;
            padding: 20px;
        }
        
        /* Product Result Card */
        .product-result {
            background: var(--card-bg);
            border-radius: 15px;
            padding: 20px;
            margin-bottom: 20px;
            box-shadow: var(--shadow);
        }
        
        .product-header {
            display: flex;
            align-items: center;
            gap: 15px;
            margin-bottom: 20px;
            padding-bottom: 15px;
            border-bottom: 1px solid #eee;
        }
        
        .product-icon {
            width: 60px;
            height: 60px;
            border-radius: 12px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 1.8rem;
            color: white;
        }
        
        .product-icon.halal {
            background: var(--halal-green);
        }
        
        .product-icon.non-halal {
            background: var(--non-halal-red);
        }
        
        .product-icon.warning {
            background: var(--warning-yellow);
        }
        
        .product-info {
            flex: 1;
        }
        
        .product-name {
            font-size: 1.4rem;
            font-weight: 700;
            margin-bottom: 5px;
        }
        
        .product-brand {
            color: var(--text-light);
            font-size: 0.95rem;
        }
        
        .status-badge {
            padding: 8px 16px;
            border-radius: 20px;
            font-weight: 600;
            font-size: 0.9rem;
        }
        
        .halal-badge {
            background: var(--halal-green);
            color: white;
        }
        
        .non-halal-badge {
            background: var(--non-halal-red);
            color: white;
        }
        
        .warning-badge {
            background: var(--warning-yellow);
            color: white;
        }
        
        .product-details {
            margin-top: 20px;
        }
        
        .detail-section {
            margin-bottom: 20px;
        }
        
        .detail-section h4 {
            font-size: 1.1rem;
            margin-bottom: 10px;
            color: var(--halal-green);
            display: flex;
            align-items: center;
            gap: 8px;
        }
        
        .ingredient-list {
            list-style: none;
        }
        
        .ingredient-list li {
            padding: 8px 0;
            border-bottom: 1px solid #f0f0f0;
            display: flex;
            align-items: center;
            gap: 10px;
        }
        
        .ingredient-list li:last-child {
            border-bottom: none;
        }
        
        .ingredient-status {
            width: 12px;
            height: 12px;
            border-radius: 50%;
        }
        
        .ingredient-status.halal {
            background: var(--halal-green);
        }
        
        .ingredient-status.non-halal {
            background: var(--non-halal-red);
        }
        
        .ingredient-status.warning {
            background: var(--warning-yellow);
        }
        
        .certification-badge {
            display: inline-flex;
            align-items: center;
            gap: 8px;
            background: var(--halal-light);
            padding: 8px 15px;
            border-radius: 20px;
            margin-right: 10px;
            margin-bottom: 10px;
        }
        
        /* Bottom Navigation */
        .bottom-nav {
            background: white;
            border-top: 1px solid #e0e0e0;
            padding: 10px 20px;
            display: flex;
            justify-content: space-around;
            box-shadow: 0 -2px 10px rgba(0,0,0,0.1);
        }
        
        .nav-btn {
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 5px;
            background: none;
            border: none;
            color: var(--text-light);
            padding: 10px;
            cursor: pointer;
            transition: all 0.3s ease;
            flex: 1;
            max-width: 100px;
        }
        
        .nav-btn.active {
            color: var(--halal-green);
        }
        
        .nav-btn i {
            font-size: 1.3rem;
        }
        
        .nav-btn span {
            font-size: 0.8rem;
            font-weight: 500;
        }
        
        /* Database Search */
        .search-container {
            padding: 20px;
            background: white;
        }
        
        .search-box {
            position: relative;
        }
        
        .search-input {
            width: 100%;
            padding: 15px 20px 15px 50px;
            border: 2px solid #e0e0e0;
            border-radius: 30px;
            font-size: 1rem;
            transition: all 0.3s ease;
        }
        
        .search-input:focus {
            outline: none;
            border-color: var(--halal-green);
        }
        
        .search-icon {
            position: absolute;
            left: 20px;
            top: 50%;
            transform: translateY(-50%);
            color: var(--text-light);
        }
        
        /* Product List */
        .product-list {
            flex: 1;
            overflow-y: auto;
            padding: 20px;
        }
        
        .product-item {
            background: var(--card-bg);
            border-radius: 12px;
            padding: 15px;
            margin-bottom: 15px;
            display: flex;
            align-items: center;
            gap: 15px;
            box-shadow: 0 2px 8px rgba(0,0,0,0.05);
            cursor: pointer;
            transition: all 0.3s ease;
        }
        
        .product-item:hover {
            transform: translateY(-3px);
            box-shadow: 0 5px 15px rgba(0,0,0,0.1);
        }
        
        /* History Page */
        .history-container {
            padding: 20px;
            flex: 1;
            overflow-y: auto;
        }
        
        .history-date {
            font-size: 0.9rem;
            color: var(--text-light);
            margin-bottom: 10px;
            padding-left: 10px;
        }
        
        /* Settings Page */
        .settings-container {
            padding: 20px;
            flex: 1;
            overflow-y: auto;
        }
        
        .settings-section {
            background: var(--card-bg);
            border-radius: 15px;
            padding: 20px;
            margin-bottom: 20px;
            box-shadow: var(--shadow);
        }
        
        .settings-section h3 {
            margin-bottom: 20px;
            color: var(--halal-green);
            display: flex;
            align-items: center;
            gap: 10px;
        }
        
        .setting-item {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 15px 0;
            border-bottom: 1px solid #f0f0f0;
        }
        
        .setting-item:last-child {
            border-bottom: none;
        }
        
        .toggle-switch {
            position: relative;
            width: 50px;
            height: 26px;
        }
        
        .toggle-switch input {
            opacity: 0;
            width: 0;
            height: 0;
        }
        
        .toggle-slider {
            position: absolute;
            cursor: pointer;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background: #ccc;
            transition: .4s;
            border-radius: 34px;
        }
        
        .toggle-slider:before {
            position: absolute;
            content: "";
            height: 18px;
            width: 18px;
            left: 4px;
            bottom: 4px;
            background: white;
            transition: .4s;
            border-radius: 50%;
        }
        
        input:checked + .toggle-slider {
            background: var(--halal-green);
        }
        
        input:checked + .toggle-slider:before {
            transform: translateX(24px);
        }
        
        /* Responsive Design */
        @media (max-width: 768px) {
            .app-title h1 {
                font-size: 1.1rem;
            }
            
            .app-subtitle {
                font-size: 0.7rem;
            }
            
            .scan-frame {
                width: 90%;
                height: 180px;
            }
            
            .control-btn {
                width: 50px;
                height: 50px;
                font-size: 1.3rem;
            }
            
            .control-btn.primary {
                width: 60px;
                height: 60px;
            }
        }
        
        @media (max-width: 480px) {
            .app-header {
                padding: 12px 15px;
            }
            
            .scan-frame {
                height: 150px;
            }
            
            .scan-guide {
                font-size: 0.8rem;
                bottom: 15px;
            }
            
            .camera-controls {
                bottom: 15px;
            }
        }
        
        /* Utility Classes */
        .hidden {
            display: none !important;
        }
        
        .page {
            display: none;
            flex: 1;
            flex-direction: column;
            overflow: hidden;
        }
        
        .page.active {
            display: flex;
        }
        
        .close-btn {
            background: none;
            border: none;
            color: white;
            font-size: 1.5rem;
            cursor: pointer;
            width: 40px;
            height: 40px;
            display: flex;
            align-items: center;
            justify-content: center;
            border-radius: 50%;
            transition: background 0.3s ease;
        }
        
        .close-btn:hover {
            background: rgba(255, 255, 255, 0.1);
        }
        
        /* Loading Spinner */
        .spinner {
            width: 40px;
            height: 40px;
            border: 4px solid #f3f3f3;
            border-top: 4px solid var(--halal-green);
            border-radius: 50%;
            animation: spin 1s linear infinite;
            margin: 20px auto;
        }
        
        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
    </style>
</head>
<body>
    <!-- App Header -->
    <header class="app-header">
        <div class="app-title">
            <i class="fas fa-qrcode"></i>
            <div>
                <h1>Halal Life Scanner</h1>
                <div class="app-subtitle">Universal Product Scanner with Lifetime Database</div>
            </div>
        </div>
        <div class="app-actions">
            <button class="app-btn" id="flash-btn" title="Toggle Flash">
                <i class="fas fa-bolt"></i>
            </button>
        </div>
    </header>
    
    <!-- Main Container -->
    <div class="app-container">
        <!-- Scanner Page -->
        <div id="scanner-page" class="page active">
            <div class="scanner-container">
                <div class="camera-view">
                    <div class="camera-placeholder" id="camera-placeholder">
                        <i class="fas fa-camera"></i>
                        <p>Camera feed will appear here when access is granted</p>
                        <p style="font-size: 0.9rem; margin-top: 10px;">Point camera at barcode or product</p>
                    </div>
                    <video id="camera-feed" autoplay playsinline></video>
                    <div class="camera-overlay">
                        <div class="scan-frame"></div>
                        <div class="scan-guide">
                            Position barcode within frame for automatic scanning
                        </div>
                    </div>
                    <div class="scan-animation" id="scan-animation"></div>
                </div>
                
                <div class="camera-controls">
                    <button class="control-btn" id="switch-camera" title="Switch Camera">
                        <i class="fas fa-camera-retro"></i>
                    </button>
                    <button class="control-btn primary" id="scan-btn" title="Scan Product">
                        <i class="fas fa-search"></i>
                    </button>
                    <button class="control-btn" id="toggle-torch" title="Toggle Flash">
                        <i class="fas fa-bolt"></i>
                    </button>
                </div>
            </div>
        </div>
        
        <!-- Database Page -->
        <div id="database-page" class="page">
            <div class="search-container">
                <div class="search-box">
                    <i class="fas fa-search search-icon"></i>
                    <input type="text" class="search-input" id="search-input" placeholder="Search products by name, brand, or barcode...">
                </div>
            </div>
            <div class="product-list" id="database-list">
                <!-- Products will be loaded here -->
            </div>
        </div>
        
        <!-- History Page -->
        <div id="history-page" class="page">
            <div class="history-container" id="history-list">
                <!-- History items will be loaded here -->
            </div>
        </div>
        
        <!-- Settings Page -->
        <div id="settings-page" class="page">
            <div class="settings-container">
                <div class="settings-section">
                    <h3><i class="fas fa-camera"></i> Scanner Settings</h3>
                    
                    <div class="setting-item">
                        <div>
                            <h4>Auto-scan Mode</h4>
                            <p>Automatically scan when barcode is detected</p>
                        </div>
                        <label class="toggle-switch">
                            <input type="checkbox" id="auto-scan" checked>
                            <span class="toggle-slider"></span>
                        </label>
                    </div>
                    
                    <div class="setting-item">
                        <div>
                            <h4>Vibration Feedback</h4>
                            <p>Vibrate when scan is successful</p>
                        </div>
                        <label class="toggle-switch">
                            <input type="checkbox" id="vibration" checked>
                            <span class="toggle-slider"></span>
                        </label>
                    </div>
                    
                    <div class="setting-item">
                        <div>
                            <h4>Sound Effects</h4>
                            <p>Play sounds when scanning products</p>
                        </div>
                        <label class="toggle-switch">
                            <input type="checkbox" id="sound-effects" checked>
                            <span class="toggle-slider"></span>
                        </label>
                    </div>
                </div>
                
                <div class="settings-section">
                    <h3><i class="fas fa-database"></i> Database Settings</h3>
                    
                    <div class="setting-item">
                        <div>
                            <h4>Auto-update Database</h4>
                            <p>Automatically update product database</p>
                        </div>
                        <label class="toggle-switch">
                            <input type="checkbox" id="auto-update" checked>
                            <span class="toggle-slider"></span>
                        </label>
                    </div>
                    
                    <div class="setting-item">
                        <div>
                            <h4>Offline Mode</h4>
                            <p>Work without internet connection</p>
                        </div>
                        <label class="toggle-switch">
                            <input type="checkbox" id="offline-mode" checked>
                            <span class="toggle-slider"></span>
                        </label>
                    </div>
                    
                    <button class="btn btn-primary" id="update-db-btn" style="width: 100%; margin-top: 10px; padding: 12px; background: var(--halal-green); color: white; border: none; border-radius: 10px; font-weight: 600; cursor: pointer;">
                        <i class="fas fa-sync"></i> Update Database Now
                    </button>
                </div>
                
                <div class="settings-section">
                    <h3><i class="fas fa-info-circle"></i> About</h3>
                    <div style="padding: 10px 0;">
                        <p><strong>Halal Life Scanner v2.0</strong></p>
                        <p style="margin-top: 10px; font-size: 0.9rem; color: var(--text-light);">
                            This app scans barcodes and checks products against a comprehensive halal database. 
                            It works offline and is regularly updated with new products.
                        </p>
                    </div>
                </div>
            </div>
        </div>
    </div>
    
    <!-- Results Panel -->
    <div class="results-panel" id="results-panel">
        <div class="results-header">
            <h2 style="font-size: 1.3rem;">Scan Result</h2>
            <button class="close-btn" id="close-results">
                <i class="fas fa-times"></i>
            </button>
        </div>
        <div class="results-content" id="results-content">
            <!-- Results will be displayed here -->
        </div>
    </div>
    
    <!-- Bottom Navigation -->
    <nav class="bottom-nav">
        <button class="nav-btn active" data-page="scanner">
            <i class="fas fa-camera"></i>
            <span>Scan</span>
        </button>
        <button class="nav-btn" data-page="database">
            <i class="fas fa-database"></i>
            <span>Database</span>
        </button>
        <button class="nav-btn" data-page="history">
            <i class="fas fa-history"></i>
            <span>History</span>
        </button>
        <button class="nav-btn" data-page="settings">
            <i class="fas fa-cog"></i>
            <span>Settings</span>
        </button>
    </nav>

    <!-- JavaScript -->
    <script>
        // Comprehensive Halal Product Database (in real app, this would be much larger)
        const HALAL_DATABASE = {
            // Food & Beverages
            "5901234567890": {
                name: "Cadbury Dairy Milk Chocolate",
                brand: "Cadbury",
                barcode: "5901234567890",
                status: "halal",
                category: "Confectionery",
                ingredients: ["Milk chocolate", "Sugar", "Cocoa butter", "Cocoa mass", "Vegetable fats", "Emulsifiers (soya lecithin, E476)", "Flavors"],
                certifications: ["JAKIM Halal Certified", "IFANCA Halal"],
                warning: [],
                halalIngredients: ["Milk chocolate", "Sugar", "Cocoa butter", "Cocoa mass", "Vegetable fats"],
                nonHalalIngredients: [],
                questionableIngredients: ["E476 (Polyglycerol polyricinoleate)"],
                notes: "Certified halal by JAKIM Malaysia"
            },
            
            "5909876543210": {
                name: "Hershey's Chocolate Bar",
                brand: "Hershey's",
                barcode: "5909876543210",
                status: "non-halal",
                category: "Confectionery",
                ingredients: ["Sugar", "Cocoa butter", "Chocolate", "Nonfat milk", "Milk fat", "Lactose", "Lecithin (soy)", "Natural flavor", "PGPR", "Alcohol"],
                certifications: [],
                warning: ["Contains alcohol-based flavoring"],
                halalIngredients: ["Sugar", "Cocoa butter", "Chocolate", "Nonfat milk", "Milk fat", "Lecithin (soy)"],
                nonHalalIngredients: ["Alcohol"],
                questionableIngredients: ["PGPR (Polyglycerol polyricinoleate)", "Natural flavor"],
                notes: "Contains alcohol-based flavoring which is non-halal"
            },
            
            "4014400905629": {
                name: "Nestlé Nescafé Classic",
                brand: "Nestlé",
                barcode: "4014400905629",
                status: "halal",
                category: "Beverages",
                ingredients: ["100% pure coffee"],
                certifications: ["JAKIM Halal Certified", "MUIS Halal"],
                warning: [],
                halalIngredients: ["Coffee"],
                nonHalalIngredients: [],
                questionableIngredients: [],
                notes: "Certified halal by multiple authorities"
            },
            
            "5901231231234": {
                name: "Kellogg's Corn Flakes",
                brand: "Kellogg's",
                barcode: "5901231231234",
                status: "halal",
                category: "Breakfast Cereal",
                ingredients: ["Corn", "Sugar", "Malt flavoring", "High fructose corn syrup", "Salt", "Vitamins and minerals"],
                certifications: ["IFANCA Halal Certified"],
                warning: [],
                halalIngredients: ["Corn", "Sugar", "Salt", "Vitamins and minerals"],
                nonHalalIngredients: [],
                questionableIngredients: ["Malt flavoring", "High fructose corn syrup"],
                notes: "Certified halal by IFANCA"
            },
            
            "5905555555555": {
                name: "Coca-Cola Original",
                brand: "Coca-Cola",
                barcode: "5905555555555",
                status: "halal",
                category: "Beverages",
                ingredients: ["Carbonated water", "Sugar", "Caramel color", "Phosphoric acid", "Natural flavors", "Caffeine"],
                certifications: ["Halal Food Authority"],
                warning: [],
                halalIngredients: ["Carbonated water", "Sugar", "Caramel color", "Phosphoric acid", "Caffeine"],
                nonHalalIngredients: [],
                questionableIngredients: ["Natural flavors"],
                notes: "Certified halal in most Muslim countries"
            },
            
            "5904444444444": {
                name: "Gelatin Dessert Mix",
                brand: "Royal",
                barcode: "5904444444444",
                status: "non-halal",
                category: "Dessert",
                ingredients: ["Sugar", "Pork gelatin", "Adipic acid", "Disodium phosphate", "Fumaric acid", "Artificial flavor", "Artificial color"],
                certifications: [],
                warning: ["Contains pork gelatin", "Non-halal animal derivative"],
                halalIngredients: ["Sugar"],
                nonHalalIngredients: ["Pork gelatin"],
                questionableIngredients: ["Artificial flavor", "Artificial color"],
                notes: "Contains pork-derived gelatin which is strictly non-halal"
            },
            
            "5903333333333": {
                name: "Beef Sausages",
                brand: "Farmhouse",
                barcode: "5903333333333",
                status: "warning",
                category: "Meat Products",
                ingredients: ["Beef", "Water", "Salt", "Spices", "Dextrose", "Flavorings", "Sodium nitrite", "Sodium erythorbate"],
                certifications: [],
                warning: ["Slaughter method not specified", "May contain non-halal additives"],
                halalIngredients: ["Beef", "Water", "Salt", "Spices"],
                nonHalalIngredients: [],
                questionableIngredients: ["Flavorings", "Sodium nitrite", "Sodium erythorbate"],
                notes: "Halal status uncertain - slaughter method not verified"
            },
            
            "5902222222222": {
                name: "Al-Marai Yogurt",
                brand: "Al-Marai",
                barcode: "5902222222222",
                status: "halal",
                category: "Dairy",
                ingredients: ["Milk", "Live yogurt cultures", "Vitamin D"],
                certifications: ["SASO Halal Certified", "GCC Standardization"],
                warning: [],
                halalIngredients: ["Milk", "Live yogurt cultures", "Vitamin D"],
                nonHalalIngredients: [],
                questionableIngredients: [],
                notes: "Produced in Saudi Arabia with halal certification"
            },
            
            "5901111111111": {
                name: "Maggi Chicken Noodles",
                brand: "Nestlé",
                barcode: "5901111111111",
                status: "halal",
                category: "Instant Food",
                ingredients: ["Wheat flour", "Palm oil", "Salt", "Chicken powder", "Monosodium glutamate", "Spices", "Sugar", "Onion powder", "Garlic powder"],
                certifications: ["JAKIM Halal Certified"],
                warning: [],
                halalIngredients: ["Wheat flour", "Palm oil", "Salt", "Spices", "Sugar", "Onion powder", "Garlic powder"],
                nonHalalIngredients: [],
                questionableIngredients: ["Chicken powder", "Monosodium glutamate"],
                notes: "Certified halal by JAKIM Malaysia"
            },
            
            "5906666666666": {
                name: "Pepsi Cola",
                brand: "PepsiCo",
                barcode: "5906666666666",
                status: "halal",
                category: "Beverages",
                ingredients: ["Carbonated water", "High fructose corn syrup", "Caramel color", "Phosphoric acid", "Caffeine", "Citric acid", "Natural flavors"],
                certifications: ["Halal Monitoring Committee"],
                warning: [],
                halalIngredients: ["Carbonated water", "Caramel color", "Phosphoric acid", "Caffeine", "Citric acid"],
                nonHalalIngredients: [],
                questionableIngredients: ["High fructose corn syrup", "Natural flavors"],
                notes: "Generally considered halal worldwide"
            }
        };

        // Common barcodes for testing (you can scan these with camera)
        const TEST_BARCODES = [
            "5901234567890", "5909876543210", "4014400905629", 
            "5901231231234", "5905555555555", "5904444444444",
            "5903333333333", "5902222222222", "5901111111111"
        ];

        // App State
        const AppState = {
            currentPage: 'scanner',
            cameraActive: false,
            currentCamera: 'environment', // 'environment' for back, 'user' for front
            torchActive: false,
            scanHistory: [],
            settings: {
                autoScan: true,
                vibration: true,
                soundEffects: true,
                autoUpdate: true,
                offlineMode: true
            },
            lastScanned: null
        };

        // DOM Elements
        const cameraFeed = document.getElementById('camera-feed');
        const cameraPlaceholder = document.getElementById('camera-placeholder');
        const scanBtn = document.getElementById('scan-btn');
        const switchCameraBtn = document.getElementById('switch-camera');
        const torchBtn = document.getElementById('toggle-torch');
        const flashBtn = document.getElementById('flash-btn');
        const scanAnimation = document.getElementById('scan-animation');
        const resultsPanel = document.getElementById('results-panel');
        const resultsContent = document.getElementById('results-content');
        const closeResultsBtn = document.getElementById('close-results');
        const navBtns = document.querySelectorAll('.nav-btn');
        const pages = document.querySelectorAll('.page');
        const databaseList = document.getElementById('database-list');
        const historyList = document.getElementById('history-list');
        const searchInput = document.getElementById('search-input');
        const updateDbBtn = document.getElementById('update-db-btn');

        let cameraStream = null;
        let scanInterval = null;

        // Initialize App
        function initApp() {
            loadSettings();
            loadScanHistory();
            setupEventListeners();
            renderDatabase();
            renderHistory();
            
            // Initialize camera if on scanner page
            if (AppState.currentPage === 'scanner') {
                setTimeout(() => initCamera(), 500);
            }
            
            // Check for PWA installation
            if ('serviceWorker' in navigator) {
                navigator.serviceWorker.register('/sw.js').catch(console.error);
            }
        }

        // Load settings from localStorage
        function loadSettings() {
            const saved = localStorage.getItem('halalScannerSettings');
            if (saved) {
                AppState.settings = JSON.parse(saved);
                updateSettingsUI();
            }
        }

        // Save settings to localStorage
        function saveSettings() {
            localStorage.setItem('halalScannerSettings', JSON.stringify(AppState.settings));
        }

        // Update settings UI
        function updateSettingsUI() {
            document.getElementById('auto-scan').checked = AppState.settings.autoScan;
            document.getElementById('vibration').checked = AppState.settings.vibration;
            document.getElementById('sound-effects').checked = AppState.settings.soundEffects;
            document.getElementById('auto-update').checked = AppState.settings.autoUpdate;
            document.getElementById('offline-mode').checked = AppState.settings.offlineMode;
        }

        // Setup event listeners
        function setupEventListeners() {
            // Navigation
            navBtns.forEach(btn => {
                btn.addEventListener('click', () => {
                    const page = btn.dataset.page;
                    switchPage(page);
                    navBtns.forEach(b => b.classList.remove('active'));
                    btn.classList.add('active');
                });
            });

            // Scanner controls
            scanBtn.addEventListener('click', scanProduct);
            switchCameraBtn.addEventListener('click', switchCamera);
            torchBtn.addEventListener('click', toggleTorch);
            flashBtn.addEventListener('click', toggleTorch);
            closeResultsBtn.addEventListener('click', closeResults);

            // Settings toggles
            document.getElementById('auto-scan').addEventListener('change', (e) => {
                AppState.settings.autoScan = e.target.checked;
                saveSettings();
            });

            document.getElementById('vibration').addEventListener('change', (e) => {
                AppState.settings.vibration = e.target.checked;
                saveSettings();
            });

            document.getElementById('sound-effects').addEventListener('change', (e) => {
                AppState.settings.soundEffects = e.target.checked;
                saveSettings();
            });

            document.getElementById('auto-update').addEventListener('change', (e) => {
                AppState.settings.autoUpdate = e.target.checked;
                saveSettings();
            });

            document.getElementById('offline-mode').addEventListener('change', (e) => {
                AppState.settings.offlineMode = e.target.checked;
                saveSettings();
            });

            // Update database button
            updateDbBtn.addEventListener('click', updateDatabase);

            // Search input
            searchInput.addEventListener('input', filterDatabase);

            // Handle back button on results panel
            document.addEventListener('keydown', (e) => {
                if (e.key === 'Escape' && resultsPanel.classList.contains('active')) {
                    closeResults();
                }
            });

            // Auto-scan simulation
            if (AppState.settings.autoScan) {
                startAutoScan();
            }
        }

        // Initialize camera
        async function initCamera() {
            try {
                cameraStream = await navigator.mediaDevices.getUserMedia({
                    video: {
                        facingMode: AppState.currentCamera,
                        width: { ideal: 1280 },
                        height: { ideal: 720 }
                    }
                });

                cameraFeed.srcObject = cameraStream;
                cameraFeed.style.display = 'block';
                cameraPlaceholder.style.display = 'none';
                
                // Adjust video transform based on camera type
                cameraFeed.style.transform = AppState.currentCamera === 'user' ? 'scaleX(-1)' : 'scaleX(1)';
                
                console.log("Camera initialized successfully");
            } catch (error) {
                console.error("Camera error:", error);
                cameraPlaceholder.innerHTML = `
                    <i class="fas fa-camera-slash"></i>
                    <p>Camera access denied or not available</p>
                    <p style="font-size: 0.9rem; margin-top: 10px;">Please allow camera access to use the scanner</p>
                `;
            }
        }

        // Switch between front and back camera
        async function switchCamera() {
            if (!cameraStream) return;
            
            // Stop current stream
            cameraStream.getTracks().forEach(track => track.stop());
            
            // Switch camera type
            AppState.currentCamera = AppState.currentCamera === 'environment' ? 'user' : 'environment';
            
            // Reinitialize camera
            await initCamera();
            
            // Update button icon
            const icon = switchCameraBtn.querySelector('i');
            icon.className = AppState.currentCamera === 'environment' ? 'fas fa-camera-retro' : 'fas fa-mobile-alt';
        }

        // Toggle torch/flash
        async function toggleTorch() {
            if (!cameraStream) return;
            
            try {
                const track = cameraStream.getVideoTracks()[0];
                if (typeof track.getCapabilities === 'function') {
                    const capabilities = track.getCapabilities();
                    if (capabilities.torch) {
                        await track.applyConstraints({
                            advanced: [{ torch: !AppState.torchActive }]
                        });
                        AppState.torchActive = !AppState.torchActive;
                        
                        // Update button icon
                        const icon = torchBtn.querySelector('i');
                        icon.style.color = AppState.torchActive ? '#FFD700' : 'white';
                    } else {
                        alert("Flash/torch not available on this device");
                    }
                }
            } catch (error) {
                console.error("Torch error:", error);
            }
        }

        // Start auto-scan simulation
        function startAutoScan() {
            if (scanInterval) clearInterval(scanInterval);
            
            scanInterval = setInterval(() => {
                if (AppState.currentPage === 'scanner' && cameraStream) {
                    // Simulate barcode detection (in real app, use Quagga.js or similar)
                    if (Math.random() > 0.7) {
                        simulateBarcodeDetection();
                    }
                }
            }, 3000);
        }

        // Simulate barcode detection
        function simulateBarcodeDetection() {
            const randomBarcode = TEST_BARCODES[Math.floor(Math.random() * TEST_BARCODES.length)];
            scanProduct(randomBarcode);
        }

        // Scan product
        function scanProduct(barcode = null) {
            // Show scanning animation
            scanAnimation.style.display = 'block';
            scanBtn.disabled = true;
            
            // Simulate scanning process
            setTimeout(() => {
                scanAnimation.style.display = 'none';
                scanBtn.disabled = false;
                
                // Use provided barcode or generate random one
                const scannedBarcode = barcode || TEST_BARCODES[Math.floor(Math.random() * TEST_BARCODES.length)];
                
                // Find product in database
                const product = HALAL_DATABASE[scannedBarcode] || createUnknownProduct(scannedBarcode);
                
                // Add to history
                addToHistory(product);
                
                // Show results
                showResults(product);
                
                // Provide haptic feedback if enabled
                if (AppState.settings.vibration && navigator.vibrate) {
                    navigator.vibrate(200);
                }
                
                // Play sound if enabled
                if (AppState.settings.soundEffects) {
                    playScanSound(product.status);
                }
                
            }, 1500);
        }

        // Create unknown product entry
        function createUnknownProduct(barcode) {
            return {
                name: "Unknown Product",
                brand: "Unknown Brand",
                barcode: barcode,
                status: "warning",
                category: "Unknown",
                ingredients: ["Unknown ingredients"],
                certifications: [],
                warning: ["Product not found in database"],
                halalIngredients: [],
                nonHalalIngredients: [],
                questionableIngredients: [],
                notes: "This product is not in our database. Please verify halal status manually."
            };
        }

        // Play scan sound
        function playScanSound(status) {
            // In real app, play actual audio files
            console.log(`Playing ${status} sound`);
        }

        // Show results panel
        function showResults(product) {
            AppState.lastScanned = product;
            
            // Render product details
            resultsContent.innerHTML = renderProductDetails(product);
            
            // Show panel
            resultsPanel.classList.add('active');
            
            // Update history if on history page
            if (AppState.currentPage === 'history') {
                renderHistory();
            }
        }

        // Render product details
        function renderProductDetails(product) {
            const statusIcon = product.status === 'halal' ? 'fa-check-circle' : 
                              product.status === 'non-halal' ? 'fa-times-circle' : 'fa-exclamation-triangle';
            
            const statusColor = product.status === 'halal' ? 'halal' : 
                               product.status === 'non-halal' ? 'non-halal' : 'warning';
            
            return `
                <div class="product-result">
                    <div class="product-header">
                        <div class="product-icon ${statusColor}">
                            <i class="fas ${statusIcon}"></i>
                        </div>
                        <div class="product-info">
                            <div class="product-name">${product.name}</div>
                            <div class="product-brand">${product.brand} • ${product.category}</div>
                        </div>
                        <div class="status-badge ${statusColor}-badge">
                            ${product.status.toUpperCase()}
                        </div>
                    </div>
                    
                    <div class="product-details">
                        <div class="detail-section">
                            <h4><i class="fas fa-barcode"></i> Barcode</h4>
                            <p>${product.barcode}</p>
                        </div>
                        
                        <div class="detail-section">
                            <h4><i class="fas fa-certificate"></i> Certifications</h4>
                            ${product.certifications.length > 0 ? 
                                product.certifications.map(cert => 
                                    `<div class="certification-badge">
                                        <i class="fas fa-check"></i> ${cert}
                                    </div>`
                                ).join('') : 
                                '<p>No halal certification found</p>'
                            }
                        </div>
                        
                        <div class="detail-section">
                            <h4><i class="fas fa-list-ol"></i> Ingredients Analysis</h4>
                            <ul class="ingredient-list">
                                ${renderIngredientsList(product)}
                            </ul>
                        </div>
                        
                        ${product.warning.length > 0 ? `
                            <div class="detail-section">
                                <h4><i class="fas fa-exclamation-triangle" style="color: var(--warning-yellow);"></i> Warnings</h4>
                                <ul style="color: var(--warning-yellow); padding-left: 20px;">
                                    ${product.warning.map(w => `<li>${w}</li>`).join('')}
                                </ul>
                            </div>
                        ` : ''}
                        
                        <div class="detail-section">
                            <h4><i class="fas fa-sticky-note"></i> Notes</h4>
                            <p>${product.notes}</p>
                        </div>
                    </div>
                </div>
            `;
        }

        // Render ingredients list
        function renderIngredientsList(product) {
            let html = '';
            
            // Halal ingredients
            product.halalIngredients.forEach(ing => {
                html += `<li>
                    <div class="ingredient-status halal"></div>
                    <span>${ing} <small style="color: var(--halal-green);">(Halal)</small></span>
                </li>`;
            });
            
            // Questionable ingredients
            product.questionableIngredients.forEach(ing => {
                html += `<li>
                    <div class="ingredient-status warning"></div>
                    <span>${ing} <small style="color: var(--warning-yellow);">(Needs verification)</small></span>
                </li>`;
            });
            
            // Non-halal ingredients
            product.nonHalalIngredients.forEach(ing => {
                html += `<li>
                    <div class="ingredient-status non-halal"></div>
                    <span>${ing} <small style="color: var(--non-halal-red);">(Non-halal)</small></span>
                </li>`;
            });
            
            return html || '<li>No ingredient information available</li>';
        }

        // Close results panel
        function closeResults() {
            resultsPanel.classList.remove('active');
        }

        // Add product to scan history
        function addToHistory(product) {
            const historyItem = {
                id: Date.now(),
                product: product,
                timestamp: new Date().toISOString(),
                date: new Date().toLocaleDateString(),
                time: new Date().toLocaleTimeString()
            };
            
            AppState.scanHistory.unshift(historyItem);
            
            // Keep only last 100 items
            if (AppState.scanHistory.length > 100) {
                AppState.scanHistory.pop();
            }
            
            // Save to localStorage
            localStorage.setItem('halalScannerHistory', JSON.stringify(AppState.scanHistory));
        }

        // Load scan history from localStorage
        function loadScanHistory() {
            const saved = localStorage.getItem('halalScannerHistory');
            if (saved) {
                AppState.scanHistory = JSON.parse(saved);
            }
        }

        // Switch between pages
        function switchPage(pageName) {
            // Stop camera if leaving scanner page
            if (AppState.currentPage === 'scanner' && pageName !== 'scanner') {
                stopCamera();
            }
            
            // Start camera if switching to scanner page
            if (pageName === 'scanner' && AppState.currentPage !== 'scanner') {
                setTimeout(() => initCamera(), 100);
            }
            
            // Update current page
            AppState.currentPage = pageName;
            
            // Hide all pages
            pages.forEach(page => page.classList.remove('active'));
            
            // Show selected page
            document.getElementById(`${pageName}-page`).classList.add('active');
            
            // Update content based on page
            switch(pageName) {
                case 'database':
                    renderDatabase();
                    break;
                case 'history':
                    renderHistory();
                    break;
            }
        }

        // Stop camera
        function stopCamera() {
            if (cameraStream) {
                cameraStream.getTracks().forEach(track => track.stop());
                cameraStream = null;
            }
            cameraFeed.style.display = 'none';
            cameraPlaceholder.style.display = 'block';
        }

        // Render database
        function renderDatabase(filter = '') {
            const products = Object.values(HALAL_DATABASE);
            let filteredProducts = products;
            
            if (filter) {
                const filterLower = filter.toLowerCase();
                filteredProducts = products.filter(p => 
                    p.name.toLowerCase().includes(filterLower) ||
                    p.brand.toLowerCase().includes(filterLower) ||
                    p.barcode.includes(filter)
                );
            }
            
            databaseList.innerHTML = filteredProducts.map(product => `
                <div class="product-item" onclick="showProductDetails('${product.barcode}')">
                    <div class="product-icon ${product.status}">
                        <i class="fas ${product.status === 'halal' ? 'fa-check' : 
                                      product.status === 'non-halal' ? 'fa-times' : 'fa-exclamation'}"></i>
                    </div>
                    <div class="product-info">
                        <h3>${product.name}</h3>
                        <p>${product.brand} • ${product.barcode}</p>
                        <p>Status: <strong>${product.status === 'halal' ? 'Halal Certified' : 
                                           product.status === 'non-halal' ? 'Non-Halal' : 'Uncertain'}</strong></p>
                    </div>
                    <div>
                        <i class="fas fa-chevron-right"></i>
                    </div>
                </div>
            `).join('');
            
            if (filteredProducts.length === 0) {
                databaseList.innerHTML = `
                    <div style="text-align: center; padding: 40px; color: var(--text-light);">
                        <i class="fas fa-search" style="font-size: 3rem; margin-bottom: 15px;"></i>
                        <p>No products found matching your search</p>
                    </div>
                `;
            }
        }

        // Filter database
        function filterDatabase() {
            renderDatabase(searchInput.value);
        }

        // Show product details from database
        function showProductDetails(barcode) {
            const product = HALAL_DATABASE[barcode];
            if (product) {
                showResults(product);
            }
        }

        // Render history
        function renderHistory() {
            if (AppState.scanHistory.length === 0) {
                historyList.innerHTML = `
                    <div style="text-align: center; padding: 40px; color: var(--text-light);">
                        <i class="fas fa-history" style="font-size: 3rem; margin-bottom: 15px;"></i>
                        <p>No scan history yet</p>
                        <p style="font-size: 0.9rem; margin-top: 10px;">Start scanning products to build your history</p>
                    </div>
                `;
                return;
            }
            
            // Group by date
            const grouped = {};
            AppState.scanHistory.forEach(item => {
                if (!grouped[item.date]) {
                    grouped[item.date] = [];
                }
                grouped[item.date].push(item);
            });
            
            historyList.innerHTML = Object.entries(grouped).map(([date, items]) => `
                <div class="history-date">${date}</div>
                ${items.map(item => `
                    <div class="product-item" onclick="showProductDetails('${item.product.barcode}')">
                        <div class="product-icon ${item.product.status}">
                            <i class="fas ${item.product.status === 'halal' ? 'fa-check' : 
                                          item.product.status === 'non-halal' ? 'fa-times' : 'fa-exclamation'}"></i>
                        </div>
                        <div class="product-info">
                            <h3>${item.product.name}</h3>
                            <p>${item.time} • ${item.product.brand}</p>
                            <p>Status: <strong>${item.product.status === 'halal' ? 'Halal' : 
                                               item.product.status === 'non-halal' ? 'Non-Halal' : 'Uncertain'}</strong></p>
                        </div>
                    </div>
                `).join('')}
            `).join('');
        }

        // Update database
        function updateDatabase() {
            updateDbBtn.innerHTML = '<i class="fas fa-spinner fa-spin"></i> Updating...';
            updateDbBtn.disabled = true;
            
            setTimeout(() => {
                // In real app, this would fetch from API
                alert('Database updated successfully! Added 50 new products.');
                updateDbBtn.innerHTML = '<i class="fas fa-sync"></i> Update Database Now';
                updateDbBtn.disabled = false;
            }, 2000);
        }

        // Initialize when page loads
        document.addEventListener('DOMContentLoaded', initApp);

        // Make functions available globally for onclick handlers
        window.showProductDetails = showProductDetails;
    </script>
</body>
</html>
