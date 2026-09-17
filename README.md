<!DOCTYPE html>
<html lang="en" class="h-full">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Al-Qadeer Estate Agency - Property Inventory System</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    colors: {
                        emerald: {
                            800: '#064e3b',
                            900: '#022c22',
                            950: '#011c14',
                        },
                        gold: {
                            400: '#facc15',
                            500: '#eab308',
                            600: '#ca8a04',
                            700: '#a16207',
                        }
                    }
                }
            }
        }
    </script>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css" rel="stylesheet">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;500;600;700;850&display=swap');
        body {
            font-family: 'Plus Jakarta+ Sans', sans-serif;
            -webkit-tap-highlight-color: transparent;
        }
        .gold-gradient {
            background: linear-gradient(135deg, #fef08a 0%, #eab308 50%, #ca8a04 100%);
        }
        .emerald-gradient {
            background: linear-gradient(135deg, #064e3b 0%, #022c22 100%);
        }
        .card-shadow {
            box-shadow: 0 10px 25px -5px rgba(0, 0, 0, 0.08), 0 8px 10px -6px rgba(0, 0, 0, 0.08);
        }
        /* Custom scrollbar for clean mobile feel */
        ::-webkit-scrollbar {
            width: 4px;
            height: 4px;
        }
        ::-webkit-scrollbar-track {
            background: #f1f5f9;
        }
        ::-webkit-scrollbar-thumb {
            background: #cbd5e1;
            border-radius: 4px;
        }
    </style>
</head>
<body class="bg-slate-900 text-slate-100 h-full flex justify-center items-center p-0 sm:p-4">

    <!-- Mobile Frame Container for Desktop / PWA Shell Preview -->
    <div id="app-container" class="w-full sm:max-w-md h-full sm:h-[880px] sm:rounded-[3rem] sm:border-8 sm:border-slate-800 bg-slate-950 shadow-2xl flex flex-col overflow-hidden relative">

        <!-- HEADER / STATUS BAR SIMULATOR -->
        <div class="bg-emerald-950/80 backdrop-blur-md px-6 py-3 flex justify-between items-center text-xs font-semibold text-slate-300 border-b border-emerald-900/50 z-50 shrink-0">
            <div class="flex items-center space-x-2">
                <span id="current-time-display">3:59 PM</span>
            </div>
            <div class="flex items-center space-x-2 text-gold-500">
                <span id="user-role-badge" class="px-2 py-0.5 rounded-full text-[10px] bg-gold-500/10 border border-gold-500/30 uppercase tracking-wider font-bold">Visitor Mode</span>
                <i class="fa-solid fa-signal text-[10px]"></i>
                <i class="fa-solid fa-wifi text-[10px]"></i>
                <i class="fa-solid fa-battery-full text-xs"></i>
            </div>
        </div>

        <!-- MAIN DYNAMIC CONTENT AREA -->
        <div id="main-viewport" class="flex-1 overflow-y-auto relative flex flex-col">
            <!-- Dynamic screens injected here -->
        </div>

        <!-- GLOBAL TOAST NOTIFICATION -->
        <div id="toast" class="absolute bottom-6 left-1/2 -translate-x-1/2 z-50 transition-all duration-300 opacity-0 pointer-events-none transform translate-y-4">
            <div class="bg-emerald-900 text-slate-100 border border-gold-500/40 px-5 py-3 rounded-2xl shadow-2xl flex items-center space-x-3 text-sm">
                <i id="toast-icon" class="fa-solid fa-circle-check text-gold-400"></i>
                <span id="toast-message" class="font-medium">Action successful!</span>
            </div>
        </div>

    </div>

    <!-- SCRIPT LOGIC FOR PWA -->
    <script>
        // --- MOCK DATABASE & STATE ---
        let appState = {
            currentScreen: 'login', // login, dashboard, addProperty, visitProperty, propertyDetail
            role: null, // 'staff' or 'visitor'
            selectedSociety: '',
            selectedCategory: '',
            selectedProperty: null,
            properties: [
                {
                    id: 'PROP-101',
                    society: 'DHA Phase 6',
                    category: 'Plot',
                    rate: 'PKR 1.85 Crore',
                    block: 'Sector J',
                    contact: '+92 300 1234567',
                    plotNumber: 'Plot # 45',
                    size: '1 Kanal',
                    status: 'Available',
                    description: 'Prime location park facing residential plot ready for immediate transfer. High investment return potential.',
                    addedBy: 'Staff Admin'
                },
                {
                    id: 'PROP-102',
                    society: 'Bahria Town',
                    category: 'House',
                    rate: 'PKR 3.40 Crore',
                    block: 'Sector C',
                    contact: '+92 321 9876543',
                    plotNumber: 'Villa # 12',
                    size: '10 Marla',
                    status: 'Available',
                    description: 'Brand new luxury 5-bed double unit house with imported fittings, spacious car porch, and modern kitchen.',
                    addedBy: 'Staff Admin'
                },
                {
                    id: 'PROP-103',
                    society: 'DHA Phase 6',
                    category: 'Commercial',
                    rate: 'PKR 5.50 Crore',
                    block: 'Main Boulevard',
                    contact: '+92 300 1234567',
                    plotNumber: 'Shop # 08',
                    size: '4 Marla',
                    status: 'Reserved',
                    description: 'High visibility commercial building plot situated on main boulevard. Ideal for brand outlet or corporate office.',
                    addedBy: 'Staff Admin'
                },
                {
                    id: 'PROP-104',
                    society: 'Gulberg Greens',
                    category: 'Plot',
                    rate: 'PKR 2.20 Crore',
                    block: 'Block A',
                    contact: '+92 333 4567890',
                    plotNumber: 'Plot # 112',
                    size: '2 Kanal',
                    status: 'Available',
                    description: 'Scenic farm house style residential plot surrounded by lush greenery and wide roads.',
                    addedBy: 'Staff Admin'
                },
                {
                    id: 'PROP-105',
                    society: 'Bahria Town',
                    category: 'Shop',
                    rate: 'PKR 95 Lac',
                    block: 'Commercial Arena',
                    contact: '+92 321 9876543',
                    plotNumber: 'Shop # 24',
                    size: '272 Sq Ft',
                    status: 'Available',
                    description: 'Ground floor corner shop in bustling commercial arena with high pedestrian footfall.',
                    addedBy: 'Staff Admin'
                }
            ],
            societies: ['DHA Phase 6', 'Bahria Town', 'Gulberg Greens', 'Model Town Ext']
        };

        // --- TIME SIMULATOR ---
        function updateTime() {
            const now = new Date();
            let hours = now.getHours();
            let minutes = now.getMinutes();
            const ampm = hours >= 12 ? 'PM' : 'AM';
            hours = hours % 12;
            hours = hours ? hours : 12;
            minutes = minutes < 10 ? '0' + minutes : minutes;
            document.getElementById('current-time-display').innerText = `${hours}:${minutes} ${ampm}`;
        }
        setInterval(updateTime, 1000);
        updateTime();

        // --- NAVIGATION ROUTER ---
        function navigateTo(screenName) {
            appState.currentScreen = screenName;
            render();
            const viewport = document.getElementById('main-viewport');
            viewport.scrollTop = 0;
        }

        function showToast(message, isError = false) {
            const toast = document.getElementById('toast');
            const msgEl = document.getElementById('toast-message');
            const iconEl = document.getElementById('toast-icon');
            
            msgEl.innerText = message;
            if(isError) {
                iconEl.className = "fa-solid fa-circle-exclamation text-rose-400";
            } else {
                iconEl.className = "fa-solid fa-circle-check text-gold-400";
            }

            toast.classList.remove('opacity-0', 'translate-y-4', 'pointer-events-none');
            toast.classList.add('opacity-100', 'translate-y-0');

            setTimeout(() => {
                toast.classList.remove('opacity-100', 'translate-y-0');
                toast.classList.add('opacity-0', 'translate-y-4', 'pointer-events-none');
            }, 3000);
        }

        // --- RENDER CONTROLLER ---
        function render() {
            const viewport = document.getElementById('main-viewport');
            const roleBadge = document.getElementById('user-role-badge');
            
            // Update role badge in header
            if(appState.role === 'staff') {
                roleBadge.innerText = 'Staff Mode';
                roleBadge.className = "px-2 py-0.5 rounded-full text-[10px] bg-emerald-500/20 text-emerald-400 border border-emerald-500/40 uppercase tracking-wider font-bold";
            } else if(appState.role === 'visitor') {
                roleBadge.innerText = 'Visitor Mode';
                roleBadge.className = "px-2 py-0.5 rounded-full text-[10px] bg-gold-500/10 text-gold-400 border border-gold-500/30 uppercase tracking-wider font-bold";
            } else {
                roleBadge.innerText = 'Authentication';
                roleBadge.className = "px-2 py-0.5 rounded-full text-[10px] bg-slate-800 text-slate-400 border border-slate-700 uppercase tracking-wider font-bold";
            }

            switch(appState.currentScreen) {
                case 'login':
                    viewport.innerHTML = renderLoginScreen();
                    break;
                case 'dashboard':
                    viewport.innerHTML = renderDashboardScreen();
                    break;
                case 'addProperty':
                    viewport.innerHTML = renderAddPropertyScreen();
                    break;
                case 'visitProperty':
                    viewport.innerHTML = renderVisitPropertyScreen();
                    break;
                case 'propertyDetail':
                    viewport.innerHTML = renderPropertyDetailScreen();
                    break;
                default:
                    viewport.innerHTML = renderLoginScreen();
            }
        }

        // --- 1. LOGIN & VISITOR ENTRY SCREEN ---
        function renderLoginScreen() {
            return `
                <div class="flex-1 flex flex-col justify-between p-6 relative overflow-hidden bg-gradient-to-b from-emerald-950 via-slate-950 to-slate-950">
                    <!-- Background subtle graphic overlay -->
                    <div class="absolute inset-0 opacity-15 pointer-events-none bg-cover bg-center" style="background-image: url('https://images.unsplash.com/photo-1582407947304-fd86f028f716?auto=format&fit=crop&q=80&w=800');"></div>
                    <div class="absolute inset-0 bg-gradient-to-t from-slate-950 via-slate-950/80 to-emerald-950/40 pointer-events-none"></div>

                    <!-- Top Logo & Branding -->
                    <div class="relative z-10 flex flex-col items-center pt-8 text-center">
                        <div class="w-24 h-24 rounded-2xl bg-gradient-to-tr from-emerald-900 to-emerald-800 p-2 shadow-2xl border border-gold-500/30 flex items-center justify-center mb-4 transform hover:scale-105 transition-transform duration-300">
                            <img src="1000661512.png" alt="Al-Qadeer Logo" class="w-full h-full object-contain drop-shadow-md rounded-xl">
                        </div>
                        <h1 class="text-2xl font-extrabold text-slate-100 tracking-tight">Al-Qadeer <span class="text-gold-400">Estate Agency</span></h1>
                        <p class="text-xs text-slate-400 mt-1 uppercase tracking-widest font-semibold">Luxury Property Management</p>
                    </div>

                    <!-- Middle Login / Entry Options -->
                    <div class="relative z-10 my-auto space-y-5 w-full max-w-sm mx-auto">
                        
                        <!-- Staff Login Card -->
                        <div class="bg-emerald-900/40 backdrop-blur-xl border border-emerald-700/40 rounded-3xl p-5 card-shadow transition-all">
                            <div class="flex items-center space-x-3 mb-4">
                                <div class="w-10 h-10 rounded-xl bg-gold-500/20 text-gold-400 flex items-center justify-center">
                                    <i class="fa-solid fa-user-shield"></i>
                                </div>
                                <div>
                                    <h3 class="font-bold text-sm text-slate-200">Staff Portal</h3>
                                    <p class="text-[11px] text-slate-400">Inventory & management access</p>
                                </div>
                            </div>
                            <form onsubmit="handleStaffLogin(event)" class="space-y-3">
                                <div>
                                    <input type="email" id="staff-email" placeholder="Staff Email (e.g. staff@alqadeer.com)" required
                                        class="w-full bg-slate-900/80 border border-emerald-700/60 rounded-xl px-4 py-3 text-xs text-slate-200 placeholder-slate-500 focus:outline-none focus:border-gold-500 transition-colors">
                                </div>
                                <div>
                                    <input type="password" id="staff-pin" placeholder="PIN / Password (any)" required
                                        class="w-full bg-slate-900/80 border border-emerald-700/60 rounded-xl px-4 py-3 text-xs text-slate-200 placeholder-slate-500 focus:outline-none focus:border-gold-500 transition-colors">
                                </div>
                                <button type="submit" class="w-full gold-gradient text-slate-950 font-bold py-3 px-4 rounded-xl text-xs uppercase tracking-wider shadow-lg hover:brightness-105 active:scale-[0.98] transition-all flex items-center justify-center space-x-2">
                                    <span>Staff Login</span>
                                    <i class="fa-solid fa-arrow-right text-xs"></i>
                                </button>
                            </form>
                        </div>

                        <div class="flex items-center my-4">
                            <div class="flex-grow border-t border-slate-800"></div>
                            <span class="px-3 text-[11px] text-slate-500 uppercase tracking-widest font-semibold">OR</span>
                            <div class="flex-grow border-t border-slate-800"></div>
                        </div>

                        <!-- Visitor Mode Button -->
                        <button onclick="handleVisitorLogin()" class="w-full bg-slate-800/80 hover:bg-slate-800 text-slate-200 border border-slate-700/60 font-semibold py-4 px-6 rounded-3xl text-xs uppercase tracking-wider shadow-lg hover:border-slate-600 active:scale-[0.98] transition-all flex items-center justify-between group">
                            <div class="flex items-center space-x-3">
                                <div class="w-10 h-10 rounded-xl bg-slate-700/50 text-slate-300 flex items-center justify-center group-hover:text-gold-400 transition-colors">
                                    <i class="fa-solid fa-compass"></i>
                                </div>
                                <div class="text-left">
                                    <div class="font-bold text-slate-200">Continue as Visitor</div>
                                    <div class="text-[10px] text-slate-400 normal-case">Browse available inventory instantly</div>
                                </div>
                            </div>
                            <i class="fa-solid fa-chevron-right text-slate-500 group-hover:text-slate-300 transition-colors"></i>
                        </button>

                    </div>

                    <!-- Footer Info -->
                    <div class="relative z-10 text-center pb-4 text-[10px] text-slate-500">
                        &copy; 2026 Al-Qadeer Estate Agency. All rights reserved.
                    </div>
                </div>
            `;
        }

        function handleStaffLogin(e) {
            e.preventDefault();
            appState.role = 'staff';
            showToast('Welcome back, Staff Member!');
            navigateTo('dashboard');
        }

        function handleVisitorLogin() {
            appState.role = 'visitor';
            showToast('Welcome Guest! Browsing in Visitor Mode.');
            navigateTo('dashboard');
        }

        // --- 2. DASHBOARD SCREEN ---
        function renderDashboardScreen() {
            const totalProps = appState.properties.length;
            const availablePlots = appState.properties.filter(p => p.status === 'Available').length;
            const totalSocieties = [...new Set(appState.properties.map(p => p.society))].length;

            return `
                <div class="flex-1 flex flex-col bg-slate-950 pb-8">
                    <!-- Dashboard Header -->
                    <div class="bg-gradient-to-b from-emerald-950 to-slate-900 px-6 pt-6 pb-6 rounded-b-[2.5rem] border-b border-emerald-900/40 relative shadow-xl">
                        <div class="flex justify-between items-center mb-6">
                            <div class="flex items-center space-x-3">
                                <div class="w-12 h-12 rounded-2xl bg-gradient-to-tr from-emerald-900 to-emerald-800 p-1.5 shadow-lg border border-gold-500/30 flex items-center justify-center">
                                    <img src="1000661512.png" alt="Logo" class="w-full h-full object-contain rounded-xl">
                                </div>
                                <div>
                                    <h2 class="text-sm font-bold text-slate-100">Al-Qadeer Estate</h2>
                                    <p class="text-[11px] text-gold-400 font-medium">Inventory Control Hub</p>
                                </div>
                            </div>
                            <button onclick="handleLogout()" class="w-9 h-9 rounded-xl bg-slate-900/80 border border-slate-700 text-slate-400 hover:text-rose-400 hover:border-rose-500/50 flex items-center justify-center transition-colors text-xs" title="Switch User / Logout">
                                <i class="fa-solid fa-power-off"></i>
                            </button>
                        </div>

                        <!-- Stats Row -->
                        <div class="grid grid-cols-3 gap-3">
                            <div class="bg-emerald-900/30 border border-emerald-700/40 rounded-2xl p-3 text-center">
                                <div class="text-lg font-extrabold text-gold-400">${totalProps}</div>
                                <div class="text-[10px] text-slate-400 uppercase tracking-wider font-semibold mt-0.5">Properties</div>
                            </div>
                            <div class="bg-emerald-900/30 border border-emerald-700/40 rounded-2xl p-3 text-center">
                                <div class="text-lg font-extrabold text-emerald-400">${availablePlots}</div>
                                <div class="text-[10px] text-slate-400 uppercase tracking-wider font-semibold mt-0.5">Available</div>
                            </div>
                            <div class="bg-emerald-900/30 border border-emerald-700/40 rounded-2xl p-3 text-center">
                                <div class="text-lg font-extrabold text-slate-200">${totalSocieties}</div>
                                <div class="text-[10px] text-slate-400 uppercase tracking-wider font-semibold mt-0.5">Societies</div>
                            </div>
                        </div>
                    </div>

                    <!-- Action Cards / Navigation Buttons -->
                    <div class="px-6 mt-6 space-y-4">
                        <h3 class="text-xs uppercase tracking-widest text-slate-400 font-bold px-1">Main Actions</h3>

                        <!-- Add Property (Staff Only condition check) -->
                        ${appState.role === 'staff' ? `
                        <div onclick="navigateTo('addProperty')" class="group bg-gradient-to-r from-emerald-900/60 to-emerald-950 border border-emerald-600/40 hover:border-gold-500/60 rounded-3xl p-5 card-shadow cursor-pointer transition-all flex items-center justify-between">
                            <div class="flex items-center space-x-4">
                                <div class="w-14 h-14 rounded-2xl gold-gradient text-slate-950 flex items-center justify-center text-xl shadow-lg group-hover:scale-105 transition-transform">
                                    <i class="fa-solid fa-circle-plus"></i>
                                </div>
                                <div>
                                    <h4 class="font-bold text-base text-slate-100 group-hover:text-gold-400 transition-colors">Add Property</h4>
                                    <p class="text-xs text-slate-400 mt-0.5">List a new plot, house or shop</p>
                                </div>
                            </div>
                            <div class="w-8 h-8 rounded-full bg-emerald-800/50 flex items-center justify-center text-slate-300 group-hover:bg-gold-500 group-hover:text-slate-950 transition-all">
                                <i class="fa-solid fa-arrow-right text-xs"></i>
                            </div>
                        </div>
                        ` : `
                        <div onclick="showStaffRequiredMessage()" class="bg-slate-900/50 border border-slate-800 rounded-3xl p-5 opacity-60 cursor-pointer flex items-center justify-between">
                            <div class="flex items-center space-x-4">
                                <div class="w-14 h-14 rounded-2xl bg-slate-800 text-slate-500 flex items-center justify-center text-xl">
                                    <i class="fa-solid fa-lock"></i>
                                </div>
                                <div>
                                    <h4 class="font-bold text-base text-slate-400">Add Property (Staff Only)</h4>
                                    <p class="text-xs text-slate-500 mt-0.5">Login as staff to list new properties</p>
                                </div>
                            </div>
                            <i class="fa-solid fa-ban text-slate-600"></i>
                        </div>
                        `}

                        <!-- Visit Property Card -->
                        <div onclick="navigateTo('visitProperty')" class="group bg-gradient-to-r from-slate-900 to-slate-900/90 border border-slate-700/60 hover:border-emerald-500/60 rounded-3xl p-5 card-shadow cursor-pointer transition-all flex items-center justify-between">
                            <div class="flex items-center space-x-4">
                                <div class="w-14 h-14 rounded-2xl bg-gradient-to-br from-emerald-600 to-emerald-800 text-slate-100 flex items-center justify-center text-xl shadow-lg group-hover:scale-105 transition-transform">
                                    <i class="fa-solid fa-building-user"></i>
                                </div>
                                <div>
                                    <h4 class="font-bold text-base text-slate-100 group-hover:text-emerald-400 transition-colors">Visit Property</h4>
                                    <p class="text-xs text-slate-400 mt-0.5">Browse and filter active listings</p>
                                </div>
                            </div>
                            <div class="w-8 h-8 rounded-full bg-slate-800 flex items-center justify-center text-slate-300 group-hover:bg-emerald-500 group-hover:text-slate-950 transition-all">
                                <i class="fa-solid fa-arrow-right text-xs"></i>
                            </div>
                        </div>
                    </div>

                    <!-- Recent Inventory Preview -->
                    <div class="px-6 mt-8">
                        <div class="flex justify-between items-center mb-4 px-1">
                            <h3 class="text-xs uppercase tracking-widest text-slate-400 font-bold">Recent Listings</h3>
                            <button onclick="navigateTo('visitProperty')" class="text-xs text-gold-400 hover:underline font-semibold">View All (${totalProps})</button>
                        </div>
                        <div class="space-y-3">
                            ${appState.properties.slice(0, 3).map(prop => `
                                <div onclick="selectProperty('${prop.id}')" class="bg-slate-900/80 border border-slate-800 hover:border-gold-500/40 rounded-2xl p-4 cursor-pointer transition-all flex items-center justify-between group">
                                    <div class="flex items-center space-x-3">
                                        <div class="w-10 h-10 rounded-xl bg-emerald-950 border border-emerald-800 text-gold-400 flex items-center justify-center text-sm font-bold">
                                            ${prop.category === 'Plot' ? '<i class="fa-solid fa-map"></i>' : prop.category === 'House' ? '<i class="fa-solid fa-house"></i>' : '<i class="fa-solid fa-store"></i>'}
                                        </div>
                                        <div>
                                            <div class="font-bold text-xs text-slate-200 group-hover:text-gold-400 transition-colors">${prop.plotNumber} - ${prop.society}</div>
                                            <div class="text-[11px] text-slate-400 mt-0.5">${prop.block} &bull; <span class="text-emerald-400 font-semibold">${prop.rate}</span></div>
                                        </div>
                                    </div>
                                    <span class="text-[10px] px-2.5 py-1 rounded-full font-semibold ${prop.status === 'Available' ? 'bg-emerald-500/10 text-emerald-400 border border-emerald-500/30' : 'bg-amber-500/10 text-amber-400 border border-amber-500/30'}">
                                        ${prop.status}
                                    </span>
                                </div>
                            `).join('')}
                        </div>
                    </div>
                </div>
            `;
        }

        function handleLogout() {
            appState.role = null;
            showToast('Logged out successfully');
            navigateTo('login');
        }

        function showStaffRequiredMessage() {
            showToast('Staff login required to add properties!', true);
        }

        // --- 3. ADD PROPERTY PAGE (Staff Only) ---
        function renderAddPropertyScreen() {
            return `
                <div class="flex-1 flex flex-col bg-slate-950 pb-12">
                    <!-- Top Navigation Bar -->
                    <div class="bg-emerald-950/90 backdrop-blur-md px-6 py-4 flex items-center justify-between border-b border-emerald-900/50 sticky top-0 z-40">
                        <div class="flex items-center space-x-3">
                            <button onclick="navigateTo('dashboard')" class="w-9 h-9 rounded-xl bg-slate-900 border border-emerald-800 text-slate-300 flex items-center justify-center hover:bg-emerald-900 transition-colors">
                                <i class="fa-solid fa-arrow-left text-xs"></i>
                            </button>
                            <h2 class="text-sm font-bold text-slate-100">Add New Property</h2>
                        </div>
                        <div class="w-8 h-8 rounded-full bg-emerald-900/50 flex items-center justify-center text-gold-400 text-xs">
                            <i class="fa-solid fa-plus"></i>
                        </div>
                    </div>

                    <!-- Form Container -->
                    <div class="px-6 mt-6">
                        <form onsubmit="handleSaveProperty(event)" class="space-y-4">
                            
                            <!-- Society Name (Dropdown + Custom Option) -->
                            <div>
                                <label class="block text-[11px] font-bold uppercase tracking-wider text-slate-400 mb-1.5">Society Name *</label>
                                <select id="add-society" onchange="checkCustomSociety(this)" required
                                    class="w-full bg-slate-900 border border-slate-700/80 rounded-xl px-4 py-3 text-xs text-slate-200 focus:outline-none focus:border-gold-500">
                                    <option value="" disabled selected>Select Society</option>
                                    ${appState.societies.map(soc => `<option value="${soc}">${soc}</option>`).join('')}
                                    <option value="CUSTOM">+ Add New Society</option>
                                </select>
                            </div>

                            <!-- Custom Society Input (Hidden by default) -->
                            <div id="custom-society-container" class="hidden">
                                <label class="block text-[11px] font-bold uppercase tracking-wider text-slate-400 mb-1.5">Enter New Society Name *</label>
                                <input type="text" id="add-custom-society" placeholder="e.g. Al-Raziq Garden"
                                    class="w-full bg-slate-900 border border-slate-700/80 rounded-xl px-4 py-3 text-xs text-slate-200 placeholder-slate-500 focus:outline-none focus:border-gold-500">
                            </div>

                            <!-- Category -->
                            <div>
                                <label class="block text-[11px] font-bold uppercase tracking-wider text-slate-400 mb-1.5">Category *</label>
                                <select id="add-category" required
                                    class="w-full bg-slate-900 border border-slate-700/80 rounded-xl px-4 py-3 text-xs text-slate-200 focus:outline-none focus:border-gold-500">
                                    <option value="" disabled selected>Select Category</option>
                                    <option value="Plot">Plot</option>
                                    <option value="House">House</option>
                                    <option value="Commercial">Commercial</option>
                                    <option value="Shop">Shop</option>
                                </select>
                            </div>

                            <!-- Rate (Price) -->
                            <div>
                                <label class="block text-[11px] font-bold uppercase tracking-wider text-slate-400 mb-1.5">Rate / Price (PKR) *</label>
                                <input type="text" id="add-rate" placeholder="e.g. PKR 1.85 Crore or 85 Lac" required
                                    class="w-full bg-slate-900 border border-slate-700/80 rounded-xl px-4 py-3 text-xs text-slate-200 placeholder-slate-500 focus:outline-none focus:border-gold-500">
                            </div>

                            <!-- Block / Sector -->
                            <div>
                                <label class="block text-[11px] font-bold uppercase tracking-wider text-slate-400 mb-1.5">Block / Sector *</label>
                                <input type="text" id="add-block" placeholder="e.g. Sector J or Block A" required
                                    class="w-full bg-slate-900 border border-slate-700/80 rounded-xl px-4 py-3 text-xs text-slate-200 placeholder-slate-500 focus:outline-none focus:border-gold-500">
                            </div>

                            <!-- Contact Number -->
                            <div>
                                <label class="block text-[11px] font-bold uppercase tracking-wider text-slate-400 mb-1.5">Contact Number *</label>
                                <input type="text" id="add-contact" value="+92 300 1234567" required
                                    class="w-full bg-slate-900 border border-slate-700/80 rounded-xl px-4 py-3 text-xs text-slate-200 placeholder-slate-500 focus:outline-none focus:border-gold-500">
                            </div>

                            <!-- Optional: Plot Number -->
                            <div>
                                <label class="block text-[11px] font-bold uppercase tracking-wider text-slate-400 mb-1.5">Plot / Unit Number (Optional)</label>
                                <input type="text" id="add-plotNumber" placeholder="e.g. Plot # 45"
                                    class="w-full bg-slate-900 border border-slate-700/80 rounded-xl px-4 py-3 text-xs text-slate-200 placeholder-slate-500 focus:outline-none focus:border-gold-500">
                            </div>

                            <!-- Optional: Size -->
                            <div>
                                <label class="block text-[11px] font-bold uppercase tracking-wider text-slate-400 mb-1.5">Size (Optional)</label>
                                <input type="text" id="add-size" placeholder="e.g. 1 Kanal / 10 Marla"
                                    class="w-full bg-slate-900 border border-slate-700/80 rounded-xl px-4 py-3 text-xs text-slate-200 placeholder-slate-500 focus:outline-none focus:border-gold-500">
                            </div>

                            <!-- Optional: Status -->
                            <div>
                                <label class="block text-[11px] font-bold uppercase tracking-wider text-slate-400 mb-1.5">Status</label>
                                <select id="add-status"
                                    class="w-full bg-slate-900 border border-slate-700/80 rounded-xl px-4 py-3 text-xs text-slate-200 focus:outline-none focus:border-gold-500">
                                    <option value="Available">Available</option>
                                    <option value="Reserved">Reserved</option>
                                    <option value="Sold">Sold</option>
                                </select>
                            </div>

                            <!-- Optional: Description -->
                            <div>
                                <label class="block text-[11px] font-bold uppercase tracking-wider text-slate-400 mb-1.5">Short Description (Optional)</label>
                                <textarea id="add-description" rows="3" placeholder="Enter key selling points or features..."
                                    class="w-full bg-slate-900 border border-slate-700/80 rounded-xl px-4 py-3 text-xs text-slate-200 placeholder-slate-500 focus:outline-none focus:border-gold-500"></textarea>
                            </div>

                            <!-- Submit Button -->
                            <div class="pt-2">
                                <button type="submit" class="w-full gold-gradient text-slate-950 font-bold py-3.5 px-4 rounded-2xl text-xs uppercase tracking-wider shadow-lg hover:brightness-105 active:scale-[0.98] transition-all flex items-center justify-center space-x-2">
                                    <i class="fa-solid fa-floppy-disk"></i>
                                    <span>Save Property</span>
                                </button>
                            </div>
                        </form>
                    </div>
                </div>
            `;
        }

        function checkCustomSociety(selectEl) {
            const customContainer = document.getElementById('custom-society-container');
            if(selectEl.value === 'CUSTOM') {
                customContainer.classList.remove('hidden');
                document.getElementById('add-custom-society').setAttribute('required', 'true');
            } else {
                customContainer.classList.add('hidden');
                document.getElementById('add-custom-society').removeAttribute('required');
            }
        }

        function handleSaveProperty(e) {
            e.preventDefault();
            
            let society = document.getElementById('add-society').value;
            if(society === 'CUSTOM') {
                society = document.getElementById('add-custom-society').value.trim();
                if(society && !appState.societies.includes(society)) {
                    appState.societies.push(society);
                }
            }

            const category = document.getElementById('add-category').value;
            const rate = document.getElementById('add-rate').value;
            const block = document.getElementById('add-block').value;
            const contact = document.getElementById('add-contact').value;
            let plotNumber = document.getElementById('add-plotNumber').value.trim();
            if(!plotNumber) plotNumber = `${category} # ${Math.floor(Math.random() * 900) + 100}`;
            const size = document.getElementById('add-size').value || 'Standard Size';
            const status = document.getElementById('add-status').value;
            const description = document.getElementById('add-description').value || 'Prime real estate listing with excellent location and high potential.';

            const newProp = {
                id: 'PROP-' + (100 + appState.properties.length + 1),
                society,
                category,
                rate,
                block,
                contact,
                plotNumber,
                size,
                status,
                description,
                addedBy: appState.role === 'staff' ? 'Staff Member' : 'Visitor'
            };

            appState.properties.unshift(newProp);
            showToast('Property successfully added to inventory!');
            navigateTo('visitProperty');
        }

        // --- 4. VISIT PROPERTY PAGE ---
        function renderVisitPropertyScreen() {
            // Filter properties based on selected society and category
            let filtered = appState.properties;
            if(appState.selectedSociety) {
                filtered = filtered.filter(p => p.society === appState.selectedSociety);
            }
            if(appState.selectedCategory) {
                filtered = filtered.filter(p => p.category === appState.selectedCategory);
            }

            return `
                <div class="flex-1 flex flex-col bg-slate-950 pb-12">
                    <!-- Top Navigation Bar -->
                    <div class="bg-emerald-950/90 backdrop-blur-md px-6 py-4 flex items-center justify-between border-b border-emerald-900/50 sticky top-0 z-40">
                        <div class="flex items-center space-x-3">
                            <button onclick="navigateTo('dashboard')" class="w-9 h-9 rounded-xl bg-slate-900 border border-emerald-800 text-slate-300 flex items-center justify-center hover:bg-emerald-900 transition-colors">
                                <i class="fa-solid fa-arrow-left text-xs"></i>
                            </button>
                            <h2 class="text-sm font-bold text-slate-100">Visit & Browse Properties</h2>
                        </div>
                        <span class="text-[11px] bg-emerald-900/60 text-emerald-400 border border-emerald-700/50 px-2.5 py-1 rounded-full font-semibold">
                            ${filtered.length} found
                        </span>
                    </div>

                    <!-- Filter Controls -->
                    <div class="px-6 mt-5 space-y-3">
                        <div class="grid grid-cols-2 gap-3">
                            <!-- Select Society -->
                            <div>
                                <label class="block text-[10px] font-bold uppercase tracking-wider text-slate-400 mb-1">Society Name</label>
                                <select onchange="appState.selectedSociety = this.value; render();"
                                    class="w-full bg-slate-900 border border-slate-700/80 rounded-xl px-3 py-2.5 text-xs text-slate-200 focus:outline-none focus:border-gold-500">
                                    <option value="">All Societies</option>
                                    ${appState.societies.map(soc => `<option value="${soc}" ${appState.selectedSociety === soc ? 'selected' : ''}>${soc}</option>`).join('')}
                                </select>
                            </div>

                            <!-- Select Category -->
                            <div>
                                <label class="block text-[10px] font-bold uppercase tracking-wider text-slate-400 mb-1">Category</label>
                                <select onchange="appState.selectedCategory = this.value; render();"
                                    class="w-full bg-slate-900 border border-slate-700/80 rounded-xl px-3 py-2.5 text-xs text-slate-200 focus:outline-none focus:border-gold-500">
                                    <option value="">All Categories</option>
                                    <option value="Plot" ${appState.selectedCategory === 'Plot' ? 'selected' : ''}>Plot</option>
                                    <option value="House" ${appState.selectedCategory === 'House' ? 'selected' : ''}>House</option>
                                    <option value="Commercial" ${appState.selectedCategory === 'Commercial' ? 'selected' : ''}>Commercial</option>
                                    <option value="Shop" ${appState.selectedCategory === 'Shop' ? 'selected' : ''}>Shop</option>
                                </select>
                            </div>
                        </div>

                        ${(appState.selectedSociety || appState.selectedCategory) ? `
                        <div class="flex justify-between items-center pt-1 px-1">
                            <span class="text-[11px] text-slate-400">Filters active</span>
                            <button onclick="clearFilters()" class="text-[11px] text-gold-400 hover:underline font-semibold flex items-center space-x-1">
                                <i class="fa-solid fa-rotate-right text-[10px]"></i>
                                <span>Reset Filters</span>
                            </button>
                        </div>
                        ` : ''}
                    </div>

                    <!-- Property List -->
                    <div class="px-6 mt-4 space-y-3.5 flex-1">
                        ${filtered.length === 0 ? `
                            <div class="text-center py-16 bg-slate-900/40 border border-slate-800/80 rounded-3xl p-6 mt-4">
                                <div class="w-16 h-16 rounded-2xl bg-slate-800 text-slate-500 flex items-center justify-center mx-auto mb-3 text-xl">
                                    <i class="fa-solid fa-building-circle-xmark"></i>
                                </div>
                                <h4 class="font-bold text-sm text-slate-300">No properties matched</h4>
                                <p class="text-xs text-slate-500 mt-1">Try changing your society or category filter.</p>
                                <button onclick="clearFilters()" class="mt-4 px-4 py-2 rounded-xl bg-slate-800 text-gold-400 text-xs font-semibold border border-slate-700">Clear Filters</button>
                            </div>
                        ` : filtered.map(prop => `
                            <div onclick="selectProperty('${prop.id}')" class="bg-gradient-to-br from-slate-900 to-slate-900/90 border border-slate-800 hover:border-gold-500/50 rounded-2xl p-4 cursor-pointer transition-all card-shadow group">
                                <div class="flex justify-between items-start mb-2">
                                    <div class="flex items-center space-x-2.5">
                                        <div class="w-10 h-10 rounded-xl bg-emerald-950 border border-emerald-800/80 text-gold-400 flex items-center justify-center text-sm shadow-inner">
                                            ${prop.category === 'Plot' ? '<i class="fa-solid fa-map"></i>' : prop.category === 'House' ? '<i class="fa-solid fa-house"></i>' : prop.category === 'Commercial' ? '<i class="fa-solid fa-briefcase"></i>' : '<i class="fa-solid fa-store"></i>'}
                                        </div>
                                        <div>
                                            <span class="text-[10px] uppercase font-bold text-gold-400 tracking-wider">${prop.category} &bull; ${prop.size}</span>
                                            <h4 class="font-bold text-sm text-slate-100 group-hover:text-gold-400 transition-colors">${prop.plotNumber}</h4>
                                        </div>
                                    </div>
                                    <span class="text-[10px] px-2.5 py-1 rounded-full font-semibold ${prop.status === 'Available' ? 'bg-emerald-500/10 text-emerald-400 border border-emerald-500/30' : 'bg-amber-500/10 text-amber-400 border border-amber-500/30'}">
                                        ${prop.status}
                                    </span>
                                </div>
                                <div class="text-xs text-slate-300 font-medium mb-3 flex items-center space-x-2">
                                    <i class="fa-solid fa-location-dot text-emerald-500"></i>
                                    <span>${prop.society}, ${prop.block}</span>
                                </div>
                                <div class="flex justify-between items-center pt-3 border-t border-slate-800/80">
                                    <div class="text-xs font-extrabold text-emerald-400">${prop.rate}</div>
                                    <div class="text-[11px] text-slate-400 flex items-center space-x-1 group-hover:text-slate-200">
                                        <span>View Details</span>
                                        <i class="fa-solid fa-chevron-right text-[10px]"></i>
                                    </div>
                                </div>
                            </div>
                        `).join('')}
                    </div>
                </div>
            `;
        }

        function clearFilters() {
            appState.selectedSociety = '';
            appState.selectedCategory = '';
            render();
        }

        function selectProperty(propId) {
            appState.selectedProperty = appState.properties.find(p => p.id === propId);
            navigateTo('propertyDetail');
        }

        // --- 5. PROPERTY DETAIL / VISITOR VIEW PAGE ---
        function renderPropertyDetailScreen() {
            const prop = appState.selectedProperty;
            if(!prop) {
                navigateTo('visitProperty');
                return '';
            }

            return `
                <div class="flex-1 flex flex-col bg-slate-950 pb-12">
                    <!-- Top Navigation Bar -->
                    <div class="bg-emerald-950/90 backdrop-blur-md px-6 py-4 flex items-center justify-between border-b border-emerald-900/50 sticky top-0 z-40">
                        <div class="flex items-center space-x-3">
                            <button onclick="navigateTo('visitProperty')" class="w-9 h-9 rounded-xl bg-slate-900 border border-emerald-800 text-slate-300 flex items-center justify-center hover:bg-emerald-900 transition-colors">
                                <i class="fa-solid fa-arrow-left text-xs"></i>
                            </button>
                            <h2 class="text-sm font-bold text-slate-100">Property Overview</h2>
                        </div>
                        <div class="flex items-center space-x-2">
                            <button onclick="sharePropertyWhatsApp()" class="w-9 h-9 rounded-xl bg-emerald-600 text-slate-100 flex items-center justify-center hover:bg-emerald-500 transition-colors shadow-md text-xs" title="Share on WhatsApp">
                                <i class="fa-brands fa-whatsapp text-sm"></i>
                            </button>
                        </div>
                    </div>

                    <div class="px-6 mt-5 space-y-5">
                        
                        <!-- AI-Generated Style Shareable Banner / Card -->
                        <div id="shareable-banner" class="rounded-3xl overflow-hidden shadow-2xl border border-gold-500/40 relative bg-gradient-to-br from-emerald-950 via-emerald-900 to-slate-950 p-6 text-slate-100">
                            <!-- Background pattern/glow -->
                            <div class="absolute -right-10 -bottom-10 w-48 h-48 bg-gold-500/10 rounded-full blur-2xl pointer-events-none"></div>
                            <div class="absolute top-0 right-0 p-4 opacity-10 text-gold-400 text-7xl font-extrabold pointer-events-none">
                                <i class="fa-solid fa-building-columns"></i>
                            </div>

                            <!-- Header inside banner -->
                            <div class="flex justify-between items-center mb-6 relative z-10">
                                <div class="flex items-center space-x-2.5">
                                    <div class="w-9 h-9 rounded-xl bg-slate-950/80 p-1 border border-gold-500/40 flex items-center justify-center">
                                        <img src="1000661512.png" alt="Logo" class="w-full h-full object-contain rounded-lg">
                                    </div>
                                    <div>
                                        <div class="text-[10px] text-gold-400 font-bold uppercase tracking-widest">Al-Qadeer Estate</div>
                                        <div class="text-[9px] text-slate-300">Verified Listing</div>
                                    </div>
                                </div>
                                <span class="text-[10px] px-2.5 py-1 rounded-full font-bold uppercase tracking-wider ${prop.status === 'Available' ? 'bg-emerald-500/20 text-emerald-300 border border-emerald-400/40' : 'bg-amber-500/20 text-amber-300 border border-amber-400/40'}">
                                    ${prop.status}
                                </span>
                            </div>

                            <!-- Big Bold Plot Number & Details -->
                            <div class="relative z-10 my-4">
                                <div class="text-xs uppercase font-extrabold tracking-widest text-gold-400 mb-1">${prop.category} &bull; ${prop.size}</div>
                                <h3 class="text-3xl font-extrabold tracking-tight text-white mb-2">${prop.plotNumber}</h3>
                                <div class="flex items-center space-x-2 text-slate-300 text-xs font-medium">
                                    <div class="w-6 h-6 rounded-lg bg-gold-500/20 text-gold-400 flex items-center justify-center text-xs">
                                        <i class="fa-solid fa-location-dot"></i>
                                    </div>
                                    <span>${prop.society}, ${prop.block}</span>
                                </div>
                            </div>

                            <!-- Rate & Footer inside banner -->
                            <div class="mt-6 pt-4 border-t border-white/10 flex justify-between items-end relative z-10">
                                <div>
                                    <div class="text-[10px] uppercase text-slate-400 font-bold">Demand Price</div>
                                    <div class="text-lg font-extrabold text-gold-400">${prop.rate}</div>
                                </div>
                                <div class="text-right">
                                    <div class="text-[10px] uppercase text-slate-400 font-bold">Inquiry Contact</div>
                                    <div class="text-xs font-bold text-slate-200">${prop.contact}</div>
                                </div>
                            </div>
                        </div>

                        <!-- Big Green WhatsApp Share Button -->
                        <button onclick="sharePropertyWhatsApp()" class="w-full bg-emerald-600 hover:bg-emerald-500 text-slate-100 font-bold py-4 px-6 rounded-2xl text-xs uppercase tracking-wider shadow-xl active:scale-[0.98] transition-all flex items-center justify-center space-x-3 group">
                            <i class="fa-brands fa-whatsapp text-xl text-gold-300 group-hover:scale-110 transition-transform"></i>
                            <span>Share on WhatsApp</span>
                        </button>

                        <!-- Clean Property Details Card -->
                        <div class="bg-slate-900/90 border border-slate-800 rounded-3xl p-5 card-shadow space-y-4">
                            <h4 class="text-xs uppercase tracking-widest text-gold-400 font-bold">Property Specifications</h4>
                            
                            <div class="grid grid-cols-2 gap-3 text-xs">
                                <div class="bg-slate-950/60 p-3 rounded-2xl border border-slate-800">
                                    <div class="text-[10px] text-slate-400 uppercase font-semibold">Society</div>
                                    <div class="font-bold text-slate-200 mt-0.5">${prop.society}</div>
                                </div>
                                <div class="bg-slate-950/60 p-3 rounded-2xl border border-slate-800">
                                    <div class="text-[10px] text-slate-400 uppercase font-semibold">Block / Sector</div>
                                    <div class="font-bold text-slate-200 mt-0.5">${prop.block}</div>
                                </div>
                                <div class="bg-slate-950/60 p-3 rounded-2xl border border-slate-800">
                                    <div class="text-[10px] text-slate-400 uppercase font-semibold">Category & Size</div>
                                    <div class="font-bold text-slate-200 mt-0.5">${prop.category} (${prop.size})</div>
                                </div>
                                <div class="bg-slate-950/60 p-3 rounded-2xl border border-slate-800">
                                    <div class="text-[10px] text-slate-400 uppercase font-semibold">Listing ID</div>
                                    <div class="font-bold text-emerald-400 mt-0.5">${prop.id}</div>
                                </div>
                            </div>

                            <div>
                                <div class="text-[10px] text-slate-400 uppercase font-semibold mb-1">Description & Features</div>
                                <p class="text-xs text-slate-300 leading-relaxed bg-slate-950/60 p-3.5 rounded-2xl border border-slate-800">
                                    ${prop.description}
                                </p>
                            </div>

                            <div class="pt-2 flex items-center justify-between text-xs border-t border-slate-800">
                                <span class="text-slate-400">Listed by: <strong class="text-slate-200">${prop.addedBy || 'Staff'}</strong></span>
                                <a href="tel:${prop.contact}" class="text-gold-400 font-bold hover:underline flex items-center space-x-1">
                                    <i class="fa-solid fa-phone text-xs"></i>
                                    <span>Call Agent</span>
                                </a>
                            </div>
                        </div>

                    </div>
                </div>
            `;
        }

        function sharePropertyWhatsApp() {
            const prop = appState.selectedProperty;
            if(!prop) return;

            const message = encodeURIComponent(
                `🌟 *Al-Qadeer Estate Agency* 🌟\n\n` +
                `📌 *${prop.plotNumber}* (${prop.category})\n` +
                `📍 *Society:* ${prop.society} (${prop.block})\n` +
                `📏 *Size:* ${prop.size}\n` +
                `💰 *Rate:* ${prop.rate}\n` +
                `🔖 *Status:* ${prop.status}\n\n` +
                `📝 *Details:* ${prop.description}\n\n` +
                `📞 *Contact:* ${prop.contact}\n\n` +
                `_Powered by Al-Qadeer Estate Agency Inventory System_`
            );

            // Open WhatsApp with pre-filled message
            const whatsappUrl = `https://api.whatsapp.com/send?text=${message}`;
            window.open(whatsappUrl, '_blank');
            showToast('Opening WhatsApp with pre-filled property card!');
        }

        // Initialize App on load
        render();
    </script>
</body>
</html>

