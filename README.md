<!DOCTYPE html>
<html lang="de" class="h-full bg-slate-50">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Behörden-Helfer | Schnell, Einfach & Zuverlässig Anträge stellen</title>
    
    <!-- PWA Meta Tags -->
    <meta name="theme-color" content="#1e3a8a">
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    <meta name="apple-mobile-web-app-title" content="Behörden-Helfer">
    <meta name="description" content="Professionelle Ausfüllhilfe & Schreibservice für Behördenanträge. Wohngeld, Bürgergeld, Kindergeld & mehr. Festpreis 19,90 €">

    <!-- Embedded Manifest Data URI -->
    <link rel="manifest" href='data:application/manifest+json,{"name":"Behörden-Helfer","short_name":"BehördenHelfer","description":"Ausfüllhilfe & Schreibservice für Behördenanträge","start_url":"/","display":"standalone","background_color":"#f8fafc","theme_color":"#1e3a8a","icons":[{"src":"https://img.icons8.com/color/512/bureaucracy.png","sizes":"512x512","type":"image/png","purpose":"any maskable"}]}'>
    <link rel="apple-touch-icon" href="https://img.icons8.com/color/192/bureaucracy.png">

    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    colors: {
                        brand: {
                            50: '#eff6ff',
                            100: '#dbeafe',
                            500: '#3b82f6',
                            600: '#2563eb',
                            800: '#1e40af',
                            900: '#1e3a8a',
                        }
                    }
                }
            }
        }
    </script>
    <!-- Alpine.js CDN -->
    <script defer src="https://cdn.jsdelivr.net/npm/alpinejs@3.x.x/dist/cdn.min.js"></script>
    <!-- FontAwesome Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    
    <style>
        [x-cloak] { display: none !important; }
        html { scroll-behavior: smooth; }
    </style>
