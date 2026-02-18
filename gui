// ==UserScript==
// @name         Michael's Kahoot Cheats
// @version      10.3
// @description  Ultimate undetectable Kahoot cheats with Quiz Browser
// @namespace    https://
// @match        https://kahoot.it/*
// @grant        none
// ==/UserScript==

(function() {
    'use strict';
    
    // === STEALTH CONSOLE - ABSOLUTELY NO CLEARING, JUST FILTERING ===
    (function stealthConsole() {
        if (!window.console) return;
        
        const originalLog = console.log;
        const originalError = console.error;
        const originalWarn = console.warn;
        const originalInfo = console.info;
        const originalDebug = console.debug;
        
        const filterKeywords = [
            'michaels', 'kahoot', 'marker', 'revealer', 'locker', 
            'password', 'unlock', 'authenticated', 'config', 
            'localstorage', 'cheat', 'hack', '5189', 'michael',
            'highlight', 'answer', 'toggle', 'switch', 'knob',
            'dropdown', 'settings', 'panel', 'ui', 'initialize',
            'fetch', 'quiz', 'search', 'results', 'dropdown',
            'monitoring', 'interval', 'keyboard', 'handler',
            'apply', 'remove', 'show', 'hide', 'enable', 'disable'
        ];
        
        const shouldFilter = (args) => {
            try {
                const argsStr = JSON.stringify(args).toLowerCase();
                return filterKeywords.some(keyword => argsStr.includes(keyword.toLowerCase()));
            } catch {
                return false;
            }
        };
        
        console.log = function(...args) { 
            if (!shouldFilter(args)) originalLog.apply(console, args); 
        };
        console.error = function(...args) { 
            if (!shouldFilter(args)) originalError.apply(console, args); 
        };
        console.warn = function(...args) { 
            if (!shouldFilter(args)) originalWarn.apply(console, args); 
        };
        console.info = function(...args) { 
            if (!shouldFilter(args)) originalInfo.apply(console, args); 
        };
        console.debug = function(...args) { 
            if (!shouldFilter(args)) originalDebug.apply(console, args); 
        };
    })();
    
    // === PASSWORD CONFIGURATION ===
    const PASSWORD = '5189';
    let isAuthenticated = false;
    
    // === DEFAULT CONFIGURATION ===
    const DEFAULTS = {
        highlightMode: 'legit',
        revealerMode: 'legit',
        lockerEnabled: false,
        markerColor: '#000000',
        markerSize: '8px',
        detectionInterval: 100,
        apiEndpoint: 'https://damp-leaf-16aa.johnwee.workers.dev/api-proxy/',
        searchEndpoint: 'https://damp-leaf-16aa.johnwee.workers.dev/rest/kahoots/'
    };
    
    // === LOAD CONFIG ===
    let CONFIG = { ...DEFAULTS };
    try {
        const saved = localStorage.getItem('michaelsKahootConfig');
        if (saved) {
            CONFIG = { ...DEFAULTS, ...JSON.parse(saved) };
        }
    } catch (e) {}
    
    // === CORNER MAPPING ===
    const CORNER_MAP = {
        0: 'bottom-right',
        1: 'bottom-left',
        2: 'top-right',
        3: 'top-left'
    };
    
    // === STATE MANAGEMENT ===
    let state = {
        visible: true,
        questions: [],
        questionsData: [], // Store full question data including text and choices
        currentQuestion: -1,
        gamePin: null,
        quizLoaded: false,
        markersEnabled: false,
        revealerEnabled: false,
        lockerEnabled: CONFIG.lockerEnabled,
        highlightMode: CONFIG.highlightMode,
        revealerMode: CONFIG.revealerMode,
        active: true,
        currentQuizId: null,
        currentQuizTitle: '',
        isSearching: false,
        settingsOpen: false,
        quizBrowserOpen: false,
        ui: null,
        highlightSwitch: null,
        highlightKnob: null,
        revealerSwitch: null,
        revealerKnob: null,
        lockerSwitch: null,
        lockerKnob: null,
        highlightModeIndicator: null,
        revealerModeIndicator: null,
        questionDisplay: null,
        pinDisplay: null,
        searchInput: null,
        resultsDropdown: null,
        highlightDropdown: null,
        revealerDropdown: null,
        highlightArrow: null,
        revealerArrow: null,
        quizBrowserContainer: null,
        quizBrowserArrow: null
    };
    
    // === TINY PASSWORD OVERLAY ===
    const overlay = document.createElement('div');
    Object.assign(overlay.style, {
        position: 'fixed',
        top: '50%',
        left: '50%',
        transform: 'translate(-50%, -50%)',
        backgroundColor: '#0f0f13',
        padding: '20px',
        borderRadius: '8px',
        border: '1px solid #2a2a35',
        width: '220px',
        zIndex: '100000',
        fontFamily: '"Segoe UI", "Roboto", sans-serif',
        boxShadow: '0 10px 30px rgba(0,0,0,0.5)'
    });
    
    const lockIcon = document.createElement('div');
    lockIcon.innerHTML = '🔒';
    lockIcon.style.fontSize = '24px';
    lockIcon.style.textAlign = 'center';
    lockIcon.style.marginBottom = '10px';
    
    const passwordTitle = document.createElement('div');
    passwordTitle.textContent = 'ENTER PASSWORD';
    Object.assign(passwordTitle.style, {
        color: '#ffffff',
        fontSize: '12px',
        fontWeight: '600',
        letterSpacing: '1px',
        textAlign: 'center',
        marginBottom: '15px'
    });
    
    const passwordInput = document.createElement('input');
    passwordInput.type = 'password';
    passwordInput.maxLength = 4;
    passwordInput.placeholder = '****';
    Object.assign(passwordInput.style, {
        width: '100%',
        padding: '8px',
        background: '#1a1a24',
        border: '2px solid #2a2a35',
        borderRadius: '6px',
        color: '#ffffff',
        fontSize: '16px',
        textAlign: 'center',
        letterSpacing: '4px',
        outline: 'none',
        marginBottom: '10px',
        boxSizing: 'border-box'
    });
    
    const passwordError = document.createElement('div');
    passwordError.style.color = '#ff4757';
    passwordError.style.fontSize = '11px';
    passwordError.style.textAlign = 'center';
    passwordError.style.marginBottom = '5px';
    passwordError.style.display = 'none';
    
    const submitBtn = document.createElement('button');
    submitBtn.textContent = 'UNLOCK';
    Object.assign(submitBtn.style, {
        width: '100%',
        padding: '8px',
        background: '#6c5ce7',
        color: '#ffffff',
        border: 'none',
        borderRadius: '6px',
        fontSize: '12px',
        fontWeight: '600',
        cursor: 'pointer'
    });
    
    overlay.appendChild(lockIcon);
    overlay.appendChild(passwordTitle);
    overlay.appendChild(passwordInput);
    overlay.appendChild(passwordError);
    overlay.appendChild(submitBtn);
    document.body.appendChild(overlay);
    
    // === PASSWORD CHECK ===
    function checkPassword() {
        if (passwordInput.value === PASSWORD) {
            isAuthenticated = true;
            document.body.removeChild(overlay);
            initializeUI();
        } else {
            passwordError.textContent = 'Wrong password';
            passwordError.style.display = 'block';
            passwordInput.value = '';
            passwordInput.focus();
        }
    }
    
    passwordInput.addEventListener('keypress', (e) => {
        if (e.key === 'Enter') checkPassword();
    });
    
    passwordInput.addEventListener('input', () => {
        passwordError.style.display = 'none';
        if (passwordInput.value.length === 4) setTimeout(checkPassword, 100);
    });
    
    submitBtn.addEventListener('click', checkPassword);
    
    // === MAIN UI ===
    function initializeUI() {
        state.ui = document.createElement('div');
        Object.assign(state.ui.style, {
            position: 'fixed',
            top: '30px',
            left: '30px',
            width: '380px',
            backgroundColor: '#0f0f13',
            borderRadius: '12px',
            boxShadow: '0 10px 40px rgba(0, 0, 0, 0.9)',
            zIndex: '99999',
            fontFamily: '"Segoe UI", "Roboto", sans-serif',
            border: '1px solid #2a2a35',
            overflow: 'hidden'
        });
        
        // Drag Header
        const header = document.createElement('div');
        Object.assign(header.style, {
            padding: '16px 20px',
            background: 'linear-gradient(135deg, #2d2d3a 0%, #1a1a24 100%)',
            cursor: 'move',
            display: 'flex',
            alignItems: 'center',
            justifyContent: 'space-between',
            borderBottom: '1px solid #2a2a35',
            userSelect: 'none'
        });
        
        const title = document.createElement('div');
        title.innerHTML = '<span style="color:#6c5ce7;font-size:20px;margin-right:10px">😈</span><span style="color:#ffffff;font-weight:600;font-size:16px">Michael\'s Kahoot Cheats</span>';
        header.appendChild(title);
        
        const headerButtons = document.createElement('div');
        headerButtons.style.display = 'flex';
        headerButtons.style.gap = '8px';
        
        const settingsBtn = createControlBtn('⚙️', '#2a2a35');
        settingsBtn.title = 'Settings';
        const closeBtn = createControlBtn('×', '#ff4757');
        
        headerButtons.appendChild(settingsBtn);
        headerButtons.appendChild(closeBtn);
        header.appendChild(headerButtons);
        state.ui.appendChild(header);
        
        // Content Area
        const content = document.createElement('div');
        content.style.padding = '20px';
        
        // === SETTINGS PANEL ===
        const settingsPanel = document.createElement('div');
        settingsPanel.style.cssText = `
            margin-bottom: 20px;
            padding: 15px;
            background: #1a1a24;
            border-radius: 8px;
            border: 1px solid #2a2a35;
            display: none;
        `;
        
        const settingsTitle = document.createElement('div');
        settingsTitle.textContent = 'SETTINGS';
        Object.assign(settingsTitle.style, {
            color: '#6c5ce7',
            fontSize: '11px',
            fontWeight: '600',
            letterSpacing: '1px',
            marginBottom: '15px',
            textTransform: 'uppercase'
        });
        
        // Keybinds display
        const keybindsContainer = document.createElement('div');
        keybindsContainer.style.marginBottom = '15px';
        
        const keybindsLabel = document.createElement('div');
        keybindsLabel.textContent = 'KEYBOARD SHORTCUTS';
        Object.assign(keybindsLabel.style, {
            color: '#888888',
            fontSize: '10px',
            fontWeight: '600',
            marginBottom: '8px',
            textTransform: 'uppercase'
        });
        
        const keybindsList = document.createElement('div');
        keybindsList.style.cssText = `
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 8px;
            font-size: 11px;
        `;
        
        const shortcuts = [
            { key: 'Q', action: 'Toggle UI' },
            { key: 'B', action: 'Toggle Highlight' },
            { key: 'R', action: 'Toggle Revealer' },
            { key: 'L', action: 'Toggle Answer Locker' },
            { key: 'X', action: 'Self Destruct' }
            // Ctrl+P is HIDDEN - not shown here
        ];
        
        shortcuts.forEach(s => {
            const item = document.createElement('div');
            item.style.cssText = `
                background: #222222;
                padding: 6px 8px;
                border-radius: 4px;
                display: flex;
                align-items: center;
                gap: 6px;
            `;
            item.innerHTML = `
                <kbd style="background:#6c5ce7;color:white;padding:2px 6px;border-radius:3px;font-size:10px;">Ctrl+${s.key}</kbd>
                <span style="color:#cccccc;">${s.action}</span>
            `;
            keybindsList.appendChild(item);
        });
        
        keybindsContainer.appendChild(keybindsLabel);
        keybindsContainer.appendChild(keybindsList);
        
        // Reset button
        const resetBtn = document.createElement('button');
        resetBtn.textContent = '↻ RESTORE DEFAULTS';
        Object.assign(resetBtn.style, {
            width: '100%',
            padding: '10px',
            background: '#2a2a35',
            color: '#ffffff',
            border: '1px solid #6c5ce7',
            borderRadius: '6px',
            fontSize: '12px',
            fontWeight: '600',
            cursor: 'pointer',
            transition: 'all 0.2s'
        });
        
        resetBtn.addEventListener('mouseenter', () => {
            resetBtn.style.background = '#6c5ce7';
        });
        resetBtn.addEventListener('mouseleave', () => {
            resetBtn.style.background = '#2a2a35';
        });
        
        resetBtn.addEventListener('click', () => {
            CONFIG = { ...DEFAULTS };
            state.highlightMode = DEFAULTS.highlightMode;
            state.revealerMode = DEFAULTS.revealerMode;
            state.lockerEnabled = DEFAULTS.lockerEnabled;
            localStorage.setItem('michaelsKahootConfig', JSON.stringify(CONFIG));
            
            updateModeIndicators();
            updateAllSwitches();
            
            if (state.markersEnabled) applyHighlight();
            if (state.revealerEnabled) showRevealer();
            if (state.lockerEnabled) enableAnswerLocker();
            else disableAnswerLocker();
        });
        
        settingsPanel.appendChild(settingsTitle);
        settingsPanel.appendChild(keybindsContainer);
        settingsPanel.appendChild(resetBtn);
        content.appendChild(settingsPanel);
        
        // Quiz Lookup Section
        const lookupSection = document.createElement('div');
        lookupSection.style.marginBottom = '20px';
        
        const lookupLabel = document.createElement('div');
        lookupLabel.textContent = 'QUIZ LOOKUP';
        Object.assign(lookupLabel.style, {
            color: '#6c5ce7',
            fontSize: '11px',
            fontWeight: '600',
            letterSpacing: '1px',
            marginBottom: '10px',
            textTransform: 'uppercase'
        });
        
        const lookupContainer = document.createElement('div');
        lookupContainer.style.position = 'relative';
        
        state.searchInput = document.createElement('input');
        Object.assign(state.searchInput.style, {
            width: '100%',
            padding: '12px 40px 12px 12px',
            background: '#1a1a24',
            border: '2px solid #2a2a35',
            borderRadius: '8px',
            color: '#ffffff',
            outline: 'none',
            fontSize: '14px',
            boxSizing: 'border-box'
        });
        state.searchInput.placeholder = 'Search quiz name or paste Quiz ID...';
        
        const searchIcon = document.createElement('div');
        searchIcon.innerHTML = '🔍';
        Object.assign(searchIcon.style, {
            position: 'absolute',
            right: '12px',
            top: '50%',
            transform: 'translateY(-50%)',
            color: '#6c5ce7',
            cursor: 'pointer',
            fontSize: '16px'
        });
        
        state.resultsDropdown = document.createElement('div');
        Object.assign(state.resultsDropdown.style, {
            position: 'absolute',
            top: '100%',
            left: '0',
            width: '100%',
            maxHeight: '200px',
            overflowY: 'auto',
            backgroundColor: '#1a1a24',
            border: '1px solid #2a2a35',
            borderRadius: '8px',
            marginTop: '5px',
            display: 'none',
            zIndex: '100000'
        });
        
        lookupContainer.appendChild(state.searchInput);
        lookupContainer.appendChild(searchIcon);
        lookupContainer.appendChild(state.resultsDropdown);
        lookupSection.appendChild(lookupLabel);
        lookupSection.appendChild(lookupContainer);
        content.appendChild(lookupSection);
        
        // === QUIZ BROWSER SECTION (NEW) ===
        const quizBrowserSection = document.createElement('div');
        quizBrowserSection.style.marginBottom = '20px';
        
        const quizBrowserHeader = document.createElement('div');
        quizBrowserHeader.style.cssText = `
            padding: 12px;
            background: #1a1a24;
            border-radius: 8px;
            border: 1px solid #2a2a35;
            display: flex;
            align-items: center;
            justify-content: space-between;
            cursor: pointer;
            user-select: none;
        `;
        
        const quizBrowserLeft = document.createElement('div');
        quizBrowserLeft.style.display = 'flex';
        quizBrowserLeft.style.alignItems = 'center';
        quizBrowserLeft.style.gap = '8px';
        
        const quizBrowserIcon = document.createElement('span');
        quizBrowserIcon.innerHTML = '📚';
        quizBrowserIcon.style.fontSize = '16px';
        
        const quizBrowserTitle = document.createElement('span');
        quizBrowserTitle.textContent = 'Quiz Browser';
        quizBrowserTitle.style.cssText = `
            color: #ffffff;
            font-size: 14px;
            font-weight: 500;
        `;
        
        const quizBrowserCount = document.createElement('span');
        quizBrowserCount.id = 'quizBrowserCount';
        quizBrowserCount.textContent = '(0)';
        quizBrowserCount.style.cssText = `
            color: #888888;
            font-size: 12px;
        `;
        
        quizBrowserLeft.appendChild(quizBrowserIcon);
        quizBrowserLeft.appendChild(quizBrowserTitle);
        quizBrowserLeft.appendChild(quizBrowserCount);
        
        state.quizBrowserArrow = document.createElement('div');
        state.quizBrowserArrow.innerHTML = '▼';
        state.quizBrowserArrow.style.color = '#ffffff';
        state.quizBrowserArrow.style.fontSize = '12px';
        
        quizBrowserHeader.appendChild(quizBrowserLeft);
        quizBrowserHeader.appendChild(state.quizBrowserArrow);
        
        state.quizBrowserContainer = document.createElement('div');
        state.quizBrowserContainer.style.cssText = `
            padding: 12px;
            background: #0a0a0a;
            border: 1px solid #2a2a35;
            border-top: none;
            border-radius: 0 0 8px 8px;
            display: none;
            max-height: 300px;
            overflow-y: auto;
        `;
        
        quizBrowserSection.appendChild(quizBrowserHeader);
        quizBrowserSection.appendChild(state.quizBrowserContainer);
        content.appendChild(quizBrowserSection);
        
        // === FEATURES SECTION ===
        const featuresSection = document.createElement('div');
        featuresSection.style.marginBottom = '15px';
        
        const featuresLabel = document.createElement('div');
        featuresLabel.textContent = 'FEATURES';
        Object.assign(featuresLabel.style, {
            color: '#6c5ce7',
            fontSize: '11px',
            fontWeight: '600',
            letterSpacing: '1px',
            marginBottom: '12px',
            textTransform: 'uppercase'
        });
        featuresSection.appendChild(featuresLabel);
        
        // HIGHLIGHT SECTION (with dropdown)
        const highlightSection = createFeatureSection(
            'Highlight Answers',
            'highlight',
            ['LEGIT', 'BLATANT'],
            state.highlightMode,
            state.markersEnabled
        );
        featuresSection.appendChild(highlightSection.container);
        
        // REVEALER SECTION (with dropdown)
        const revealerSection = createFeatureSection(
            'Answer Revealer',
            'revealer',
            ['LEGIT', 'BLATANT'],
            state.revealerMode,
            state.revealerEnabled
        );
        featuresSection.appendChild(revealerSection.container);
        
        // ANSWER LOCKER SECTION (simple toggle)
        const lockerSection = createSimpleToggle(
            'Answer Locker (Ctrl+L)',
            'locker',
            state.lockerEnabled
        );
        featuresSection.appendChild(lockerSection.container);
        
        content.appendChild(featuresSection);
        
        // Game Info Section
        const infoSection = document.createElement('div');
        
        const infoLabel = document.createElement('div');
        infoLabel.textContent = 'GAME INFO';
        Object.assign(infoLabel.style, {
            color: '#6c5ce7',
            fontSize: '11px',
            fontWeight: '600',
            letterSpacing: '1px',
            marginBottom: '12px',
            textTransform: 'uppercase'
        });
        
        const infoCard = document.createElement('div');
        Object.assign(infoCard.style, {
            background: '#1a1a24',
            borderRadius: '8px',
            padding: '15px',
            border: '1px solid #2a2a35'
        });
        
        state.questionDisplay = document.createElement('div');
        state.questionDisplay.id = 'questionDisplay';
        state.questionDisplay.textContent = 'Questions: 0/0';
        Object.assign(state.questionDisplay.style, {
            color: '#ffffff',
            fontSize: '13px',
            marginBottom: '8px'
        });
        
        state.pinDisplay = document.createElement('div');
        state.pinDisplay.id = 'pinDisplay';
        state.pinDisplay.textContent = 'Game PIN: None';
        Object.assign(state.pinDisplay.style, {
            color: '#6c5ce7',
            fontSize: '13px',
            fontWeight: '500'
        });
        
        infoCard.appendChild(state.questionDisplay);
        infoCard.appendChild(state.pinDisplay);
        infoSection.appendChild(infoLabel);
        infoSection.appendChild(infoCard);
        content.appendChild(infoSection);
        
        state.ui.appendChild(content);
        document.body.appendChild(state.ui);
        
        // === STYLES ===
        const styleSheet = document.createElement('style');
        styleSheet.textContent = `
            .kahoot-marker {
                position: absolute !important;
                width: 8px !important;
                height: 8px !important;
                background-color: #000000 !important;
                border-radius: 1px !important;
                pointer-events: none !important;
                z-index: 10000 !important;
            }
            .kahoot-marker.top-left { top: 2px !important; left: 2px !important; }
            .kahoot-marker.top-right { top: 2px !important; right: 2px !important; }
            .kahoot-marker.bottom-left { bottom: 2px !important; left: 2px !important; }
            .kahoot-marker.bottom-right { bottom: 2px !important; right: 2px !important; }
            
            .kahoot-correct {
                background-color: #00ff00 !important;
                color: #000000 !important;
                border: 3px solid #00cc00 !important;
                box-shadow: 0 0 20px #00ff00 !important;
                transform: scale(1.05) !important;
                transition: all 0.2s !important;
                z-index: 10001 !important;
            }
            .kahoot-incorrect {
                background-color: #ff0000 !important;
                color: #ffffff !important;
                border: 3px solid #cc0000 !important;
                opacity: 0.9 !important;
                filter: grayscale(50%) !important;
                z-index: 10001 !important;
            }
            
            .kahoot-revealer-blatant {
                position: fixed !important;
                bottom: 20px !important;
                left: 20px !important;
                background: rgba(0,0,0,0.9) !important;
                color: #00ff00 !important;
                padding: 15px !important;
                border-radius: 8px !important;
                border: 2px solid #00ff00 !important;
                font-size: 18px !important;
                font-weight: bold !important;
                z-index: 100000 !important;
                box-shadow: 0 0 20px #00ff00 !important;
                max-width: 300px !important;
            }
            
            .kahoot-revealer-legit {
                position: fixed !important;
                bottom: 10px !important;
                left: 10px !important;
                background: rgba(0,0,0,0.3) !important;
                color: #ffffff !important;
                padding: 4px 8px !important;
                border-radius: 2px !important;
                font-size: 11px !important;
                font-family: monospace !important;
                z-index: 100000 !important;
                border: none !important;
                box-shadow: none !important;
            }
            
            .quiz-item {
                margin-bottom: 8px;
                border: 1px solid #2a2a35;
                border-radius: 6px;
                overflow: hidden;
            }
            
            .quiz-header {
                padding: 10px;
                background: #1a1a24;
                cursor: pointer;
                display: flex;
                align-items: center;
                justify-content: space-between;
                color: #ffffff;
                font-size: 13px;
                font-weight: 500;
            }
            
            .quiz-header:hover {
                background: #2a2a35;
            }
            
            .quiz-content {
                padding: 10px;
                background: #0f0f13;
                display: none;
            }
            
            .quiz-question-item {
                margin-bottom: 8px;
                border: 1px solid #2a2a35;
                border-radius: 4px;
                overflow: hidden;
            }
            
            .quiz-question-header {
                padding: 8px;
                background: #1a1a24;
                cursor: pointer;
                display: flex;
                align-items: center;
                justify-content: space-between;
                color: #ffffff;
                font-size: 12px;
            }
            
            .quiz-question-header:hover {
                background: #2a2a35;
            }
            
            .quiz-question-content {
                padding: 10px;
                background: #0a0a0a;
                display: none;
            }
            
            .quiz-answer {
                padding: 6px 8px;
                margin: 4px 0;
                background: #1a1a24;
                border-left: 3px solid #888888;
                border-radius: 4px;
                color: #ffffff;
                font-size: 12px;
            }
            
            .quiz-answer-correct {
                border-left: 3px solid #00ff00;
                background: rgba(0,255,0,0.1);
                font-weight: 500;
            }
            
            .quiz-answer-incorrect {
                border-left: 3px solid #ff4757;
                opacity: 0.8;
            }
        `;
        document.head.appendChild(styleSheet);
        
        // === FEATURE CREATION FUNCTIONS ===
        
        function createFeatureSection(title, type, modes, currentMode, enabled) {
            const container = document.createElement('div');
            container.style.marginBottom = '10px';
            
            const header = document.createElement('div');
            header.style.cssText = `
                padding: 12px;
                background: #1a1a24;
                border-radius: 8px;
                border: 1px solid #2a2a35;
                display: flex;
                align-items: center;
                justify-content: space-between;
                cursor: pointer;
                user-select: none;
            `;
            
            const leftDiv = document.createElement('div');
            leftDiv.style.display = 'flex';
            leftDiv.style.alignItems = 'center';
            leftDiv.style.gap = '12px';
            
            const label = document.createElement('span');
            label.textContent = title;
            label.style.cssText = `
                color: #ffffff;
                font-size: 14px;
                font-weight: 500;
            `;
            
            const modeIndicator = document.createElement('span');
            modeIndicator.id = `${type}ModeIndicator`;
            modeIndicator.textContent = `[${currentMode.toUpperCase()}]`;
            modeIndicator.style.cssText = `
                color: ${currentMode === 'legit' ? '#888888' : (type === 'highlight' ? '#ff4757' : '#00ff00')};
                font-size: 11px;
                font-weight: 600;
                background: #222222;
                padding: 2px 6px;
                border-radius: 4px;
            `;
            
            leftDiv.appendChild(label);
            leftDiv.appendChild(modeIndicator);
            
            const rightDiv = document.createElement('div');
            rightDiv.style.display = 'flex';
            rightDiv.style.alignItems = 'center';
            rightDiv.style.gap = '12px';
            
            const switchEl = document.createElement('div');
            switchEl.style.cssText = `
                position: relative;
                width: 44px;
                height: 24px;
                background: ${enabled ? '#6c5ce7' : '#2a2a35'};
                border-radius: 12px;
            `;
            
            const knob = document.createElement('div');
            knob.style.cssText = `
                position: absolute;
                width: 18px;
                height: 18px;
                background: ${enabled ? '#ffffff' : '#888888'};
                border-radius: 50%;
                top: 3px;
                left: ${enabled ? '23px' : '3px'};
                transition: left 0.2s;
            `;
            switchEl.appendChild(knob);
            
            const arrow = document.createElement('div');
            arrow.innerHTML = '▼';
            arrow.style.color = '#ffffff';
            arrow.style.fontSize = '12px';
            
            rightDiv.appendChild(switchEl);
            rightDiv.appendChild(arrow);
            
            header.appendChild(leftDiv);
            header.appendChild(rightDiv);
            
            const dropdown = document.createElement('div');
            dropdown.style.cssText = `
                padding: 12px;
                background: #0a0a0a;
                border: 1px solid #2a2a35;
                border-top: none;
                border-radius: 0 0 8px 8px;
                display: none;
            `;
            
            const modeLabel = document.createElement('div');
            modeLabel.textContent = `${title.toUpperCase()} MODE`;
            Object.assign(modeLabel.style, {
                color: '#888888',
                fontSize: '10px',
                fontWeight: '600',
                letterSpacing: '1px',
                marginBottom: '8px'
            });
            
            const modeButtons = document.createElement('div');
            modeButtons.style.display = 'flex';
            modeButtons.style.gap = '8px';
            
            const legitBtn = document.createElement('button');
            legitBtn.textContent = 'LEGIT';
            Object.assign(legitBtn.style, {
                flex: '1',
                padding: '8px',
                background: currentMode === 'legit' ? '#6c5ce7' : '#222222',
                color: '#ffffff',
                border: 'none',
                borderRadius: '4px',
                cursor: 'pointer',
                fontSize: '12px',
                fontWeight: currentMode === 'legit' ? '600' : '400'
            });
            
            const blatantBtn = document.createElement('button');
            blatantBtn.textContent = 'BLATANT';
            Object.assign(blatantBtn.style, {
                flex: '1',
                padding: '8px',
                background: currentMode === 'blatant' ? (type === 'highlight' ? '#ff4757' : '#00ff00') : '#222222',
                color: currentMode === 'blatant' && type === 'revealer' ? '#000000' : '#ffffff',
                border: 'none',
                borderRadius: '4px',
                cursor: 'pointer',
                fontSize: '12px',
                fontWeight: currentMode === 'blatant' ? '600' : '400'
            });
            
            modeButtons.appendChild(legitBtn);
            modeButtons.appendChild(blatantBtn);
            dropdown.appendChild(modeLabel);
            dropdown.appendChild(modeButtons);
            
            container.appendChild(header);
            container.appendChild(dropdown);
            
            // Store references
            if (type === 'highlight') {
                state.highlightSwitch = switchEl;
                state.highlightKnob = knob;
                state.highlightArrow = arrow;
                state.highlightDropdown = dropdown;
                state.highlightModeIndicator = modeIndicator;
                
                header.addEventListener('click', (e) => {
                    const rect = switchEl.getBoundingClientRect();
                    const isClickOnSwitch = e.clientX >= rect.left && e.clientX <= rect.right && 
                                            e.clientY >= rect.top && e.clientY <= rect.bottom;
                    
                    if (isClickOnSwitch) {
                        state.markersEnabled = !state.markersEnabled;
                        switchEl.style.background = state.markersEnabled ? '#6c5ce7' : '#2a2a35';
                        knob.style.left = state.markersEnabled ? '23px' : '3px';
                        knob.style.background = state.markersEnabled ? '#ffffff' : '#888888';
                        
                        if (state.markersEnabled) applyHighlight();
                        else removeHighlight();
                    } else {
                        const isOpen = dropdown.style.display === 'block';
                        dropdown.style.display = isOpen ? 'none' : 'block';
                        arrow.innerHTML = isOpen ? '▼' : '▲';
                    }
                });
                
                legitBtn.addEventListener('click', () => {
                    state.highlightMode = 'legit';
                    CONFIG.highlightMode = 'legit';
                    localStorage.setItem('michaelsKahootConfig', JSON.stringify(CONFIG));
                    modeIndicator.textContent = '[LEGIT]';
                    modeIndicator.style.color = '#888888';
                    legitBtn.style.background = '#6c5ce7';
                    legitBtn.style.fontWeight = '600';
                    blatantBtn.style.background = '#222222';
                    blatantBtn.style.fontWeight = '400';
                    if (state.markersEnabled) applyHighlight();
                });
                
                blatantBtn.addEventListener('click', () => {
                    state.highlightMode = 'blatant';
                    CONFIG.highlightMode = 'blatant';
                    localStorage.setItem('michaelsKahootConfig', JSON.stringify(CONFIG));
                    modeIndicator.textContent = '[BLATANT]';
                    modeIndicator.style.color = '#ff4757';
                    blatantBtn.style.background = '#ff4757';
                    blatantBtn.style.fontWeight = '600';
                    legitBtn.style.background = '#222222';
                    legitBtn.style.fontWeight = '400';
                    if (state.markersEnabled) applyHighlight();
                });
            }
            
            if (type === 'revealer') {
                state.revealerSwitch = switchEl;
                state.revealerKnob = knob;
                state.revealerArrow = arrow;
                state.revealerDropdown = dropdown;
                state.revealerModeIndicator = modeIndicator;
                
                header.addEventListener('click', (e) => {
                    const rect = switchEl.getBoundingClientRect();
                    const isClickOnSwitch = e.clientX >= rect.left && e.clientX <= rect.right && 
                                            e.clientY >= rect.top && e.clientY <= rect.bottom;
                    
                    if (isClickOnSwitch) {
                        state.revealerEnabled = !state.revealerEnabled;
                        switchEl.style.background = state.revealerEnabled ? '#6c5ce7' : '#2a2a35';
                        knob.style.left = state.revealerEnabled ? '23px' : '3px';
                        knob.style.background = state.revealerEnabled ? '#ffffff' : '#888888';
                        
                        if (state.revealerEnabled) showRevealer();
                        else hideRevealer();
                    } else {
                        const isOpen = dropdown.style.display === 'block';
                        dropdown.style.display = isOpen ? 'none' : 'block';
                        arrow.innerHTML = isOpen ? '▼' : '▲';
                    }
                });
                
                legitBtn.addEventListener('click', () => {
                    state.revealerMode = 'legit';
                    CONFIG.revealerMode = 'legit';
                    localStorage.setItem('michaelsKahootConfig', JSON.stringify(CONFIG));
                    modeIndicator.textContent = '[LEGIT]';
                    modeIndicator.style.color = '#888888';
                    legitBtn.style.background = '#6c5ce7';
                    legitBtn.style.fontWeight = '600';
                    blatantBtn.style.background = '#222222';
                    blatantBtn.style.fontWeight = '400';
                    blatantBtn.style.color = '#ffffff';
                    if (state.revealerEnabled) showRevealer();
                });
                
                blatantBtn.addEventListener('click', () => {
                    state.revealerMode = 'blatant';
                    CONFIG.revealerMode = 'blatant';
                    localStorage.setItem('michaelsKahootConfig', JSON.stringify(CONFIG));
                    modeIndicator.textContent = '[BLATANT]';
                    modeIndicator.style.color = '#00ff00';
                    blatantBtn.style.background = '#00ff00';
                    blatantBtn.style.color = '#000000';
                    blatantBtn.style.fontWeight = '600';
                    legitBtn.style.background = '#222222';
                    legitBtn.style.fontWeight = '400';
                    if (state.revealerEnabled) showRevealer();
                });
            }
            
            return { container, switchEl, knob, dropdown };
        }
        
        function createSimpleToggle(title, type, enabled) {
            const container = document.createElement('div');
            container.style.marginBottom = '10px';
            
            const toggleBar = document.createElement('div');
            toggleBar.style.cssText = `
                padding: 12px;
                background: #1a1a24;
                border-radius: 8px;
                border: 1px solid #2a2a35;
                display: flex;
                align-items: center;
                justify-content: space-between;
                cursor: pointer;
                user-select: none;
            `;
            
            const label = document.createElement('span');
            label.textContent = title;
            label.style.cssText = `
                color: #ffffff;
                font-size: 14px;
                font-weight: 500;
            `;
            
            const switchEl = document.createElement('div');
            switchEl.style.cssText = `
                position: relative;
                width: 44px;
                height: 24px;
                background: ${enabled ? '#6c5ce7' : '#2a2a35'};
                border-radius: 12px;
            `;
            
            const knob = document.createElement('div');
            knob.style.cssText = `
                position: absolute;
                width: 18px;
                height: 18px;
                background: ${enabled ? '#ffffff' : '#888888'};
                border-radius: 50%;
                top: 3px;
                left: ${enabled ? '23px' : '3px'};
                transition: left 0.2s;
            `;
            switchEl.appendChild(knob);
            
            toggleBar.appendChild(label);
            toggleBar.appendChild(switchEl);
            container.appendChild(toggleBar);
            
            // Store references
            if (type === 'locker') {
                state.lockerSwitch = switchEl;
                state.lockerKnob = knob;
                
                toggleBar.addEventListener('click', () => {
                    state.lockerEnabled = !state.lockerEnabled;
                    switchEl.style.background = state.lockerEnabled ? '#6c5ce7' : '#2a2a35';
                    knob.style.left = state.lockerEnabled ? '23px' : '3px';
                    knob.style.background = state.lockerEnabled ? '#ffffff' : '#888888';
                    
                    if (state.lockerEnabled) enableAnswerLocker();
                    else disableAnswerLocker();
                    
                    CONFIG.lockerEnabled = state.lockerEnabled;
                    localStorage.setItem('michaelsKahootConfig', JSON.stringify(CONFIG));
                });
            }
            
            return { container, switchEl, knob };
        }
        
        function updateAllSwitches() {
            if (state.highlightSwitch) {
                state.highlightSwitch.style.background = state.markersEnabled ? '#6c5ce7' : '#2a2a35';
                state.highlightKnob.style.left = state.markersEnabled ? '23px' : '3px';
                state.highlightKnob.style.background = state.markersEnabled ? '#ffffff' : '#888888';
            }
            
            if (state.revealerSwitch) {
                state.revealerSwitch.style.background = state.revealerEnabled ? '#6c5ce7' : '#2a2a35';
                state.revealerKnob.style.left = state.revealerEnabled ? '23px' : '3px';
                state.revealerKnob.style.background = state.revealerEnabled ? '#ffffff' : '#888888';
            }
            
            if (state.lockerSwitch) {
                state.lockerSwitch.style.background = state.lockerEnabled ? '#6c5ce7' : '#2a2a35';
                state.lockerKnob.style.left = state.lockerEnabled ? '23px' : '3px';
                state.lockerKnob.style.background = state.lockerEnabled ? '#ffffff' : '#888888';
            }
        }
        
        function updateModeIndicators() {
            if (state.highlightModeIndicator) {
                state.highlightModeIndicator.textContent = `[${state.highlightMode.toUpperCase()}]`;
                state.highlightModeIndicator.style.color = state.highlightMode === 'legit' ? '#888888' : '#ff4757';
            }
            
            if (state.revealerModeIndicator) {
                state.revealerModeIndicator.textContent = `[${state.revealerMode.toUpperCase()}]`;
                state.revealerModeIndicator.style.color = state.revealerMode === 'legit' ? '#888888' : '#00ff00';
            }
        }
        
        // Settings toggle
        settingsBtn.addEventListener('click', () => {
            state.settingsOpen = !state.settingsOpen;
            settingsPanel.style.display = state.settingsOpen ? 'block' : 'none';
        });
        
        // Quiz Browser toggle
        quizBrowserHeader.addEventListener('click', () => {
            state.quizBrowserOpen = !state.quizBrowserOpen;
            state.quizBrowserContainer.style.display = state.quizBrowserOpen ? 'block' : 'none';
            state.quizBrowserArrow.innerHTML = state.quizBrowserOpen ? '▲' : '▼';
            
            if (state.quizBrowserOpen && state.questionsData.length > 0) {
                renderQuizBrowser();
            }
        });
        
        // === QUIZ BROWSER FUNCTIONS ===
        function renderQuizBrowser() {
            if (!state.quizBrowserContainer) return;
            
            const countSpan = document.getElementById('quizBrowserCount');
            if (countSpan) {
                countSpan.textContent = `(${state.questionsData.length})`;
            }
            
            if (state.questionsData.length === 0) {
                state.quizBrowserContainer.innerHTML = '<div style="color:#888; text-align:center; padding:20px;">No quiz loaded</div>';
                return;
            }
            
            let html = '';
            
            // Quiz-level dropdown
            html += `<div class="quiz-item">`;
            html += `<div class="quiz-header" onclick="this.nextElementSibling.style.display = this.nextElementSibling.style.display === 'none' ? 'block' : 'none'">`;
            html += `<span>📊 ${state.currentQuizTitle || 'Quiz'}</span>`;
            html += `<span>▼</span>`;
            html += `</div>`;
            html += `<div class="quiz-content" style="display:none">`;
            
            // Questions
            state.questionsData.forEach((q, qIndex) => {
                html += `<div class="quiz-question-item">`;
                html += `<div class="quiz-question-header" onclick="this.nextElementSibling.style.display = this.nextElementSibling.style.display === 'none' ? 'block' : 'none'">`;
                html += `<span>Q${qIndex + 1}: ${q.question || 'Question'}</span>`;
                html += `<span>▼</span>`;
                html += `</div>`;
                html += `<div class="quiz-question-content" style="display:none">`;
                
                // Answers
                if (q.choices && q.choices.length > 0) {
                    q.choices.forEach((choice, cIndex) => {
                        const isCorrect = choice.correct || (q.correctIndices && q.correctIndices.includes(cIndex));
                        html += `<div class="quiz-answer ${isCorrect ? 'quiz-answer-correct' : 'quiz-answer-incorrect'}">`;
                        html += `${String.fromCharCode(65 + cIndex)}. ${choice.answer || choice.text || 'Answer'}`;
                        if (isCorrect) html += ` ✓`;
                        html += `</div>`;
                    });
                }
                
                html += `</div>`; // Close question content
                html += `</div>`; // Close question item
            });
            
            html += `</div>`; // Close quiz content
            html += `</div>`; // Close quiz item
            
            state.quizBrowserContainer.innerHTML = html;
            
            // Re-attach event listeners
            const quizHeader = state.quizBrowserContainer.querySelector('.quiz-header');
            if (quizHeader) {
                quizHeader.onclick = function(e) {
                    const content = this.nextElementSibling;
                    content.style.display = content.style.display === 'none' ? 'block' : 'none';
                };
            }
            
            const questionHeaders = state.quizBrowserContainer.querySelectorAll('.quiz-question-header');
            questionHeaders.forEach(header => {
                header.onclick = function(e) {
                    const content = this.nextElementSibling;
                    content.style.display = content.style.display === 'none' ? 'block' : 'none';
                };
            });
        }
        
        // === ANSWER LOCKER ===
        function enableAnswerLocker() {
            document.addEventListener('click', handleAnswerClick, true);
        }
        
        function disableAnswerLocker() {
            document.removeEventListener('click', handleAnswerClick, true);
        }
        
        function handleAnswerClick(e) {
            if (!state.lockerEnabled || !state.questions[state.currentQuestion]) return;
            
            const question = state.questions[state.currentQuestion];
            if (!question.correctIndices || question.correctIndices.length === 0) return;
            
            const correctIndex = question.correctIndices[0];
            const correctButton = document.querySelector(`button[data-functional-selector="answer-${correctIndex}"]`);
            
            if (correctButton && !correctButton.contains(e.target)) {
                e.preventDefault();
                e.stopPropagation();
                
                const event = new MouseEvent('click', {
                    view: window,
                    bubbles: true,
                    cancelable: true
                });
                correctButton.dispatchEvent(event);
            }
        }
        
        // === HIGHLIGHT FUNCTIONS ===
        function applyHighlight() {
            if (!state.markersEnabled || state.currentQuestion < 0 || 
                !state.questions[state.currentQuestion]) return;
            
            removeHighlight();
            
            const question = state.questions[state.currentQuestion];
            if (!question.correctIndices || question.correctIndices.length === 0) return;
            
            if (state.highlightMode === 'legit') {
                const correctIndex = question.correctIndices[0];
                const cornerClass = CORNER_MAP[correctIndex] || 'bottom-right';
                
                let attempts = 0;
                const tryApply = () => {
                    attempts++;
                    const button = document.querySelector(`button[data-functional-selector="answer-${correctIndex}"]`);
                    
                    if (button) {
                        const marker = document.createElement('div');
                        marker.className = `kahoot-marker ${cornerClass}`;
                        button.style.position = 'relative';
                        button.appendChild(marker);
                    } else if (attempts < 10) {
                        setTimeout(tryApply, 100);
                    }
                };
                tryApply();
            } else {
                const correctIndices = question.correctIndices;
                
                let attempts = 0;
                const tryApply = () => {
                    attempts++;
                    const allButtons = document.querySelectorAll('button[data-functional-selector^="answer-"]');
                    
                    if (allButtons.length > 0) {
                        allButtons.forEach((button, index) => {
                            if (correctIndices.includes(index)) {
                                button.classList.add('kahoot-correct');
                                button.classList.remove('kahoot-incorrect');
                            } else {
                                button.classList.add('kahoot-incorrect');
                                button.classList.remove('kahoot-correct');
                            }
                        });
                    } else if (attempts < 10) {
                        setTimeout(tryApply, 100);
                    }
                };
                tryApply();
            }
        }
        
        function removeHighlight() {
            document.querySelectorAll('.kahoot-marker').forEach(m => m.remove());
            document.querySelectorAll('.kahoot-correct, .kahoot-incorrect').forEach(el => {
                el.classList.remove('kahoot-correct', 'kahoot-incorrect');
            });
        }
        
        // === REVEALER FUNCTIONS ===
        let revealerElement = null;
        
        function showRevealer() {
            hideRevealer();
            
            if (!state.questions[state.currentQuestion]) return;
            
            const question = state.questions[state.currentQuestion];
            if (!question.correctIndices || question.correctIndices.length === 0) return;
            
            const correctIndex = question.correctIndices[0] + 1;
            
            if (state.revealerMode === 'legit') {
                revealerElement = document.createElement('div');
                revealerElement.className = 'kahoot-revealer-legit';
                revealerElement.textContent = `✓${correctIndex}`;
            } else {
                revealerElement = document.createElement('div');
                revealerElement.className = 'kahoot-revealer-blatant';
                revealerElement.innerHTML = `
                    <div style="font-size: 12px; color: #888; margin-bottom: 2px;">🔍 ANSWER</div>
                    <div style="font-size: 24px;">${correctIndex}</div>
                `;
            }
            
            document.body.appendChild(revealerElement);
        }
        
        function hideRevealer() {
            if (revealerElement && revealerElement.parentNode) {
                revealerElement.parentNode.removeChild(revealerElement);
                revealerElement = null;
            }
        }
        
        // === SEARCH ===
        function performSearch(query) {
            if (!query) return;
            
            const uuidPattern = /^[0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12}$/i;
            if (uuidPattern.test(query)) {
                fetchQuiz(query);
                hideResults();
                return;
            }
            
            state.resultsDropdown.innerHTML = '<div style="padding:10px;color:#888;text-align:center;">Searching...</div>';
            state.resultsDropdown.style.display = 'block';
            
            fetch(`${CONFIG.searchEndpoint}?query=${encodeURIComponent(query)}`)
                .then(r => r.json())
                .then(data => {
                    let results = data.entities || data;
                    if (!results || !results.length) {
                        state.resultsDropdown.innerHTML = '<div style="padding:10px;color:#888;text-align:center;">No results</div>';
                        return;
                    }
                    
                    state.resultsDropdown.innerHTML = '';
                    results.slice(0, 8).forEach(r => {
                        const card = r.card || r;
                        if (!card.uuid) return;
                        
                        const item = document.createElement('div');
                        item.style.cssText = `
                            padding: 10px;
                            border-bottom: 1px solid #2a2a35;
                            cursor: pointer;
                            color: #fff;
                            font-size: 13px;
                            display: flex;
                            justify-content: space-between;
                        `;
                        item.innerHTML = `
                            <span>${card.title || 'Quiz'}</span>
                            <span style="color:#6c5ce7;">${card.number_of_questions || '?'}q</span>
                        `;
                        
                        item.addEventListener('click', () => {
                            state.searchInput.value = card.uuid;
                            hideResults();
                            fetchQuiz(card.uuid);
                        });
                        
                        state.resultsDropdown.appendChild(item);
                    });
                })
                .catch(() => {
                    state.resultsDropdown.innerHTML = '<div style="padding:10px;color:#888;text-align:center;">Search failed</div>';
                });
        }
        
        function hideResults() {
            state.resultsDropdown.style.display = 'none';
        }
        
        searchIcon.addEventListener('click', () => performSearch(state.searchInput.value.trim()));
        
        state.searchInput.addEventListener('keypress', (e) => {
            if (e.key === 'Enter') performSearch(state.searchInput.value.trim());
        });
        
        state.searchInput.addEventListener('input', (e) => {
            const query = state.searchInput.value.trim();
            if (query.length >= 3) {
                clearTimeout(state.searchInput.timeout);
                state.searchInput.timeout = setTimeout(() => performSearch(query), 300);
            } else if (query.length === 0) {
                hideResults();
            }
        });
        
        document.addEventListener('click', (e) => {
            if (!lookupContainer.contains(e.target)) {
                hideResults();
            }
        });
        
        // === FETCH QUIZ ===
        function fetchQuiz(quizId) {
            fetch(`${CONFIG.apiEndpoint}${quizId}`)
                .then(r => r.json())
                .then(data => {
                    // Store full question data
                    state.questionsData = data.questions || [];
                    state.currentQuizTitle = data.title || 'Quiz';
                    
                    // Store simplified data for gameplay
                    state.questions = state.questionsData.map(q => ({
                        correctIndices: (q.choices || []).reduce((acc, c, i) => {
                            if (c.correct) acc.push(i);
                            return acc;
                        }, [])
                    }));
                    
                    state.questionDisplay.textContent = `Questions: ${state.questions.length} loaded`;
                    state.searchInput.value = quizId;
                    state.currentQuizId = quizId;
                    
                    // Update quiz browser count
                    const countSpan = document.getElementById('quizBrowserCount');
                    if (countSpan) {
                        countSpan.textContent = `(${state.questionsData.length})`;
                    }
                    
                    // Auto-render if browser is open
                    if (state.quizBrowserOpen) {
                        renderQuizBrowser();
                    }
                    
                    if (state.markersEnabled) setTimeout(applyHighlight, 500);
                    if (state.revealerEnabled) setTimeout(showRevealer, 500);
                });
        }
        
        // === KEYBOARD SHORTCUTS ===
        let activeKeyHandler = null;
        
        function setupKeyboard() {
            if (activeKeyHandler) {
                document.removeEventListener('keydown', activeKeyHandler);
            }
            
            activeKeyHandler = (e) => {
                if (e.target.tagName === 'INPUT' || e.target.tagName === 'TEXTAREA') return;
                if (!e.ctrlKey) return;
                
                const key = e.key.toLowerCase();
                
                if (key === 'q') {
                    e.preventDefault();
                    state.visible = !state.visible;
                    state.ui.style.opacity = state.visible ? '1' : '0';
                    state.ui.style.pointerEvents = state.visible ? 'all' : 'none';
                }
                
                if (key === 'b') {
                    e.preventDefault();
                    state.markersEnabled = !state.markersEnabled;
                    if (state.highlightSwitch) {
                        state.highlightSwitch.style.background = state.markersEnabled ? '#6c5ce7' : '#2a2a35';
                        state.highlightKnob.style.left = state.markersEnabled ? '23px' : '3px';
                        state.highlightKnob.style.background = state.markersEnabled ? '#ffffff' : '#888888';
                    }
                    
                    if (state.markersEnabled) applyHighlight();
                    else removeHighlight();
                }
                
                if (key === 'r') {
                    e.preventDefault();
                    state.revealerEnabled = !state.revealerEnabled;
                    if (state.revealerSwitch) {
                        state.revealerSwitch.style.background = state.revealerEnabled ? '#6c5ce7' : '#2a2a35';
                        state.revealerKnob.style.left = state.revealerEnabled ? '23px' : '3px';
                        state.revealerKnob.style.background = state.revealerEnabled ? '#ffffff' : '#888888';
                    }
                    
                    if (state.revealerEnabled) showRevealer();
                    else hideRevealer();
                }
                
                if (key === 'l') {
                    e.preventDefault();
                    state.lockerEnabled = !state.lockerEnabled;
                    if (state.lockerSwitch) {
                        state.lockerSwitch.style.background = state.lockerEnabled ? '#6c5ce7' : '#2a2a35';
                        state.lockerKnob.style.left = state.lockerEnabled ? '23px' : '3px';
                        state.lockerKnob.style.background = state.lockerEnabled ? '#ffffff' : '#888888';
                    }
                    
                    if (state.lockerEnabled) enableAnswerLocker();
                    else disableAnswerLocker();
                    
                    CONFIG.lockerEnabled = state.lockerEnabled;
                    localStorage.setItem('michaelsKahootConfig', JSON.stringify(CONFIG));
                }
                
                // HIDDEN KEYBIND - Ctrl+P clears console once
                if (key === 'p') {
                    e.preventDefault();
                    // Clear console ONE TIME only - no message shown
                    console.clear = function(){}; // Override to prevent "Console was cleared" message
                    window.console.clear(); // Actually clear
                    // Restore original clear after a delay
                    setTimeout(() => {
                        delete console.clear;
                    }, 100);
                }
                
                if (key === 'x') {
                    e.preventDefault();
                    if (state.ui && state.ui.parentNode) {
                        document.body.removeChild(state.ui);
                    }
                    state.active = false;
                }
            };
            
            document.addEventListener('keydown', activeKeyHandler);
        }
        
        // === MONITORING ===
        function startMonitoring() {
            setInterval(() => {
                if (!state.active || !state.ui) return;
                
                const pinInput = document.querySelector('input[name="gameId"]');
                if (pinInput && pinInput.value && state.pinDisplay) {
                    state.pinDisplay.textContent = `Game PIN: ${pinInput.value}`;
                }
                
                const qCounter = document.querySelector('[data-functional-selector="question-index-counter"]');
                if (qCounter && state.questionDisplay) {
                    const match = qCounter.textContent.match(/(\d+)/);
                    if (match) {
                        const newQ = parseInt(match[1]) - 1;
                        if (newQ !== state.currentQuestion) {
                            state.currentQuestion = newQ;
                            state.questionDisplay.textContent = `Question: ${state.currentQuestion + 1}/${state.questions.length || 0}`;
                            
                            if (state.markersEnabled) setTimeout(applyHighlight, 50);
                            if (state.revealerEnabled) setTimeout(showRevealer, 50);
                        }
                    }
                }
                
                // Keep highlight applied if enabled
                if (state.markersEnabled && state.questions.length > 0) {
                    applyHighlight();
                }
            }, 100);
        }
        
        // === DRAG ===
        let isDragging = false;
        let startX, startY, initialLeft, initialTop;
        
        header.addEventListener('mousedown', (e) => {
            isDragging = true;
            startX = e.clientX;
            startY = e.clientY;
            initialLeft = state.ui.offsetLeft;
            initialTop = state.ui.offsetTop;
            e.preventDefault();
        });
        
        document.addEventListener('mousemove', (e) => {
            if (!isDragging) return;
            
            const dx = e.clientX - startX;
            const dy = e.clientY - startY;
            
            let newLeft = initialLeft + dx;
            let newTop = initialTop + dy;
            
            const maxX = window.innerWidth - state.ui.offsetWidth;
            const maxY = window.innerHeight - state.ui.offsetHeight;
            
            state.ui.style.left = Math.max(0, Math.min(newLeft, maxX)) + 'px';
            state.ui.style.top = Math.max(0, Math.min(newTop, maxY)) + 'px';
        });
        
        document.addEventListener('mouseup', () => {
            isDragging = false;
        });
        
        closeBtn.addEventListener('click', () => {
            if (state.ui && state.ui.parentNode) {
                document.body.removeChild(state.ui);
            }
            state.active = false;
        });
        
        function createControlBtn(text, bgColor) {
            const btn = document.createElement('button');
            btn.textContent = text;
            Object.assign(btn.style, {
                width: '28px',
                height: '28px',
                borderRadius: '6px',
                background: bgColor,
                border: 'none',
                color: '#ffffff',
                cursor: 'pointer',
                fontSize: '16px',
                display: 'flex',
                alignItems: 'center',
                justifyContent: 'center',
                transition: 'transform 0.1s'
            });
            
            btn.addEventListener('mouseenter', () => {
                btn.style.transform = 'scale(1.05)';
            });
            
            btn.addEventListener('mouseleave', () => {
                btn.style.transform = 'scale(1)';
            });
            
            return btn;
        }
        
        setupKeyboard();
        startMonitoring();
    }
})();