</head>
<body class="h-full text-slate-800 flex flex-col font-sans" x-data="app()" x-init="initApp()">

    <!-- PWA Installation Banner -->
    <div x-show="showInstallPrompt" x-cloak class="bg-brand-900 text-white px-4 py-3 flex items-center justify-between shadow-lg sticky top-0 z-50">
        <div class="flex items-center space-x-3">
            <i class="fa-solid fa-mobile-screen-button text-yellow-400 text-xl">
            <div>
                <p class="text-xs font-semibold text-brand-100">Als App installieren</p>
                <p class="text-sm font-medium">Behörden-Helfer auf dem Startbildschirm speichern</p>
            </div>
        </div>
        <div class="flex items-center space-x-2">
            <button @click="installPWA()" class="bg-yellow-400 text-slate-900 px-3 py-1 rounded text-xs font-bold hover:bg-yellow-300 transition">
                Installieren
            </button>
            <button @click="showInstallPrompt = false" class="text-slate-300 hover:text-white p-1">
                <i class="fa-solid fa-xmark">
            </button>
        </div>
    </div>

    <!-- Navigation Bar -->
    <header class="bg-white border-b border-slate-200 sticky top-0 z-40 shadow-sm">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
            <div class="flex justify-between h-16 items-center">
                <!-- Logo -->
                <div class="flex items-center space-x-3 cursor-pointer" @click="navigate('home')">
                    <div class="bg-brand-900 text-white p-2 rounded-lg shadow">
                        <i class="fa-solid fa-file-signature text-xl">
                    </div>
                    <div>
                        <span class="text-xl font-black tracking-tight text-brand-900 block leading-none">BEHÖRDEN-HELFER</span>
                        <span class="text-[10px] text-slate-500 font-semibold tracking-wider uppercase">Schreib- & Ausfüllservice</span>
                    </div>
                </div>

                <!-- Desktop Nav Navigation -->
                <nav class="hidden md:flex space-x-6 text-sm font-medium">
                    <button @click="navigate('home')" :class="currentPage === 'home' ? 'text-brand-600 font-bold' : 'text-slate-600 hover:text-brand-600'">Startseite</button>
                    <button @click="navigate('antrag')" :class="currentPage === 'antrag' ? 'text-brand-600 font-bold' : 'text-slate-600 hover:text-brand-600'">Antrag stellen</button>
                    <button @click="navigate('ablauf')" :class="currentPage === 'ablauf' ? 'text-brand-600 font-bold' : 'text-slate-600 hover:text-brand-600'">Ablauf</button>
                    <button @click="navigate('preise')" :class="currentPage === 'preise' ? 'text-brand-600 font-bold' : 'text-slate-600 hover:text-brand-600'">Preise</button>
                    <button @click="navigate('faq')" :class="currentPage === 'faq' ? 'text-brand-600 font-bold' : 'text-slate-600 hover:text-brand-600'">FAQ</button>
                    <button @click="navigate('kontakt')" :class="currentPage === 'kontakt' ? 'text-brand-600 font-bold' : 'text-slate-600 hover:text-brand-600'">Kontakt</button>
                </nav>

                <!-- Actions -->
                <div class="flex items-center space-x-3">
                    <button @click="isAdmin = !isAdmin" class="text-xs px-2.5 py-1.5 rounded-full border border-slate-300 font-medium transition" :class="isAdmin ? 'bg-amber-100 text-amber-800 border-amber-300' : 'bg-slate-100 text-slate-600 hover:bg-slate-200'">
                        <i class="fa-solid fa-user-shield mr-1"></i> <span x-text="isAdmin ? 'Admin Beenden' : 'Admin Mode'"></span>
                    </button>
                    
                    <button @click="navigate('antrag')" class="hidden sm:inline-flex items-center justify-center px-4 py-2 text-sm font-semibold text-white bg-brand-600 hover:bg-brand-800 rounded-lg shadow-sm transition">
                        Antrag starten (19,90€)
                    </button>

                    <!-- Mobile Menu Button -->
                    <button @click="mobileMenuOpen = !mobileMenuOpen" class="md:hidden p-2 rounded-md text-slate-600 hover:bg-slate-100">
                        <i class="fa-solid text-xl" :class="mobileMenuOpen ? 'fa-xmark' : 'fa-bars'">
                    </button>
                </div>
            </div>
        </div>

        <!-- Mobile Menu -->
        <div x-show="mobileMenuOpen" x-cloak class="md:hidden bg-white border-b border-slate-200 px-4 pt-2 pb-4 space-y-2">
            <button @click="navigate('home'); mobileMenuOpen=false" class="block w-full text-left py-2 text-slate-700 font-medium">Startseite</button>
            <button @click="navigate('antrag'); mobileMenuOpen=false" class="block w-full text-left py-2 text-brand-600 font-bold">Jetzt Antrag stellen</button>
            <button @click="navigate('ablauf'); mobileMenuOpen=false" class="block w-full text-left py-2 text-slate-700 font-medium">Ablauf</button>
            <button @click="navigate('preise'); mobileMenuOpen=false" class="block w-full text-left py-2 text-slate-700 font-medium">Preise & Leistungen</button>
            <button @click="navigate('faq'); mobileMenuOpen=false" class="block w-full text-left py-2 text-slate-700 font-medium">Häufige Fragen (FAQ)</button>
            <button @click="navigate('kontakt'); mobileMenuOpen=false" class="block w-full text-left py-2 text-slate-700 font-medium">Kontakt</button>
            <button @click="navigate('impressum'); mobileMenuOpen=false" class="block w-full text-left py-2 text-xs text-slate-500">Impressum</button>
            <button @click="navigate('datenschutz'); mobileMenuOpen=false" class="block w-full text-left py-2 text-xs text-slate-500">Datenschutz</button>
        </div>
    </header>

    <!-- ADMIN DASHBOARD BANNER IF ACTIVE -->
    <div x-show="isAdmin" x-cloak class="bg-amber-500 text-slate-900 px-4 py-2 text-sm font-medium flex justify-between items-center shadow-inner">
        <span><i class="fa-solid fa-triangle-exclamation mr-2"></i> <strong>Betreiber-Modus aktiv:</strong> Sie sehen eingegangene Kunden-Anträge live.</span>
        <button @click="navigate('admin')" class="bg-slate-900 text-white text-xs px-3 py-1 rounded shadow hover:bg-slate-800">
            Zum Admin-Dashboard
        </button>
    </div>

    <!-- MAIN CONTENT AREA -->
    <main class="flex-grow">

        <!-- ================= HOME PAGE ================= -->
        <div x-show="currentPage === 'home'">
            <!-- Hero Section -->
            <section class="bg-gradient-to-b from-brand-900 via-brand-800 to-slate-900 text-white py-16 md:py-24 px-4 relative overflow-hidden">
                <div class="max-w-5xl mx-auto text-center relative z-10">
                    <span class="inline-flex items-center gap-2 px-3 py-1 rounded-full text-xs font-semibold bg-brand-500/20 text-brand-100 border border-brand-400/30 mb-6">
                        <i class="fa-solid fa-shield-halved text-emerald-400"></i> Keine Behörden-Kopfschmerzen mehr
                    </span>
                    <h1 class="text-3xl md:text-5xl font-extrabold tracking-tight mb-6 leading-tight">
                        Wir übernehmen Ihren Behördenantrag.<br class="hidden md:inline"> Schnell, fehlerfrei & stressfrei.
                    </h1>
                    <p class="text-slate-300 text-lg md:text-xl max-w-2xl mx-auto mb-8 font-light">
                        Komplizierte Formulare? Keine Zeit für Papierkram? Füllen Sie in nur 3 Minuten unser einfaches Formular aus. Wir erledigen das Ausfüllen und Einreichen für Sie zum Festpreis.
                    </p>
                    
                    <div class="flex flex-col sm:flex-row justify-center gap-4 max-w-md mx-auto mb-10">
                        <button @click="navigate('antrag')" class="bg-emerald-500 hover:bg-emerald-600 text-slate-950 font-bold px-8 py-4 rounded-xl shadow-lg transition text-lg flex items-center justify-center gap-2">
                            <span>Jetzt Antrag stellen</span>
                            <i class="fa-solid fa-arrow-right"></i>
                        </button>
                        <button @click="navigate('ablauf')" class="bg-white/10 hover:bg-white/20 text-white font-semibold px-6 py-4 rounded-xl backdrop-blur-sm transition border border-white/20">
                            So funktioniert's
                        </button>
                    </div>

                    <!-- Trust Indicators -->
                    <div class="grid grid-cols-2 md:grid-cols-4 gap-4 max-w-3xl mx-auto pt-8 border-t border-white/10 text-xs text-slate-300">
                        <div class="flex items-center justify-center gap-2">
                            <i class="fa-solid fa-check text-emerald-400 text-lg"></i>
                            <span>19,90 € Festpreis</span>
                        </div>
                        <div class="flex items-center justify-center gap-2">
                            <i class="fa-solid fa-bolt text-amber-400 text-lg"></i>
                            <span>Bearbeitung in 24h</span>
                        </div>
                        <div class="flex items-center justify-center gap-2">
                            <i class="fa-solid fa-lock text-blue-400 text-lg"></i>
                            <span>100% DSGVO-konform</span>
                        </div>
                        <div class="flex items-center justify-center gap-2">
                            <i class="fa-solid fa-user-check text-purple-400 text-lg"></i>
                            <span>Erfahrene Prüfer</span>
                        </div>
                    </div>
                </div>
            </section>

            <!-- Popular Applications Selection Grid -->
            <section class="py-16 max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
                <div class="text-center mb-12">
                    <h2 class="text-2xl md:text-3xl font-bold text-slate-900">Welchen Antrag möchten Sie stellen?</h2>
                    <p class="text-slate-600 mt-2">Wählen Sie Ihr Anliegen aus – wir leiten Sie Schritt für Schritt durch das Formular.</p>
                </div>

                <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-6">
                    <template x-for="item in applicationTypes" :key="item.id">
                        <div @click="selectTypeAndStart(item.id)" class="bg-white p-6 rounded-2xl border border-slate-200 hover:border-brand-500 hover:shadow-md transition cursor-pointer flex flex-col justify-between group">
                            <div>
                                <div class="w-12 h-12 bg-brand-50 text-brand-600 rounded-xl flex items-center justify-center text-xl mb-4 group-hover:bg-brand-600 group-hover:text-white transition">
                                    <i :class="item.icon"></i>
                                </div>
                                <h3 class="font-bold text-lg text-slate-900 mb-1" x-text="item.title"></h3>
                                <p class="text-sm text-slate-600 mb-4" x-text="item.desc"></p>
                            </div>
                            <div class="flex items-center justify-between pt-4 border-t border-slate-100 text-xs font-semibold text-brand-600">
                                <span>Service-Gebühr: 19,90 €</span>
                                <span class="group-hover:translate-x-1 transition-transform">Auswählen &rarr;</span>
                            </div>
                        </div>
                    </template>
                </div>
            </section>

            <!-- Step by Step Section -->
            <section class="bg-slate-100 py-16 px-4">
                <div class="max-w-5xl mx-auto">
                    <div class="text-center mb-12">
                        <h2 class="text-2xl md:text-3xl font-bold text-slate-900">In 3 einfachen Schritten zum eingereichten Antrag</h2>
                    </div>
                    <div class="grid md:grid-cols-3 gap-8">
                        <div class="bg-white p-6 rounded-xl shadow-sm text-center relative">
                            <div class="w-10 h-10 bg-brand-900 text-white font-bold rounded-full flex items-center justify-center mx-auto mb-4">1</div>
                            <h3 class="font-bold mb-2">Angaben eintragen</h3>
                            <p class="text-sm text-slate-600">Füllen Sie unser einfaches Formular in wenigen Minuten online aus.</p>
                        </div>
                        <div class="bg-white p-6 rounded-xl shadow-sm text-center relative">
                            <div class="w-10 h-10 bg-brand-900 text-white font-bold rounded-full flex items-center justify-center mx-auto mb-4">2</div>
                            <h3 class="font-bold mb-2">Prüfung durch Experten</h3>
                            <p class="text-sm text-slate-600">Wir prüfen Ihre Angaben auf Vollständigkeit und bereiten die Unterlagen vor.</p>
                        </div>
                        <div class="bg-white p-6 rounded-xl shadow-sm text-center relative">
                            <div class="w-10 h-10 bg-brand-900 text-white font-bold rounded-full flex items-center justify-center mx-auto mb-4">3</div>
                            <h3 class="font-bold mb-2">Einreichung bei der Behörde</h3>
                            <p class="text-sm text-slate-600">Der fertig gestellte Antrag geht direkt an Ihre zuständige Behörde.</p>
                        </div>
                    </div>
                </div>
            </section>
        </div>

        <!-- ================= ANTRAG STELLEN (MULTI-STEP FORM) ================= -->
        <div x-show="currentPage === 'antrag'" class="py-10 max-w-3xl mx-auto px-4">
            <!-- Progress Bar -->
            <div class="mb-8">
                <div class="flex justify-between text-xs font-semibold text-slate-500 mb-2">
                    <span :class="formStep >= 1 ? 'text-brand-600' : ''">1. Antragsauswahl</span>
                    <span :class="formStep >= 2 ? 'text-brand-600' : ''">2. Persönliche Daten</span>
                    <span :class="formStep >= 3 ? 'text-brand-600' : ''">3. Nachweise & Details</span>
                    <span :class="formStep >= 4 ? 'text-brand-600' : ''">4. Abschluss & Bezahlung</span>
                </div>
                <div class="w-full bg-slate-200 h-2 rounded-full overflow-hidden">
                    <div class="bg-brand-600 h-full transition-all duration-300" :style="`width: ${(formStep / 4) * 100}%`"></div>
                </div>
            </div>

            <div class="bg-white rounded-2xl shadow-lg border border-slate-200 p-6 sm:p-8">
                
                <!-- STEP 1: Antragsart -->
                <div x-show="formStep === 1">
                    <h2 class="text-xl font-bold text-slate-900 mb-4">Schritt 1: Welchen Antrag möchten Sie stellen?</h2>
                    <div class="space-y-3 mb-6">
                        <template x-for="item in applicationTypes" :key="item.id">
                            <label class="flex items-center p-4 border rounded-xl cursor-pointer hover:bg-slate-50 transition" :class="formData.type === item.id ? 'border-brand-600 bg-brand-50/50 ring-2 ring-brand-500/20' : 'border-slate-200'">
                                <input type="radio" name="antragsart" :value="item.id" x-model="formData.type" class="text-brand-600 focus:ring-brand-500">
                                <div class="ml-4">
                                    <span class="font-bold text-slate-900 block" x-text="item.title"></span>
                                    <span class="text-xs text-slate-500" x-text="item.desc"></span>
                                </div>
                            </label>
                        </template>
                    </div>
                    <button @click="nextStep()" :disabled="!formData.type" class="w-full bg-brand-600 hover:bg-brand-800 disabled:opacity-50 text-white font-bold py-3 rounded-xl transition">
                        Weiter zu den persönlichen Daten
                    </button>
                </div>

                <!-- STEP 2: Personal Info -->
                <div x-show="formStep === 2" x-cloak>
                    <h2 class="text-xl font-bold text-slate-900 mb-4">Schritt 2: Ihre persönlichen Angaben</h2>
                    <div class="grid grid-cols-1 sm:grid-cols-2 gap-4 mb-6">
                        <div>
                            <label class="block text-xs font-semibold text-slate-700 mb-1">Vorname *</label>
                            <input type="text" x-model="formData.firstName" class="w-full p-3 border border-slate-300 rounded-lg text-sm focus:ring-2 focus:ring-brand-500 focus:outline-none">
                        </div>
                        <div>
                            <label class="block text-xs font-semibold text-slate-700 mb-1">Nachname *</label>
                            <input type="text" x-model="formData.lastName" class="w-full p-3 border border-slate-300 rounded-lg text-sm focus:ring-2 focus:ring-brand-500 focus:outline-none">
                        </div>
                        <div class="sm:col-span-2">
                            <label class="block text-xs font-semibold text-slate-700 mb-1">E-Mail-Adresse (für Bestätigung & Rückfragen) *</label>
                            <input type="email" x-model="formData.email" class="w-full p-3 border border-slate-300 rounded-lg text-sm focus:ring-2 focus:ring-brand-500 focus:outline-none">
                        </div>
                        <div>
                            <label class="block text-xs font-semibold text-slate-700 mb-1">Telefonnummer *</label>
                            <input type="tel" x-model="formData.phone" class="w-full p-3 border border-slate-300 rounded-lg text-sm focus:ring-2 focus:ring-brand-500 focus:outline-none">
                        </div>
                        <div>
                            <label class="block text-xs font-semibold text-slate-700 mb-1">Geburtsdatum</label>
                            <input type="date" x-model="formData.dob" class="w-full p-3 border border-slate-300 rounded-lg text-sm focus:ring-2 focus:ring-brand-500 focus:outline-none">
                        </div>
                        <div class="sm:col-span-2">
                            <label class="block text-xs font-semibold text-slate-700 mb-1">Straße & Hausnummer *</label>
                            <input type="text" x-model="formData.street" class="w-full p-3 border border-slate-300 rounded-lg text-sm focus:ring-2 focus:ring-brand-500 focus:outline-none">
                        </div>
                        <div>
                            <label class="block text-xs font-semibold text-slate-700 mb-1">PLZ *</label>
                            <input type="text" x-model="formData.zip" class="w-full p-3 border border-slate-300 rounded-lg text-sm focus:ring-2 focus:ring-brand-500 focus:outline-none">
                        </div>
                        <div>
                            <label class="block text-xs font-semibold text-slate-700 mb-1">Wohnort *</label>
                            <input type="text" x-model="formData.city" class="w-full p-3 border border-slate-300 rounded-lg text-sm focus:ring-2 focus:ring-brand-500 focus:outline-none">
                        </div>
                    </div>

                    <div class="flex gap-3">
                        <button @click="formStep = 1" class="w-1/3 bg-slate-200 hover:bg-slate-300 text-slate-700 font-bold py-3 rounded-xl transition">Zurück</button>
                        <button @click="nextStep()" class="w-2/3 bg-brand-600 hover:bg-brand-800 text-white font-bold py-3 rounded-xl transition">Weiter zu Schritt 3</button>
                    </div>
                </div>

                <!-- STEP 3: Details & Upload -->
                <div x-show="formStep === 3" x-cloak>
                    <h2 class="text-xl font-bold text-slate-900 mb-4">Schritt 3: Details & Unterlagen</h2>
                    
                    <div class="space-y-4 mb-6">
                        <div>
                            <label class="block text-xs font-semibold text-slate-700 mb-1">Zuständige Behörde / Stadtverwaltung (falls bekannt)</label>
                            <input type="text" x-model="formData.authority" placeholder="z. B. Stadt Dortmund / Jobcenter Bochum" class="w-full p-3 border border-slate-300 rounded-lg text-sm focus:ring-2 focus:ring-brand-500 focus:outline-none">
                        </div>

                        <div>
                            <label class="block text-xs font-semibold text-slate-700 mb-1">Wichtige Anmerkungen oder Besonderheiten</label>
                            <textarea x-model="formData.notes" rows="3" placeholder="Geben Sie hier Informationen an, die für Ihren Antrag wichtig sind..." class="w-full p-3 border border-slate-300 rounded-lg text-sm focus:ring-2 focus:ring-brand-500 focus:outline-none"></textarea>
                        </div>

                        <div>
                            <label class="block text-xs font-semibold text-slate-700 mb-1">Dokumente/Nachweise hochladen (Optional - Fotos/PDF)</label>
                            <div class="border-2 border-dashed border-slate-300 p-4 text-center rounded-xl hover:bg-slate-50 cursor-pointer">
                                <i class="fa-solid fa-cloud-arrow-up text-2xl text-slate-400 mb-1"></i>
                                <p class="text-xs text-slate-600">Klicken zum Hochladen von Personalausweis, Bescheiden etc.</p>
                                <input type="file" class="hidden" id="fileUpload" @change="formData.filesAttached = true">
                            </div>
                            <p x-show="formData.filesAttached" class="text-xs text-emerald-600 mt-1 font-semibold"><i class="fa-solid fa-circle-check"></i> Datei erfolgreich vorgemerkt</p>
                        </div>
                    </div>

                    <div class="flex gap-3">
                        <button @click="formStep = 2" class="w-1/3 bg-slate-200 hover:bg-slate-300 text-slate-700 font-bold py-3 rounded-xl transition">Zurück</button>
                        <button @click="nextStep()" class="w-2/3 bg-brand-600 hover:bg-brand-800 text-white font-bold py-3 rounded-xl transition">Weiter zur Übersicht</button>
                    </div>
                </div>

                <!-- STEP 4: Summary & Payment -->
                <div x-show="formStep === 4" x-cloak>
                    <h2 class="text-xl font-bold text-slate-900 mb-2">Schritt 4: Zusammenfassung & Auftragserteilung</h2>
                    <p class="text-xs text-slate-500 mb-6">Überprüfen Sie Ihre Daten vor dem Absenden.</p>

                    <div class="bg-slate-50 p-4 rounded-xl border border-slate-200 text-xs space-y-2 mb-6">
                        <p><strong>Antragsart:</strong> <span x-text="getTypeTitle(formData.type)"></span></p>
                        <p><strong>Name:</strong> <span x-text="formData.firstName + ' ' + formData.lastName"></span></p>
                        <p><strong>E-Mail:</strong> <span x-text="formData.email"></span></p>
                        <p><strong>Adresse:</strong> <span x-text="formData.street + ', ' + formData.zip + ' ' + formData.city"></span></p>
                    </div>

                    <div class="bg-brand-50 border border-brand-200 p-4 rounded-xl mb-6">
                        <div class="flex justify-between items-center mb-2">
                            <span class="font-bold text-brand-900">Ausfüllhilfe & Bearbeitungsservice</span>
                            <span class="font-extrabold text-lg text-brand-900">19,90 €</span>
                        </div>
                        <p class="text-[11px] text-slate-600">Einmaliger Festpreis inkl. Prüfung auf Vollständigkeit und digitaler Übermittlung an die Behörde. Keine Versteckten Abo-Kosten.</p>
                    </div>

                    <div class="space-y-3 mb-6">
                        <label class="flex items-start gap-2 cursor-pointer">
                            <input type="checkbox" x-model="formData.agreed" class="mt-1 rounded text-brand-600">
                            <span class="text-xs text-slate-600">
                                Ich bestätige die Richtigkeit meiner Angaben und beauftrage die Ausfüllhilfe gemäß den <button @click="navigate('datenschutz')" class="text-brand-600 underline">Datenschutzbestimmungen</button>. Mir ist bekannt, dass dieser Service keine Rechtsberatung ersetzt (§ 2 RDG).
                            </span>
                        </label>
                    </div>

                    <div class="flex gap-3">
                        <button @click="formStep = 3" class="w-1/3 bg-slate-200 hover:bg-slate-300 text-slate-700 font-bold py-3 rounded-xl transition">Zurück</button>
                        <button @click="submitOrder()" :disabled="!formData.agreed" class="w-2/3 bg-emerald-500 hover:bg-emerald-600 disabled:opacity-50 text-slate-950 font-bold py-3 rounded-xl shadow-lg transition">
                            Kostenpflichtig bestellen (19,90 €)
                        </button>
                    </div>
                </div>

                <!-- SUCCESS STATE -->
                <div x-show="formStep === 5" x-cloak class="text-center py-8">
                    <div class="w-16 h-16 bg-emerald-100 text-emerald-600 rounded-full flex items-center justify-center mx-auto mb-4 text-3xl">
                        <i class="fa-solid fa-circle-check"></i>
                    </div>
                    <h2 class="text-2xl font-bold text-slate-900 mb-2">Vielen Dank für Ihren Auftrag!</h2>
                    <p class="text-slate-600 text-sm max-w-md mx-auto mb-6">
                        Ihr Antrag wurde erfolgreich übermittelt. Wir haben Ihnen eine Bestätigung per E-Mail gesendet. Ihre Vorgangsnummer lautet: <strong class="text-brand-900" x-text="lastOrderId"></strong>
                    </p>

                    <div class="bg-amber-50 border border-amber-200 p-4 rounded-xl max-w-md mx-auto mb-6 text-left">
                        <h4 class="font-bold text-amber-900 text-xs uppercase tracking-wider mb-1">Zahlungsanweisung (19,90 €)</h4>
                        <p class="text-xs text-amber-800 mb-3">Bitte überweisen Sie die Servicegebühr per PayPal oder Banküberweisung unter Angabe der Vorgangsnummer:</p>
                        <a href="https://paypal.me" target="_blank" class="inline-flex items-center gap-2 bg-blue-600 text-white text-xs px-4 py-2 rounded font-bold hover:bg-blue-700">
                            <i class="fa-brands fa-paypal"></i> Jetzt per PayPal überweisen
                        </a>
                    </div>

                    <button @click="resetForm(); navigate('home')" class="bg-slate-900 text-white font-bold px-6 py-2.5 rounded-lg text-sm">
                        Zurück zur Startseite
                    </button>
                </div>

            </div>
        </div>

        <!-- ================= ABLAUF ================= -->
        <div x-show="currentPage === 'ablauf'" x-cloak class="py-12 max-w-4xl mx-auto px-4">
            <h1 class="text-3xl font-extrabold text-slate-900 mb-6 text-center">So einfach funktioniert der Behörden-Helfer</h1>
            <p class="text-slate-600 text-center max-w-2xl mx-auto mb-12">Wir nehmen Ihnen die Bürokratie ab, damit Sie sich um die wichtigen Dinge im Leben kümmern können.</p>

            <div class="space-y-8">
                <div class="flex gap-4 items-start">
                    <div class="bg-brand-900 text-white w-10 h-10 rounded-full flex items-center justify-center font-bold shrink-0">1</div>
                    <div>
                        <h3 class="font-bold text-lg text-slate-900">Formular online ausfüllen</h3>
                        <p class="text-sm text-slate-600 mt-1">Sie wählen den gewünschten Antrag (z. B. Wohngeld) und beantworten unsere einfachen Fragen auf Deutsch.</p>
                    </div>
                </div>
                <div class="flex gap-4 items-start">
                    <div class="bg-brand-900 text-white w-10 h-10 rounded-full flex items-center justify-center font-bold shrink-0">2</div>
                    <div>
                        <h3 class="font-bold text-lg text-slate-900">Unterlagen & Nachweise anhängen</h3>
                        <p class="text-sm text-slate-600 mt-1">Laden Sie Fotos oder Dokumente direkt mit dem Smartphone hoch (z. B. Einkommensnachweise oder Personalausweis).</p>
                    </div>
                </div>
                <div class="flex gap-4 items-start">
                    <div class="bg-brand-900 text-white w-10 h-10 rounded-full flex items-center justify-center font-bold shrink-0">3</div>
                    <div>
                        <h3 class="font-bold text-lg text-slate-900">Prüfung & Fertigstellung</h3>
                        <p class="text-sm text-slate-600 mt-1">Ein geschulter Bearbeiter prüft Ihre Unterlagen auf Vollständigkeit und füllt die offiziellen Behördenformulare aus.</p>
                    </div>
                </div>
                <div class="flex gap-4 items-start">
                    <div class="bg-brand-900 text-white w-10 h-10 rounded-full flex items-center justify-center font-bold shrink-0">4</div>
                    <div>
                        <h3 class="font-bold text-lg text-slate-900">Einreichung & Bestätigung</h3>
                        <p class="text-sm text-slate-600 mt-1">Wir übermitteln den Antrag an Ihre zuständige Behörde. Sie erhalten eine Nachweisbestätigung für Ihre Unterlagen.</p>
                    </div>
                </div>
            </div>

            <div class="mt-12 text-center">
                <button @click="navigate('antrag')" class="bg-brand-600 hover:bg-brand-800 text-white font-bold px-8 py-3 rounded-xl transition">
                    Jetzt Antrag stellen
                </button>
            </div>
        </div>

        <!-- ================= PREISE ================= -->
        <div x-show="currentPage === 'preise'" x-cloak class="py-12 max-w-4xl mx-auto px-4">
            <h1 class="text-3xl font-extrabold text-slate-900 mb-4 text-center">Transparente Preise. Keine Abos.</h1>
            <p class="text-slate-600 text-center max-w-xl mx-auto mb-10">Sie zahlen nur für die Ausfüllhilfe, die Sie wirklich in Anspruch nehmen.</p>

            <div class="bg-white border-2 border-brand-600 rounded-2xl shadow-xl max-w-md mx-auto overflow-hidden">
                <div class="bg-brand-900 text-white p-6 text-center">
                    <span class="text-xs uppercase font-bold tracking-widest text-brand-100">Rundum-Sorglos-Ausfüllhilfe</span>
                    <div class="text-4xl font-extrabold my-2">19,90 €</div>
                    <span class="text-xs text-slate-300">Einmaliger Festpreis pro Antrag</span>
                </div>
                <div class="p-6 space-y-4 text-sm text-slate-700">
                    <div class="flex items-center gap-3"><i class="fa-solid fa-check text-emerald-500"></i> Erfassung aller notwendigen Angaben</div>
                    <div class="flex items-center gap-3"><i class="fa-solid fa-check text-emerald-500"></i> Prüfung auf Plausibilität & Vollständigkeit</div>
                    <div class="flex items-center gap-3"><i class="fa-solid fa-check text-emerald-500"></i> Übertragung in offizielle Behörden-Formulare</div>
                    <div class="flex items-center gap-3"><i class="fa-solid fa-check text-emerald-500"></i> Versand / Übermittlung an die zuständige Behörde</div>
                    <div class="flex items-center gap-3"><i class="fa-solid fa-check text-emerald-500"></i> Eingangsbestätigung per E-Mail</div>
                </div>
                <div class="p-6 bg-slate-50 border-t border-slate-100">
                    <button @click="navigate('antrag')" class="w-full bg-brand-600 hover:bg-brand-800 text-white font-bold py-3 rounded-xl transition text-center">
                        Jetzt Ausfüllhilfe starten
                    </button>
                </div>
            </div>
        </div>

        <!-- ================= FAQ ================= -->
        <div x-show="currentPage === 'faq'" x-cloak class="py-12 max-w-3xl mx-auto px-4">
            <h1 class="text-3xl font-extrabold text-slate-900 mb-8 text-center">Häufig gestellte Fragen (FAQ)</h1>
            
            <div class="space-y-4">
                <div class="bg-white p-5 rounded-xl border border-slate-200">
                    <h3 class="font-bold text-slate-900 mb-2">Bieten Sie eine Rechtsberatung an?</h3>
                    <p class="text-sm text-slate-600">Nein. Wir sind ein reiner Schreib- und Ausfüllservice gem. § 2 RDG. Wir unterstützen Sie beim Ausfüllen der Formulare und der Übermittlung. Für rechtliche Beratungen wenden Sie sich bitte an Anwälte oder Sozialverbände.</p>
                </div>
                <div class="bg-white p-5 rounded-xl border border-slate-200">
                    <h3 class="font-bold text-slate-900 mb-2">Wie lange dauert die Bearbeitung?</h3>
                    <p class="text-sm text-slate-600">Nach Eingang Ihrer Daten und der Servicegebühr bearbeiten wir Ihren Antrag in der Regel innerhalb von 24 Stunden an Werktagen.</p>
                </div>
                <div class="bg-white p-5 rounded-xl border border-slate-200">
                    <h3 class="font-bold text-slate-900 mb-2">Was passiert mit meinen Daten?</h3>
                    <p class="text-sm text-slate-600">Ihre Daten werden nach strengen DSGVO-Richtlinien verschlüsselt verarbeitet und ausschließlich zur Erstellung Ihres Antrags verwendet.</p>
                </div>
            </div>
        </div>

        <!-- ================= KONTAKT ================= -->
        <div x-show="currentPage === 'kontakt'" x-cloak class="py-12 max-w-xl mx-auto px-4">
            <h1 class="text-3xl font-extrabold text-slate-900 mb-4 text-center">Kontakt aufnehmen</h1>
            <p class="text-slate-600 text-center mb-8 text-sm">Haben Sie Fragen zu Ihrem Antrag oder unserem Service? Schreiben Sie uns.</p>

            <form @submit.prevent="alert('Vielen Dank! Ihre Nachricht wurde gesendet.')" class="bg-white p-6 rounded-2xl border border-slate-200 space-y-4">
                <div>
                    <label class="block text-xs font-semibold text-slate-700 mb-1">Ihr Name</label>
                    <input type="text" required class="w-full p-3 border border-slate-300 rounded-lg text-sm">
                </div>
                <div>
                    <label class="block text-xs font-semibold text-slate-700 mb-1">Ihre E-Mail-Adresse</label>
                    <input type="email" required class="w-full p-3 border border-slate-300 rounded-lg text-sm">
                </div>
                <div>
                    <label class="block text-xs font-semibold text-slate-700 mb-1">Nachricht</label>
                    <textarea rows="4" required class="w-full p-3 border border-slate-300 rounded-lg text-sm"></textarea>
                </div>
                <button type="submit" class="w-full bg-brand-600 hover:bg-brand-800 text-white font-bold py-3 rounded-xl transition">
                    Nachricht absenden
                </button>
            </form>
        </div>

        <!-- ================= IMPRESSUM ================= -->
        <div x-show="currentPage === 'impressum'" x-cloak class="py-12 max-w-3xl mx-auto px-4 text-sm text-slate-700">
            <h1 class="text-2xl font-bold mb-4 text-slate-900">Impressum</h1>
            <p class="mb-2"><strong>Angaben gemäß § 5 TMG:</strong></p>
            <p class="mb-4">
                Behörden-Helfer Service<br>
                Musterstraße 123<br>
                44135 Dortmund<br>
                Deutschland
            </p>
            <p class="mb-2"><strong>Kontakt:</strong></p>
            <p class="mb-4">
                E-Mail: info@behoerden-helfer-service.de
            </p>
            <p class="mb-2"><strong>Dienstleistungshinweis:</strong></p>
            <p class="text-xs text-slate-500">
                Der Dienstleister erbringt ausschließlich Schreib- und Ausfüllhilfen im Sinne des § 2 Abs. 3 RDG (Rechtsdienstleistungsgesetz) und führt keine Rechtsberatung durch.
            </p>
        </div>

        <!-- ================= DATENSCHUTZ ================= -->
        <div x-show="currentPage === 'datenschutz'" x-cloak class="py-12 max-w-3xl mx-auto px-4 text-sm text-slate-700">
            <h1 class="text-2xl font-bold mb-4 text-slate-900">Datenschutzerklärung</h1>
            <p class="mb-4">Wir nehmen den Schutz Ihrer persönlichen Daten sehr ernst. Nachfolgend informieren wir Sie über die Verarbeitung personenbezogener Daten bei Nutzung unserer Website.</p>
            <h3 class="font-bold mb-2"> Datenerhebung</h3>
            <p class="mb-4">Wir erheben Daten, die Sie uns im Rahmen der Antrags-Ausfüllhilfe zur Verfügung stellen (z. B. Name, Adresse, Formulardaten).</p>
        </div>

        <!-- ================= ADMIN DASHBOARD ================= -->
        <div x-show="currentPage === 'admin'" x-cloak class="py-8 max-w-6xl mx-auto px-4">
            <div class="flex justify-between items-center mb-6">
                <div>
                    <h1 class="text-2xl font-bold text-slate-900">Betreiber-Dashboard</h1>
                    <p class="text-xs text-slate-500">Übersicht aller eingegangenen Anträge</p>
                </div>
                <div class="bg-emerald-100 text-emerald-800 px-3 py-1 rounded-full text-xs font-bold">
                    Einnahmen: <span x-text="orders.length * 19.9"></span>,00 €
                </div>
            </div>

            <div class="bg-white rounded-xl shadow border border-slate-200 overflow-hidden">
                <table class="w-full text-left text-xs">
                    <thead class="bg-slate-100 text-slate-700 uppercase font-semibold">
                        <tr>
                            <th class="p-3">Vorgangs-ID</th>
                            <th class="p-3">Kunde</th>
                            <th class="p-3">Antrag</th>
                            <th class="p-3">Status</th>
                            <th class="p-3">Datum</th>
                        </tr>
                    </thead>
                    <tbody class="divide-y divide-slate-200">
                        <template x-for="ord in orders" :key="ord.id">
                            <tr class="hover:bg-slate-50">
                                <td class="p-3 font-mono font-bold text-brand-900" x-text="ord.id"></td>
                                <td class="p-3">
                                    <div class="font-bold" x-text="ord.name"></div>
                                    <div class="text-[10px] text-slate-500" x-text="ord.email"></div>
                                </td>
                                <td class="p-3 font-semibold" x-text="ord.type"></td>
                                <td class="p-3">
                                    <span class="px-2 py-0.5 rounded text-[10px] font-bold bg-amber-100 text-amber-800">Eingegangen</span>
                                </td>
                                <td class="p-3 text-slate-500" x-text="ord.date"></td>
                            </tr>
                        </template>
                        <tr x-show="orders.length === 0">
                            <td colspan="5" class="p-6 text-center text-slate-400">Noch keine Anträge vorhanden. Testen Sie das Formular selbst!</td>
                        </tr>
                    </tbody>
                </table>
            </div>
        </div>

    </main>

    <!-- FOOTER -->
    <footer class="bg-slate-900 text-slate-400 text-xs py-10 border-t border-slate-800">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
            <div class="grid grid-cols-1 md:grid-cols-4 gap-8 mb-8">
                <div>
                    <span class="text-white font-bold text-sm block mb-2">BEHÖRDEN-HELFER</span>
                    <p class="text-slate-400 text-[11px]">Ihr zuverlässiger Partner für Ausfüllhilfen & Formulardienste in Deutschland.</p>
                </div>
                <div>
                    <span class="text-white font-bold text-xs uppercase block mb-2">Navigation</span>
                    <ul class="space-y-1">
                        <li><button @click="navigate('home')" class="hover:text-white">Startseite</button></li>
                        <li><button @click="navigate('antrag')" class="hover:text-white">Antrag stellen</button></li>
                        <li><button @click="navigate('ablauf')" class="hover:text-white">Ablauf</button></li>
                        <li><button @click="navigate('preise')" class="hover:text-white">Preise</button></li>
                    </ul>
                </div>
                <div>
                    <span class="text-white font-bold text-xs uppercase block mb-2">Rechtliches</span>
                    <ul class="space-y-1">
                        <li><button @click="navigate('impressum')" class="hover:text-white">Impressum</button></li>
                        <li><button @click="navigate('datenschutz')" class="hover:text-white">Datenschutzerklärung</button></li>
                    </ul>
                </div>
                <div>
                    <span class="text-white font-bold text-xs uppercase block mb-2">Rechtlicher Hinweis</span>
                    <p class="text-[10px] text-slate-500">Wir bieten Schreib- und Ausfüllhilfen gem. § 2 RDG an. Keine Rechtsberatung.</p>
                </div>
            </div>
            <div class="border-t border-slate-800 pt-6 text-center text-slate-600 text-[11px]">
                &copy; 2026 Behörden-Helfer Service. Alle Rechte vorbehalten.
            </div>
        </div>
    </footer>

    <!-- ALPINE.JS APP LOGIC & PWA REGISTRATION -->
    <script>
        function app() {
            return {
                currentPage: 'home',
                mobileMenuOpen: false,
                isAdmin: false,
                formStep: 1,
                showInstallPrompt: false,
                deferredPrompt: null,
                lastOrderId: '',

                applicationTypes: [
                    { id: 'wohngeld', title: 'Wohngeldantrag', desc: 'Zuschuss zur Miete oder zum Eigenheim.', icon: 'fa-solid fa-house-user' },
                    { id: 'buergergeld', title: 'Bürgergeld (Jobcenter)', desc: 'Erst- oder Weiterbewilligungsantrag.', icon: 'fa-solid fa-hand-holding-hand' },
                    { id: 'kindergeld', title: 'Kindergeld & Kinderzuschlag', desc: 'Finanzielle Unterstützung für Familien.', icon: 'fa-solid fa-child-reaching' },
                    { id: 'ummeldung', title: 'Wohnsitz-Ummeldung', desc: 'Anmeldung bei der Meldebehörde/Bürgeramt.', icon: 'fa-solid fa-id-card' },
                    { id: 'elterngeld', title: 'Elterngeldantrag', desc: 'Elterngeld nach der Geburt beantragen.', icon: 'fa-solid fa-baby' },
                    { id: 'gewerbe', title: 'Gewerbeanmeldung', desc: 'Anmeldung eines Kleingewerbes oder Betriebs.', icon: 'fa-solid fa-briefcase' }
                ],

                formData: {
                    type: '',
                    firstName: '',
                    lastName: '',
                    email: '',
                    phone: '',
                    dob: '',
                    street: '',
                    zip: '',
                    city: '',
                    authority: '',
                    notes: '',
                    filesAttached: false,
                    agreed: false
                },

                orders: [
                    { id: '#BEH-2026-8921', name: 'Max Mustermann', email: 'max@example.com', type: 'Wohngeldantrag', date: '16.10.2026' }
                ],

                initApp() {
                    // PWA Installation Listener
                    window.addEventListener('beforeinstallprompt', (e) => {
                        e.preventDefault();
                        this.deferredPrompt = e;
                        this.showInstallPrompt = true;
                    });

                    // Register Service Worker from Blob
                    if ('serviceWorker' in navigator) {
                        const swCode = `
                            self.addEventListener('install', e => self.skipWaiting());
                            self.addEventListener('fetch', e => e.respondWith(fetch(e.request)));
                        `;
                        const blob = new Blob([swCode], { type: 'application/javascript' });
                        const swUrl = URL.createObjectURL(blob);
                        navigator.serviceWorker.register(swUrl).catch(() => {});
                    }
                },

                installPWA() {
                    if (this.deferredPrompt) {
                        this.deferredPrompt.prompt();
                        this.deferredPrompt.userChoice.then(() => {
                            this.showInstallPrompt = false;
                        });
                    }
                },

                navigate(page) {
                    this.currentPage = page;
                    window.scrollTo({ top: 0, behavior: 'smooth' });
                },

                selectTypeAndStart(typeId) {
                    this.formData.type = typeId;
                    this.formStep = 2;
                    this.navigate('antrag');
                },

                nextStep() {
                    if (this.formStep < 4) {
                        this.formStep++;
                        window.scrollTo({ top: 0, behavior: 'smooth' });
                    }
                },

                getTypeTitle(typeId) {
                    const found = this.applicationTypes.find(a => a.id === typeId);
                    return found ? found.title : 'Sonstiger Antrag';
                },

                submitOrder() {
                    const randomNum = Math.floor(1000 + Math.random() * 9000);
                    this.lastOrderId = `#BEH-2026-${randomNum}`;
                    
                    // Add order to admin list
                    this.orders.unshift({
                        id: this.lastOrderId,
                        name: `${this.formData.firstName} ${this.formData.lastName}`,
                        email: this.formData.email,
                        type: this.getTypeTitle(this.formData.type),
                        date: new Date().toLocaleDateString('de-DE')
                    });

                    this.formStep = 5;
                },

                resetForm() {
                    this.formData = {
                        type: '', firstName: '', lastName: '', email: '', phone: '',
                        dob: '', street: '', zip: '', city: '', authority: '', notes: '',
                        filesAttached: false, agreed: false
                    };
                    this.formStep = 1;
                }
            }
        }
    </script>
</body>
</html># antrag-stellen
