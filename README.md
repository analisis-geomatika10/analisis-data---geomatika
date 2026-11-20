# analisis-data-&-geomatika
websites untuk membantu anda mengukur lahan dan membantu menganalisis data,menghitung,dan mengetahui lokasi pengukuran
<!DOCTYPE html>
<html lang="id" class="scroll-smooth">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Analisis Lahan & Geomatika</title>
    
    <!-- Google Fonts: Inter & JetBrains Mono untuk angka teknis -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800&family=JetBrains+Mono:wght@400;600&display=swap" rel="stylesheet">
    
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    
    <!-- FontAwesome Icons CDN -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    
    <!-- Leaflet.js CSS & JS CDN -->
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" integrity="sha256-p4NxAoJBhIIN+hmNHrzRCf9tD/miZyoHS5obTRR9BMY=" crossorigin="" />
    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js" integrity="sha256-20nQCchB9co0qIjJZRGuk2/Z9VM+kNiyxNV1lvTlZBo=" crossorigin=""></script>

    <script>
        tailwind.config = {
            theme: {
                extend: {
                    fontFamily: {
                        sans: ['Inter', 'sans-serif'],
                        mono: ['JetBrains Mono', 'monospace'],
                    },
                    colors: {
                        forest: {
                            800: '#143624',
                            900: '#0b2216',
                        },
                        earth: {
                            500: '#9c6644',
                            700: '#583822',
                            900: '#2b1b10',
                        }
                    }
                }
            }
        }
    </script>

    <style>
        /* Latar belakang samar bertema tanah & hutan bumi dengan overlay gradien gelap hijau-coklat */
        body {
            background-color: #0d1f16;
            background-image: 
                linear-gradient(145deg, rgba(11, 34, 22, 0.88) 0%, rgba(24, 38, 26, 0.85) 50%, rgba(43, 27, 16, 0.90) 100%),
                url('https://images.unsplash.com/photo-1511497584788-876760111969?auto=format&fit=crop&w=1920&q=80');
            background-size: cover;
            background-position: center;
            background-attachment: fixed;
            color: #f1f5f9;
        }

        /* Kartu transparan elegan (Glassmorphism) */
        .geo-card {
            background: rgba(15, 29, 23, 0.78);
            backdrop-filter: blur(12px);
            -webkit-backdrop-filter: blur(12px);
            border: 1px solid rgba(16, 185, 129, 0.22);
            box-shadow: 0 12px 32px -8px rgba(0, 0, 0, 0.55);
        }

        .geo-subcard {
            background: rgba(10, 20, 16, 0.65);
            border: 1px solid rgba(255, 255, 255, 0.08);
        }

        /* Animasi masuk halus */
        @keyframes fadeSlideUp {
            from { opacity: 0; transform: translateY(12px); }
            to { opacity: 1; transform: translateY(0); }
        }
        .animate-reveal {
            animation: fadeSlideUp 0.4s ease-out forwards;
        }

        /* Mencegah scroll halaman saat menggambar di kanvas pada perangkat sentuh */
        .touch-none-canvas {
            touch-action: none;
        }

        /* Scrollbar halus */
        ::-webkit-scrollbar {
            width: 6px;
            height: 6px;
        }
        ::-webkit-scrollbar-track {
            background: rgba(15, 23, 42, 0.4);
        }
        ::-webkit-scrollbar-thumb {
            background: rgba(16, 185, 129, 0.4);
            border-radius: 9999px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: rgba(16, 185, 129, 0.7);
        }
    </style>
</head>
<body class="min-h-screen flex flex-col antialiased selection:bg-emerald-500 selection:text-white">

    <!-- Toast Notification Container (Pengganti alert/confirm) -->
    <div id="toastContainer" class="fixed bottom-5 right-5 z-50 flex flex-col gap-2 max-w-sm pointer-events-none"></div>

    <!-- Compact Top Navbar -->
    <header class="sticky top-0 z-40 bg-[#0a1c13]/90 backdrop-blur-md border-b border-emerald-500/20 shadow-lg">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-3 flex flex-wrap items-center justify-between gap-3">
            <div class="flex items-center gap-3">
                <div class="w-10 h-10 rounded-xl bg-gradient-to-br from-emerald-500 to-amber-700 flex items-center justify-center text-white shadow-md">
                    <i class="fas fa-compass-drafting text-lg"></i>
                </div>
                <div>
                    <h1 class="text-lg sm:text-xl font-bold tracking-tight text-white leading-tight">GeoSurvey & EcoBuild</h1>
                    <p class="text-xs text-emerald-300/80">Sistem Analisis Lahan, Geomatika & Rekayasa Ukur</p>
                </div>
            </div>

            <!-- Quick Navigation Pills -->
            <nav class="flex items-center gap-1.5 overflow-x-auto max-w-full pb-1 sm:pb-0 text-xs font-medium">
                <a href="#modul-analisis" class="px-3 py-1.5 rounded-lg bg-emerald-950/80 hover:bg-emerald-700/60 text-emerald-200 border border-emerald-500/20 transition whitespace-nowrap">
                    <i class="fas fa-layer-group mr-1"></i> Analisis Lahan
                </a>
                <a href="#modul-denah" class="px-3 py-1.5 rounded-lg bg-emerald-950/80 hover:bg-emerald-700/60 text-emerald-200 border border-emerald-500/20 transition whitespace-nowrap">
                    <i class="fas fa-drafting-compass mr-1"></i> Denah 2D
                </a>
                <a href="#modul-kamera" class="px-3 py-1.5 rounded-lg bg-emerald-950/80 hover:bg-emerald-700/60 text-emerald-200 border border-emerald-500/20 transition whitespace-nowrap">
                    <i class="fas fa-camera mr-1"></i> Kamera Ukur
                </a>
                <a href="#modul-peta" class="px-3 py-1.5 rounded-lg bg-emerald-950/80 hover:bg-emerald-700/60 text-emerald-200 border border-emerald-500/20 transition whitespace-nowrap">
                    <i class="fas fa-map-location-dot mr-1"></i> Peta & Kompas
                </a>
                <a href="#modul-kalkulator" class="px-3 py-1.5 rounded-lg bg-emerald-950/80 hover:bg-emerald-700/60 text-emerald-200 border border-emerald-500/20 transition whitespace-nowrap">
                    <i class="fas fa-calculator mr-1"></i> Kalkulator
                </a>
            </nav>
        </div>
    </header>

    <!-- Main Container -->
    <main class="flex-grow max-w-7xl w-full mx-auto px-4 sm:px-6 lg:px-8 py-6 space-y-8">

        <!-- ===================================================================== -->
        <!-- MODUL 1: ANALISIS KONDISI LAHAN, METODE UKUR & PEMANFAATAN MATERIAL   -->
        <!-- ===================================================================== -->
        <section id="modul-analisis" class="geo-card rounded-2xl p-5 sm:p-7">
            <div class="flex flex-col sm:flex-row sm:items-center justify-between gap-2 pb-4 mb-6 border-b border-emerald-500/20">
                <div>
                    <span class="text-xs font-semibold uppercase tracking-wider text-emerald-400">Modul 01 • Survei & Rekayasa Tapak</span>
                    <h2 class="text-xl sm:text-2xl font-bold text-white mt-0.5">Analisis Lahan, Metode Ukur & Sisa Material</h2>
                </div>
                <span class="text-xs text-slate-300 bg-black/30 px-3 py-1.5 rounded-lg border border-white/10 w-fit">
                    <i class="fas fa-ruler-combined text-amber-400 mr-1.5"></i>Standar Pengukuran Geomatika
                </span>
            </div>

            <div class="grid grid-cols-1 lg:grid-cols-12 gap-6">
                <!-- Form Input Lahan -->
                <div class="lg:col-span-4 geo-subcard rounded-xl p-5">
                    <h3 class="text-base font-semibold text-emerald-300 mb-4 flex items-center gap-2">
                        <i class="fas fa-sliders"></i> Parameter Dimensi & Kondisi
                    </h3>
                    <form id="landAnalysisForm" class="space-y-4">
                        <div class="grid grid-cols-2 gap-3">
                            <div>
                                <label for="inputPanjang" class="block text-xs font-medium text-slate-300 mb-1">Panjang Lahan (m)</label>
                                <div class="relative">
                                    <input type="number" id="inputPanjang" min="0.5" step="0.1" value="25" required
                                        class="w-full rounded-xl bg-slate-900/90 border border-emerald-500/30 text-white px-3 py-2.5 text-sm focus:outline-none focus:border-emerald-400 font-mono"
                                        placeholder="25">
                                </div>
                            </div>
                            <div>
                                <label for="inputLebar" class="block text-xs font-medium text-slate-300 mb-1">Lebar Lahan (m)</label>
                                <div class="relative">
                                    <input type="number" id="inputLebar" min="0.5" step="0.1" value="15" required
                                        class="w-full rounded-xl bg-slate-900/90 border border-emerald-500/30 text-white px-3 py-2.5 text-sm focus:outline-none focus:border-emerald-400 font-mono"
                                        placeholder="15">
                                </div>
                            </div>
                        </div>

                        <div>
                            <label for="inputKontur" class="block text-xs font-medium text-slate-300 mb-1">Kondisi Kontur / Kemiringan</label>
                            <select id="inputKontur" required
                                class="w-full rounded-xl bg-slate-900/90 border border-emerald-500/30 text-white px-3 py-2.5 text-sm focus:outline-none focus:border-emerald-400">
                                <option value="rata">Rata (Datar / Kemiringan &lt; 3%)</option>
                                <option value="miring">Miring (Berlereng / Kemiringan 3% – 15%)</option>
                                <option value="menanjak">Menanjak (Curam / Kemiringan &gt; 15%)</option>
                            </select>
                        </div>

                        <div>
                            <label for="inputTanah" class="block text-xs font-medium text-slate-300 mb-1">Kondisi Tanah</label>
                            <select id="inputTanah" required
                                class="w-full rounded-xl bg-slate-900/90 border border-emerald-500/30 text-white px-3 py-2.5 text-sm focus:outline-none focus:border-emerald-400">
                                <option value="kering">Kering (Padat / Stabil)</option>
                                <option value="basah">Basah (Lembek / Rawa / Muka Air Tinggi)</option>
                            </select>
                        </div>

                        <div class="pt-2 flex flex-col gap-2.5">
                            <button type="submit"
                                class="w-full py-3 px-4 rounded-xl bg-gradient-to-r from-emerald-600 to-emerald-500 hover:from-emerald-500 hover:to-emerald-400 text-white font-semibold text-sm shadow-lg shadow-emerald-950/50 transition flex items-center justify-center gap-2 cursor-pointer">
                                <i class="fas fa-magnifying-glass-chart"></i> Analisis Kondisi & Rekomendasi
                            </button>
                            <button type="button" id="syncToCanvasBtn"
                                class="w-full py-2.5 px-4 rounded-xl bg-amber-800/40 hover:bg-amber-700/50 border border-amber-500/30 text-amber-200 font-medium text-xs transition flex items-center justify-center gap-2 cursor-pointer">
                                <i class="fas fa-vector-square"></i> Terapkan Ukuran ke Denah 2D
                            </button>
                        </div>
                    </form>
                </div>

                <!-- Output Hasil Analisis -->
                <div class="lg:col-span-8 flex flex-col gap-4" id="analysisOutputArea">
                    <!-- Ringkasan Metrik -->
                    <div class="grid grid-cols-2 sm:grid-cols-4 gap-3">
                        <div class="geo-subcard rounded-xl p-3.5 border-l-4 border-l-emerald-500">
                            <span class="text-xs text-slate-400 block">Luas Lahan</span>
                            <span id="outLuas" class="text-lg sm:text-xl font-bold text-white font-mono">375 m²</span>
                            <span id="outHektar" class="text-[11px] text-emerald-400 font-mono">0.0375 ha</span>
                        </div>
                        <div class="geo-subcard rounded-xl p-3.5 border-l-4 border-l-amber-500">
                            <span class="text-xs text-slate-400 block">Keliling Batas</span>
                            <span id="outKeliling" class="text-lg sm:text-xl font-bold text-white font-mono">80 m</span>
                            <span class="text-[11px] text-amber-300">Panjang patok keliling</span>
                        </div>
                        <div class="geo-subcard rounded-xl p-3.5 border-l-4 border-l-sky-500">
                            <span class="text-xs text-slate-400 block">Alat Utama</span>
                            <span id="outAlatUtama" class="text-sm sm:text-base font-bold text-sky-300 leading-snug block mt-0.5">Waterpass & Meteran</span>
                        </div>
                        <div class="geo-subcard rounded-xl p-3.5 border-l-4 border-l-rose-500">
                            <span class="text-xs text-slate-400 block">Status Tapak</span>
                            <span id="outStatusTapak" class="text-sm sm:text-base font-bold text-rose-300 leading-snug block mt-0.5">Rata & Kering</span>
                        </div>
                    </div>

                    <!-- Detail Metode Ukur & Pemanfaatan Material -->
                    <div class="grid grid-cols-1 md:grid-cols-2 gap-4 flex-grow">
                        <!-- Rekomendasi Metode & Alat Ukur -->
                        <div class="geo-subcard rounded-xl p-4 sm:p-5 flex flex-col">
                            <h4 class="text-sm font-bold text-emerald-300 mb-3 flex items-center gap-2 border-b border-white/10 pb-2">
                                <i class="fas fa-compass text-emerald-400"></i> Metode & Alat Pengukuran Terbaik
                            </h4>
                            <div id="outMetodeList" class="space-y-3 text-xs sm:text-sm text-slate-200 leading-relaxed flex-grow">
                                <!-- Diisi dinamis via JS -->
                            </div>
                        </div>

                        <!-- Pemanfaatan Material Sisa Bangunan -->
                        <div class="geo-subcard rounded-xl p-4 sm:p-5 flex flex-col">
                            <h4 class="text-sm font-bold text-amber-300 mb-3 flex items-center gap-2 border-b border-white/10 pb-2">
                                <i class="fas fa-recycle text-amber-400"></i> Pemanfaatan Sisa Material Bangunan
                            </h4>
                            <div id="outMaterialList" class="space-y-2.5 text-xs sm:text-sm text-slate-200 leading-relaxed flex-grow">
                                <!-- Diisi dinamis via JS -->
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </section>

        <!-- ===================================================================== -->
        <!-- MODUL 2: VISUAL 2D DENAH LAHAN INTERAKTIF (FITUR UTAMA BARU)          -->
        <!-- ===================================================================== -->
        <section id="modul-denah" class="geo-card rounded-2xl p-5 sm:p-7">
            <div class="flex flex-col lg:flex-row lg:items-center justify-between gap-3 pb-4 mb-5 border-b border-emerald-500/20">
                <div>
                    <span class="text-xs font-semibold uppercase tracking-wider text-amber-400">Modul 02 • Pemetaan CAD & Topografi 2D</span>
                    <h2 class="text-xl sm:text-2xl font-bold text-white mt-0.5">Visual 2D Denah Lahan Interaktif</h2>
                    <p class="text-xs text-slate-300 mt-1">Gambar garis bervariabel dengan panjang otomatis, kemiringan (%), sudut (°), serta tempelkan ikon alat ukur geomatika.</p>
                </div>

                <!-- Pengaturan Satuan & Skala Grid Real-Time -->
                <div class="flex flex-wrap items-center gap-2.5 bg-slate-950/70 p-3 rounded-xl border border-emerald-500/25">
                    <div>
                        <label for="unitSelect2D" class="block text-[10px] uppercase tracking-wider text-emerald-300 font-semibold mb-1">Satuan Panjang</label>
                        <select id="unitSelect2D" class="rounded-lg bg-slate-900 border border-emerald-500/40 text-white px-2.5 py-1.5 text-xs font-mono font-semibold focus:outline-none focus:border-emerald-400">
                            <option value="mm">mm (Milimeter)</option>
                            <option value="cm">cm (Sentimeter)</option>
                            <option value="dm">dm (Desimeter)</option>
                            <option value="m" selected>m (Meter)</option>
                            <option value="dam">dam (Dekameter)</option>
                            <option value="hm">hm (Hektometer)</option>
                            <option value="km">km (Kilometer)</option>
                        </select>
                    </div>
                    <div>
                        <label for="gridScale2D" class="block text-[10px] uppercase tracking-wider text-amber-300 font-semibold mb-1">Skala (1 Kotak Grid = m)</label>
                        <input type="number" id="gridScale2D" min="0.01" step="0.1" value="1"
                            class="w-28 rounded-lg bg-slate-900 border border-amber-500/40 text-white px-2.5 py-1.5 text-xs font-mono font-semibold focus:outline-none focus:border-amber-400">
                    </div>
                    <div class="flex items-end">
                        <label class="inline-flex items-center gap-1.5 text-xs text-slate-300 cursor-pointer bg-slate-900/90 px-2.5 py-1.5 rounded-lg border border-white/10 mt-4">
                            <input type="checkbox" id="snapGridCheck" class="accent-emerald-500 rounded">
                            <span>Snap Grid</span>
                        </label>
                    </div>
                </div>
            </div>

            <!-- Kontrol Gambar Garis & Toolbar Ikon Alat Ukur -->
            <div class="grid grid-cols-1 lg:grid-cols-12 gap-4 mb-4">
                <!-- Kontrol Variabel Garis -->
                <div class="lg:col-span-5 geo-subcard rounded-xl p-3.5 flex flex-col justify-between gap-3">
                    <div class="flex items-center justify-between">
                        <span class="text-xs font-bold text-emerald-300 uppercase tracking-wider">
                            <i class="fas fa-pen-ruler mr-1"></i> Mode Gambar Garis
                        </span>
                        <button type="button" id="modeDrawLineBtn"
                            class="px-3 py-1 rounded-lg bg-emerald-600 text-white text-xs font-semibold shadow transition cursor-pointer">
                            <i class="fas fa-check-circle mr-1"></i> Aktif: Gambar Garis
                        </button>
                    </div>
                    <div class="flex gap-2">
                        <div class="flex-grow">
                            <label for="lineVarInput" class="block text-[11px] text-slate-400 mb-1">Nama Variabel Garis Berikutnya</label>
                            <input type="text" id="lineVarInput" value="Garis A-B" placeholder="Misal: Batas Timur / Trase 1"
                                class="w-full rounded-lg bg-slate-900 border border-white/15 text-white px-3 py-2 text-xs focus:outline-none focus:border-emerald-400">
                        </div>
                        <div class="flex items-end">
                            <button type="button" id="toggleLandBoxBtn" title="Tampilkan/Sembunyikan Kotak Batas Lahan dari Modul 1"
                                class="px-3 py-2 rounded-lg bg-slate-800 hover:bg-slate-700 text-emerald-300 border border-emerald-500/30 text-xs font-medium transition cursor-pointer whitespace-nowrap">
                                <i class="fas fa-border-all mr-1"></i> Batas Lahan
                            </button>
                        </div>
                    </div>
                </div>

                <!-- Toolbar Ikon Alat Ukur Geomatika -->
                <div class="lg:col-span-7 geo-subcard rounded-xl p-3.5">
                    <div class="flex items-center justify-between mb-2">
                        <span class="text-xs font-bold text-amber-300 uppercase tracking-wider">
                            <i class="fas fa-toolbox mr-1"></i> Pilih Ikon Alat Ukur (Klik Denah untuk Menempel)
                        </span>
                        <span id="activeToolBadge" class="text-[11px] px-2 py-0.5 rounded bg-emerald-500/20 text-emerald-300 border border-emerald-500/30">
                            Mode: Gambar Garis
                        </span>
                    </div>
                    <div class="grid grid-cols-3 sm:grid-cols-6 gap-2" id="instrumentToolbar">
                        <button type="button" data-tool="total_station" data-label="Total Station"
                            class="tool-btn flex flex-col items-center justify-center p-2 rounded-lg bg-slate-900/90 hover:bg-emerald-900/50 border border-white/10 text-slate-200 transition cursor-pointer">
                            <i class="fas fa-crosshairs text-emerald-400 text-base mb-1"></i>
                            <span class="text-[10px] font-medium text-center leading-tight">Total Station</span>
                        </button>
                        <button type="button" data-tool="prisma" data-label="Prisma Target"
                            class="tool-btn flex flex-col items-center justify-center p-2 rounded-lg bg-slate-900/90 hover:bg-emerald-900/50 border border-white/10 text-slate-200 transition cursor-pointer">
                            <i class="fas fa-gem text-amber-400 text-base mb-1"></i>
                            <span class="text-[10px] font-medium text-center leading-tight">Prisma Target</span>
                        </button>
                        <button type="button" data-tool="gps_rtk" data-label="GPS Geodetik RTK"
                            class="tool-btn flex flex-col items-center justify-center p-2 rounded-lg bg-slate-900/90 hover:bg-emerald-900/50 border border-white/10 text-slate-200 transition cursor-pointer">
                            <i class="fas fa-satellite-dish text-sky-400 text-base mb-1"></i>
                            <span class="text-[10px] font-medium text-center leading-tight">GPS RTK</span>
                        </button>
                        <button type="button" data-tool="waterpass" data-label="Waterpass"
                            class="tool-btn flex flex-col items-center justify-center p-2 rounded-lg bg-slate-900/90 hover:bg-emerald-900/50 border border-white/10 text-slate-200 transition cursor-pointer">
                            <i class="fas fa-binoculars text-teal-400 text-base mb-1"></i>
                            <span class="text-[10px] font-medium text-center leading-tight">Waterpass</span>
                        </button>
                        <button type="button" data-tool="theodolite" data-label="Theodolite"
                            class="tool-btn flex flex-col items-center justify-center p-2 rounded-lg bg-slate-900/90 hover:bg-emerald-900/50 border border-white/10 text-slate-200 transition cursor-pointer">
                            <i class="fas fa-compass text-purple-400 text-base mb-1"></i>
                            <span class="text-[10px] font-medium text-center leading-tight">Theodolite</span>
                        </button>
                        <button type="button" data-tool="drone" data-label="Drone Pemetaan"
                            class="tool-btn flex flex-col items-center justify-center p-2 rounded-lg bg-slate-900/90 hover:bg-emerald-900/50 border border-white/10 text-slate-200 transition cursor-pointer">
                            <i class="fas fa-helicopter text-rose-400 text-base mb-1"></i>
                            <span class="text-[10px] font-medium text-center leading-tight">Drone UAV</span>
                        </button>
                    </div>
                </div>
            </div>

            <!-- Area Kanvas 2D Ber-Grid -->
            <div class="relative w-full rounded-xl overflow-hidden border-2 border-emerald-500/30 bg-[#09131a] shadow-inner">
                <div class="w-full h-[360px] sm:h-[440px] relative" id="canvas2DWrapper">
                    <canvas id="blueprintCanvas" class="w-full h-full block cursor-crosshair touch-none-canvas"></canvas>
                </div>
                
                <!-- Petunjuk singkat di pojok bawah kanvas -->
                <div class="bg-slate-950/90 px-4 py-2.5 border-t border-white/10 flex flex-wrap items-center justify-between gap-2 text-xs">
                    <span class="text-slate-300" id="canvasHintText">
                        <i class="fas fa-hand-pointer text-emerald-400 mr-1"></i> Klik & tarik (atau klik titik awal lalu klik titik akhir) untuk menggambar garis bervariabel.
                    </span>
                    <div class="flex flex-wrap items-center gap-2">
                        <button type="button" id="undoLineBtn" class="px-2.5 py-1.5 rounded-lg bg-slate-800 hover:bg-slate-700 text-slate-200 border border-white/10 transition cursor-pointer">
                            <i class="fas fa-undo mr-1"></i> Hapus Garis Terakhir
                        </button>
                        <button type="button" id="undoIconBtn" class="px-2.5 py-1.5 rounded-lg bg-slate-800 hover:bg-slate-700 text-slate-200 border border-white/10 transition cursor-pointer">
                            <i class="fas fa-eraser mr-1"></i> Hapus Ikon Terakhir
                        </button>
                        <button type="button" id="resetCanvas2DBtn" class="px-2.5 py-1.5 rounded-lg bg-rose-900/70 hover:bg-rose-800 text-rose-100 border border-rose-500/30 transition cursor-pointer">
                            <i class="fas fa-trash-alt mr-1"></i> Reset Denah
                        </button>
                    </div>
                </div>
            </div>

            <!-- Tabel Ringkasan Data Garis & Alat Terpasang -->
            <div class="mt-4 grid grid-cols-1 lg:grid-cols-12 gap-4">
                <div class="lg:col-span-8 geo-subcard rounded-xl p-4">
                    <h4 class="text-xs font-bold uppercase tracking-wider text-emerald-300 mb-2.5 flex items-center justify-between">
                        <span><i class="fas fa-list-ol mr-1.5"></i>Daftar Garis & Variabel Terukur</span>
                        <span id="lineCountBadge" class="font-mono text-slate-400">0 Garis</span>
                    </h4>
                    <div class="overflow-x-auto max-h-44">
                        <table class="w-full text-left text-xs">
                            <thead class="text-slate-400 border-b border-white/10">
                                <tr>
                                    <th class="py-1.5 px-2">Variabel</th>
                                    <th class="py-1.5 px-2">Panjang</th>
                                    <th class="py-1.5 px-2">Kemiringan (%)</th>
                                    <th class="py-1.5 px-2">Sudut (°)</th>
                                    <th class="py-1.5 px-2 text-right">Aksi</th>
                                </tr>
                            </thead>
                            <tbody id="linesTableBody" class="divide-y divide-white/5 text-slate-200 font-mono">
                                <tr>
                                    <td colspan="5" class="py-4 text-center text-slate-400 font-sans">Belum ada garis tambahan. Silakan gambar pada kanvas di atas.</td>
                                </tr>
                            </tbody>
                        </table>
                    </div>
                </div>
                <div class="lg:col-span-4 geo-subcard rounded-xl p-4">
                    <h4 class="text-xs font-bold uppercase tracking-wider text-amber-300 mb-2.5 flex items-center justify-between">
                        <span><i class="fas fa-location-pin mr-1.5"></i>Alat Terpasang di Denah</span>
                        <span id="iconCountBadge" class="font-mono text-slate-400">0 Alat</span>
                    </h4>
                    <ul id="iconsListSummary" class="space-y-1.5 text-xs text-slate-300 max-h-44 overflow-y-auto">
                        <li class="text-slate-400 py-3 text-center">Belum ada ikon alat ukur ditempelkan.</li>
                    </ul>
                </div>
            </div>
        </section>

        <!-- ===================================================================== -->
        <!-- MODUL 3: KAMERA PENGUKUR LAHAN (AR SEDERHANA & AMBIL FOTO)            -->
        <!-- ===================================================================== -->
        <section id="modul-kamera" class="geo-card rounded-2xl p-5 sm:p-7">
            <div class="flex flex-col sm:flex-row sm:items-center justify-between gap-2 pb-4 mb-5 border-b border-emerald-500/20">
                <div>
                    <span class="text-xs font-semibold uppercase tracking-wider text-sky-400">Modul 03 • Fotogrametri Lapangan & AR</span>
                    <h2 class="text-xl sm:text-2xl font-bold text-white mt-0.5">Kamera Pengukur Lahan (Titik AR & Ambil Foto)</h2>
                    <p class="text-xs text-slate-300 mt-1">Ketuk layar kamera untuk menaruh tanda <strong class="text-emerald-400">+</strong> titik awal, lalu ketuk titik berikutnya untuk menarik garis estimasi jarak.</p>
                </div>
                <div class="flex items-center gap-2 bg-slate-950/70 px-3 py-2 rounded-xl border border-white/10">
                    <label for="arScaleFactor" class="text-xs text-slate-300 whitespace-nowrap">Kalibrasi AR (100px =</label>
                    <input type="number" id="arScaleFactor" min="0.1" step="0.1" value="1.0"
                        class="w-16 rounded bg-slate-900 border border-sky-500/40 text-white px-2 py-1 text-xs font-mono text-center">
                    <span class="text-xs text-sky-300 font-mono">m)</span>
                </div>
            </div>

            <div class="grid grid-cols-1 lg:grid-cols-12 gap-6 items-start">
                <!-- Viewport Kamera + Kanvas Overlay Transparan -->
                <div class="lg:col-span-8">
                    <div id="cameraViewportContainer" class="relative w-full aspect-video rounded-2xl overflow-hidden bg-slate-950 border-2 border-slate-700/80 flex items-center justify-center shadow-xl">
                        <!-- Placeholder saat kamera mati -->
                        <div id="cameraPlaceholder" class="text-center p-6">
                            <div class="w-16 h-16 rounded-full bg-slate-900 border border-white/10 flex items-center justify-center mx-auto mb-3 text-slate-500">
                                <i class="fas fa-video-slash text-2xl"></i>
                            </div>
                            <p class="text-sm font-medium text-slate-300">Kamera Belum Aktif</p>
                            <p class="text-xs text-slate-400 mt-1">Tekan tombol <strong>Mulai Kamera</strong> untuk mengukur objek lahan secara langsung.</p>
                        </div>

                        <!-- Live Video Feed -->
                        <video id="arVideoFeed" autoplay playsinline muted class="w-full h-full object-cover hidden"></video>
                        
                        <!-- Hasil Preview Foto -->
                        <img id="arPhotoPreview" alt="Hasil Foto Pengukuran" class="w-full h-full object-cover hidden">

                        <!-- Kanvas Transparan Interaktif AR di atas Video -->
                        <canvas id="arOverlayCanvas" class="absolute inset-0 w-full h-full cursor-crosshair hidden touch-none-canvas"></canvas>

                        <!-- Badge Status Jarak Total di Pojok Kiri Atas Kamera -->
                        <div id="arHudBadge" class="hidden absolute top-3 left-3 bg-black/75 backdrop-blur-md border border-emerald-500/40 rounded-xl px-3 py-1.5 text-xs font-mono text-white pointer-events-none">
                            <span class="text-emerald-400 font-bold">TITIK: <span id="arPointCount">0</span></span>
                            <span class="mx-2 text-slate-500">|</span>
                            <span>TOTAL: <strong id="arTotalDist" class="text-amber-300">0.00 m</strong></span>
                        </div>
                    </div>
                    <canvas id="arCaptureCanvas" class="hidden"></canvas>
                </div>

                <!-- Kontrol Kamera & Daftar Segmen AR -->
                <div class="lg:col-span-4 space-y-4">
                    <div class="geo-subcard rounded-xl p-4 space-y-3">
                        <h3 class="text-sm font-bold text-white flex items-center gap-2">
                            <i class="fas fa-sliders text-sky-400"></i> Kontrol Kamera & Pengukuran
                        </h3>

                        <!-- Tombol Mulai / Matikan Kamera -->
                        <button type="button" id="toggleCameraBtn"
                            class="w-full py-3 px-4 rounded-xl bg-emerald-600 hover:bg-emerald-500 text-white font-semibold text-sm shadow transition flex items-center justify-center gap-2 cursor-pointer">
                            <i class="fas fa-play" id="toggleCamIcon"></i>
                            <span id="toggleCamText">Mulai Kamera</span>
                        </button>

                        <!-- Tombol Saat Kamera Live -->
                        <div id="liveCameraControls" class="grid grid-cols-2 gap-2.5 hidden">
                            <button type="button" id="resetArPointsBtn"
                                class="py-2.5 px-3 rounded-xl bg-amber-700/50 hover:bg-amber-600/60 border border-amber-500/30 text-amber-200 font-medium text-xs transition flex items-center justify-center gap-1.5 cursor-pointer">
                                <i class="fas fa-rotate-left"></i> Reset Titik
                            </button>
                            <button type="button" id="captureArPhotoBtn"
                                class="py-2.5 px-3 rounded-xl bg-sky-600 hover:bg-sky-500 text-white font-semibold text-xs shadow transition flex items-center justify-center gap-1.5 cursor-pointer">
                                <i class="fas fa-camera"></i> Ambil Foto
                            </button>
                        </div>

                        <!-- Tombol Setelah Foto Diambil -->
                        <div id="capturedPhotoControls" class="grid grid-cols-2 gap-2.5 hidden">
                            <button type="button" id="retakeArPhotoBtn"
                                class="py-2.5 px-3 rounded-xl bg-amber-600 hover:bg-amber-500 text-white font-semibold text-xs transition flex items-center justify-center gap-1.5 cursor-pointer">
                                <i class="fas fa-redo"></i> Ulangi
                            </button>
                            <button type="button" id="downloadArPhotoBtn"
                                class="py-2.5 px-3 rounded-xl bg-emerald-600 hover:bg-emerald-500 text-white font-semibold text-xs shadow transition flex items-center justify-center gap-1.5 cursor-pointer">
                                <i class="fas fa-download"></i> Unduh
                            </button>
                        </div>

                        <p id="cameraFallbackNote" class="text-[11px] text-slate-400 leading-relaxed">
                            <i class="fas fa-info-circle text-sky-400 mr-1"></i>
                            Jika kamera tidak tersedia di perangkat Anda, Anda tetap dapat mengetuk area layar di samping setelah menekan "Mulai Kamera (Mode Simulasi)" untuk menguji pengukuran titik AR.
                        </p>
                    </div>

                    <!-- Rincian Segmen Titik Kamera -->
                    <div class="geo-subcard rounded-xl p-4">
                        <h4 class="text-xs font-bold uppercase tracking-wider text-sky-300 mb-2">Rincian Segmen Titik (+)</h4>
                        <ul id="arSegmentsList" class="space-y-1.5 text-xs font-mono text-slate-300 max-h-40 overflow-y-auto">
                            <li class="text-slate-400 font-sans">Belum ada titik ditempatkan pada layar kamera.</li>
                        </ul>
                    </div>
                </div>
            </div>
        </section>

        <!-- ===================================================================== -->
        <!-- MODUL 4: PETA INTERAKTIF (LEAFLET) & KOMPAS DIGITAL                   -->
        <!-- ===================================================================== -->
        <section id="modul-peta" class="grid grid-cols-1 lg:grid-cols-12 gap-6">
            <!-- Peta Interaktif Leaflet -->
            <div class="lg:col-span-8 geo-card rounded-2xl p-5 sm:p-6 flex flex-col">
                <div class="flex flex-wrap items-center justify-between gap-2 pb-4 mb-4 border-b border-emerald-500/20">
                    <div>
                        <span class="text-xs font-semibold uppercase tracking-wider text-emerald-400">Modul 04A • Sistem Informasi Geografis</span>
                        <h2 class="text-xl font-bold text-white mt-0.5">Peta Lokasi & Penanda Batas Lahan</h2>
                    </div>
                    <div class="flex items-center gap-2">
                        <button type="button" id="findMyLocationBtn"
                            class="px-3.5 py-2 rounded-xl bg-sky-600 hover:bg-sky-500 text-white text-xs font-semibold shadow transition flex items-center gap-1.5 cursor-pointer">
                            <i class="fas fa-location-crosshairs"></i> Lokasi Saya
                        </button>
                        <button type="button" id="clearMapMarkersBtn"
                            class="px-3 py-2 rounded-xl bg-slate-800 hover:bg-rose-900/70 text-slate-200 border border-white/10 text-xs font-medium transition cursor-pointer">
                            <i class="fas fa-trash-can mr-1"></i> Hapus Tanda
                        </button>
                    </div>
                </div>

                <!-- Container Peta -->
                <div id="leafletMap" class="w-full h-80 sm:h-96 rounded-xl border border-emerald-500/30 z-10 overflow-hidden"></div>

                <!-- Daftar Koordinat Marker -->
                <div class="mt-4 geo-subcard rounded-xl p-3.5">
                    <div class="flex items-center justify-between mb-2">
                        <span class="text-xs font-semibold text-emerald-300">
                            <i class="fas fa-map-pin mr-1"></i> Klik pada peta untuk menambah penanda koordinat (Lintang, Bujur):
                        </span>
                        <span id="markerCountLabel" class="text-xs font-mono text-slate-400">0 Titik</span>
                    </div>
                    <div id="mapMarkersList" class="flex flex-wrap gap-2 max-h-28 overflow-y-auto text-xs">
                        <span class="text-slate-400">Belum ada penanda titik lahan pada peta.</span>
                    </div>
                </div>
            </div>

            <!-- Kompas Digital Geomatika -->
            <div class="lg:col-span-4 geo-card rounded-2xl p-5 sm:p-6 flex flex-col justify-between">
                <div>
                    <span class="text-xs font-semibold uppercase tracking-wider text-amber-400">Modul 04B • Orientasi Azimuth</span>
                    <h2 class="text-xl font-bold text-white mt-0.5">Kompas Digital</h2>
                    <p class="text-xs text-slate-300 mt-1">Menentukan sudut azimuth utara magnetik dan orientasi bidang lahan.</p>
                </div>

                <!-- Visual Dial Kompas -->
                <div class="my-6 flex flex-col items-center justify-center">
                    <div class="relative w-48 h-48 sm:w-52 sm:h-52 rounded-full bg-gradient-to-b from-slate-900 to-slate-950 border-4 border-amber-500/40 shadow-2xl flex items-center justify-center">
                        <!-- Ring Derajat Luar -->
                        <div class="absolute inset-2 rounded-full border border-dashed border-slate-600"></div>
                        
                        <!-- Arah Mata Angin -->
                        <span class="absolute top-2.5 text-rose-500 font-extrabold text-sm">U (N)</span>
                        <span class="absolute bottom-2.5 text-slate-300 font-bold text-sm">S</span>
                        <span class="absolute right-3 text-slate-300 font-bold text-sm">T (E)</span>
                        <span class="absolute left-3 text-slate-300 font-bold text-sm">B (W)</span>

                        <!-- Jarum Kompas Berputar -->
                        <div id="compassNeedleEl" class="w-3 h-36 relative flex flex-col items-center justify-between transition-transform duration-150 ease-out" style="transform: rotate(0deg);">
                            <!-- Ujung Utara (Merah) -->
                            <div class="w-0 h-0 border-l-[7px] border-l-transparent border-r-[7px] border-r-transparent border-b-[68px] border-b-rose-500 drop-shadow"></div>
                            <!-- Ujung Selatan (Putih/Perak) -->
                            <div class="w-0 h-0 border-l-[7px] border-l-transparent border-r-[7px] border-r-transparent border-t-[68px] border-t-slate-300"></div>
                        </div>

                        <!-- Poros Tengah -->
                        <div class="absolute w-4 h-4 rounded-full bg-amber-400 border-2 border-slate-900 shadow"></div>
                    </div>

                    <!-- Readout Azimuth -->
                    <div class="mt-4 text-center bg-slate-950/80 px-5 py-2 rounded-xl border border-white/10">
                        <div id="compassAzimuthText" class="text-2xl font-bold font-mono text-amber-300">000°</div>
                        <div id="compassDirectionText" class="text-xs font-semibold text-emerald-300 uppercase tracking-wider">Utara (N)</div>
                    </div>
                </div>

                <!-- Kontrol Sensor & Simulasi Manual Desktop -->
                <div class="space-y-3 geo-subcard rounded-xl p-3.5">
                    <button type="button" id="activateCompassSensorBtn"
                        class="w-full py-2.5 px-4 rounded-xl bg-emerald-600 hover:bg-emerald-500 text-white font-semibold text-xs shadow transition flex items-center justify-center gap-2 cursor-pointer">
                        <i class="fas fa-compass"></i> Aktifkan Sensor Kompas Perangkat
                    </button>
                    <div>
                        <div class="flex justify-between text-[11px] text-slate-400 mb-1">
                            <span>Simulasi Azimuth Manual:</span>
                            <span id="manualSliderVal" class="font-mono text-slate-200">0°</span>
                        </div>
                        <input type="range" id="manualCompassSlider" min="0" max="360" value="0"
                            class="w-full accent-amber-500 cursor-pointer">
                    </div>
                </div>
            </div>
        </section>

        <!-- ===================================================================== -->
        <!-- MODUL 5: KALKULATOR ILMIAH, KONVERSI SATUAN & HITUNG SKALA            -->
        <!-- ===================================================================== -->
        <section id="modul-kalkulator" class="geo-card rounded-2xl p-5 sm:p-7">
            <div class="pb-4 mb-6 border-b border-emerald-500/20">
                <span class="text-xs font-semibold uppercase tracking-wider text-emerald-400">Modul 05 • Komputasi Geodesi & Kartografi</span>
                <h2 class="text-xl sm:text-2xl font-bold text-white mt-0.5">Kalkulator Ilmiah, Konversi Satuan & Skala Peta</h2>
            </div>

            <!-- Kalkulator Ilmiah (Desain Calculator Online Sesuai Referensi) -->
            <div class="rounded-2xl overflow-hidden shadow-2xl border border-slate-300/30 mb-8 bg-white text-slate-800">
                <!-- Header Biru Calculator Online -->
                <div class="bg-[#2196f3] text-white px-4 py-2.5 flex items-center justify-between">
                    <div class="flex items-center gap-2.5">
                        <div class="bg-white text-[#2196f3] font-extrabold text-[10px] leading-none px-1.5 py-1 rounded shadow-sm text-center">
                            <div>+ −</div>
                            <div>÷ =</div>
                        </div>
                        <div class="leading-tight">
                            <span class="font-bold tracking-wide text-sm block">CALCULATOR</span>
                            <span class="text-[11px] text-blue-100 block">Online • Geomatics Edition</span>
                        </div>
                    </div>
                    <div class="hidden sm:flex items-center gap-4 text-xs font-medium text-blue-50">
                        <span>Scientific</span>
                        <span>Trigonometry</span>
                        <span>Conversion</span>
                    </div>
                </div>

                <!-- Sub-Header Judul -->
                <div class="bg-[#f8fafc] text-[#1e5b88] text-center py-3 text-xl font-bold border-b border-slate-200">
                    Calculator Online
                </div>

                <!-- Body Utama Kalkulator: Keypad Kiri + History Kanan -->
                <div class="bg-[#eef2f6] p-4 sm:p-5 grid grid-cols-1 lg:grid-cols-12 gap-5">
                    <!-- Kolom Kiri: Layar Input & Tombol Ilmiah -->
                    <div class="lg:col-span-7 bg-white p-3.5 rounded-xl border border-slate-200 shadow-sm">
                        <input type="text" id="sciCalcInput" readonly placeholder="0"
                            class="w-full h-12 px-3 mb-3 rounded-lg border border-slate-300 bg-slate-50 text-right font-mono text-lg font-semibold text-slate-800 focus:outline-none">

                        <div class="grid grid-cols-5 gap-1.5 text-xs sm:text-sm">
                            <!-- Baris 1: Deg / Rad + sin cos tan -->
                            <div class="col-span-2 flex items-center justify-around bg-[#f1f8fc] border border-[#bce8f1] rounded-lg px-2 py-2 text-slate-700 font-medium">
                                <label class="flex items-center gap-1 cursor-pointer">
                                    <input type="radio" name="calcAngleMode" value="deg" checked class="accent-[#2196f3]"> Deg
                                </label>
                                <label class="flex items-center gap-1 cursor-pointer">
                                    <input type="radio" name="calcAngleMode" value="rad" class="accent-[#2196f3]"> Rad
                                </label>
                            </div>
                            <button type="button" class="sci-btn bg-[#d9edf7] hover:bg-[#c4e3f3] text-[#245269] font-medium py-2.5 rounded-lg transition cursor-pointer" data-val="sin(">sin</button>
                            <button type="button" class="sci-btn bg-[#d9edf7] hover:bg-[#c4e3f3] text-[#245269] font-medium py-2.5 rounded-lg transition cursor-pointer" data-val="cos(">cos</button>
                            <button type="button" class="sci-btn bg-[#d9edf7] hover:bg-[#c4e3f3] text-[#245269] font-medium py-2.5 rounded-lg transition cursor-pointer" data-val="tan(">tan</button>

                            <!-- Baris 2: pi, e, sin^-1, cos^-1, tan^-1 -->
                            <button type="button" class="sci-btn bg-[#d9edf7] hover:bg-[#c4e3f3] text-[#245269] font-medium py-2.5 rounded-lg transition cursor-pointer" data-val="π">π</button>
                            <button type="button" class="sci-btn bg-[#d9edf7] hover:bg-[#c4e3f3] text-[#245269] font-medium py-2.5 rounded-lg transition cursor-pointer" data-val="e">e</button>
                            <button type="button" class="sci-btn bg-[#d9edf7] hover:bg-[#c4e3f3] text-[#245269] font-medium py-2.5 rounded-lg transition cursor-pointer" data-val="asin(">sin⁻¹</button>
                            <button type="button" class="sci-btn bg-[#d9edf7] hover:bg-[#c4e3f3] text-[#245269] font-medium py-2.5 rounded-lg transition cursor-pointer" data-val="acos(">cos⁻¹</button>
                            <button type="button" class="sci-btn bg-[#d9edf7] hover:bg-[#c4e3f3] text-[#245269] font-medium py-2.5 rounded-lg transition cursor-pointer" data-val="atan(">tan⁻¹</button>

                            <!-- Baris 3: ln, log, y√x, ³√x, √x -->
                            <button type="button" class="sci-btn bg-[#d9edf7] hover:bg-[#c4e3f3] text-[#245269] font-medium py-2.5 rounded-lg transition cursor-pointer" data-val="ln(">ln</button>
                            <button type="button" class="sci-btn bg-[#d9edf7] hover:bg-[#c4e3f3] text-[#245269] font-medium py-2.5 rounded-lg transition cursor-pointer" data-val="log(">log</button>
                            <button type="button" class="sci-btn bg-[#d9edf7] hover:bg-[#c4e3f3] text-[#245269] font-medium py-2.5 rounded-lg transition cursor-pointer" data-val="y√">ʸ√x</button>
                            <button type="button" class="sci-btn bg-[#d9edf7] hover:bg-[#c4e3f3] text-[#245269] font-medium py-2.5 rounded-lg transition cursor-pointer" data-val="cbrt(">³√x</button>
                            <button type="button" class="sci-btn bg-[#d9edf7] hover:bg-[#c4e3f3] text-[#245269] font-medium py-2.5 rounded-lg transition cursor-pointer" data-val="√(">√x</button>

                            <!-- Baris 4: e^x, 10^x, x^y, x^3, x^2 -->
                            <button type="button" class="sci-btn bg-[#d9edf7] hover:bg-[#c4e3f3] text-[#245269] font-medium py-2.5 rounded-lg transition cursor-pointer" data-val="e^(">eˣ</button>
                            <button type="button" class="sci-btn bg-[#d9edf7] hover:bg-[#c4e3f3] text-[#245269] font-medium py-2.5 rounded-lg transition cursor-pointer" data-val="10^(">10ˣ</button>
                            <button type="button" class="sci-btn bg-[#d9edf7] hover:bg-[#c4e3f3] text-[#245269] font-medium py-2.5 rounded-lg transition cursor-pointer" data-val="^">xʸ</button>
                            <button type="button" class="sci-btn bg-[#d9edf7] hover:bg-[#c4e3f3] text-[#245269] font-medium py-2.5 rounded-lg transition cursor-pointer" data-val="^3">x³</button>
                            <button type="button" class="sci-btn bg-[#d9edf7] hover:bg-[#c4e3f3] text-[#245269] font-medium py-2.5 rounded-lg transition cursor-pointer" data-val="^2">x²</button>

                            <!-- Baris 5: (, ), 1/x, %, n! -->
                            <button type="button" class="sci-btn bg-[#d9edf7] hover:bg-[#c4e3f3] text-[#245269] font-medium py-2.5 rounded-lg transition cursor-pointer" data-val="(">(</button>
                            <button type="button" class="sci-btn bg-[#d9edf7] hover:bg-[#c4e3f3] text-[#245269] font-medium py-2.5 rounded-lg transition cursor-pointer" data-val=")">)</button>
                            <button type="button" class="sci-btn bg-[#d9edf7] hover:bg-[#c4e3f3] text-[#245269] font-medium py-2.5 rounded-lg transition cursor-pointer" data-val="1/(">1/x</button>
                            <button type="button" class="sci-btn bg-[#d9edf7] hover:bg-[#c4e3f3] text-[#245269] font-medium py-2.5 rounded-lg transition cursor-pointer" data-val="%">%</button>
                            <button type="button" class="sci-btn bg-[#d9edf7] hover:bg-[#c4e3f3] text-[#245269] font-medium py-2.5 rounded-lg transition cursor-pointer" data-val="!">n!</button>

                            <!-- Baris 6: 7, 8, 9, +, Backspace -->
                            <button type="button" class="sci-btn bg-[#337ab7] hover:bg-[#286090] text-white font-bold py-2.5 rounded-lg transition cursor-pointer" data-val="7">7</button>
                            <button type="button" class="sci-btn bg-[#337ab7] hover:bg-[#286090] text-white font-bold py-2.5 rounded-lg transition cursor-pointer" data-val="8">8</button>
                            <button type="button" class="sci-btn bg-[#337ab7] hover:bg-[#286090] text-white font-bold py-2.5 rounded-lg transition cursor-pointer" data-val="9">9</button>
                            <button type="button" class="sci-btn bg-[#d9edf7] hover:bg-[#c4e3f3] text-[#245269] font-bold py-2.5 rounded-lg transition cursor-pointer" data-val="+">+</button>
                            <button type="button" class="sci-btn bg-[#e2e8f0] hover:bg-[#cbd5e1] text-slate-700 font-bold py-2.5 rounded-lg transition cursor-pointer" data-action="backspace" aria-label="Backspace">
                                <i class="fas fa-delete-left"></i>
                            </button>

                            <!-- Baris 7: 4, 5, 6, -, AC -->
                            <button type="button" class="sci-btn bg-[#337ab7] hover:bg-[#286090] text-white font-bold py-2.5 rounded-lg transition cursor-pointer" data-val="4">4</button>
                            <button type="button" class="sci-btn bg-[#337ab7] hover:bg-[#286090] text-white font-bold py-2.5 rounded-lg transition cursor-pointer" data-val="5">5</button>
                            <button type="button" class="sci-btn bg-[#337ab7] hover:bg-[#286090] text-white font-bold py-2.5 rounded-lg transition cursor-pointer" data-val="6">6</button>
                            <button type="button" class="sci-btn bg-[#d9edf7] hover:bg-[#c4e3f3] text-[#245269] font-bold py-2.5 rounded-lg transition cursor-pointer" data-val="-">−</button>
                            <button type="button" class="sci-btn bg-[#e2e8f0] hover:bg-[#cbd5e1] text-slate-700 font-bold py-2.5 rounded-lg transition cursor-pointer" data-action="clear">AC</button>

                            <!-- Baris 8: 1, 2, 3, ×, ÷ -->
                            <button type="button" class="sci-btn bg-[#337ab7] hover:bg-[#286090] text-white font-bold py-2.5 rounded-lg transition cursor-pointer" data-val="1">1</button>
                            <button type="button" class="sci-btn bg-[#337ab7] hover:bg-[#286090] text-white font-bold py-2.5 rounded-lg transition cursor-pointer" data-val="2">2</button>
                            <button type="button" class="sci-btn bg-[#337ab7] hover:bg-[#286090] text-white font-bold py-2.5 rounded-lg transition cursor-pointer" data-val="3">3</button>
                            <button type="button" class="sci-btn bg-[#d9edf7] hover:bg-[#c4e3f3] text-[#245269] font-bold py-2.5 rounded-lg transition cursor-pointer" data-val="×">×</button>
                            <button type="button" class="sci-btn bg-[#d9edf7] hover:bg-[#c4e3f3] text-[#245269] font-bold py-2.5 rounded-lg transition cursor-pointer" data-val="÷">÷</button>

                            <!-- Baris 9: 0, ., = -->
                            <button type="button" class="sci-btn col-span-2 bg-[#337ab7] hover:bg-[#286090] text-white font-bold py-2.5 rounded-lg transition cursor-pointer" data-val="0">0</button>
                            <button type="button" class="sci-btn bg-[#337ab7] hover:bg-[#286090] text-white font-bold py-2.5 rounded-lg transition cursor-pointer" data-val=".">.</button>
                            <button type="button" class="sci-btn col-span-2 bg-[#286090] hover:bg-[#1e4b73] text-white font-extrabold py-2.5 rounded-lg shadow transition cursor-pointer" data-action="equals">=</button>
                        </div>
                    </div>

                    <!-- Kolom Kanan: Output Utama & History -->
                    <div class="lg:col-span-5 flex flex-col bg-white rounded-xl border border-slate-200 shadow-sm overflow-hidden">
                        <div id="sciCalcMainResult" class="bg-[#337ab7] text-white px-4 py-3 text-right font-mono text-2xl font-bold truncate">
                            0
                        </div>
                        <div class="p-4 flex-grow flex flex-col justify-between">
                            <div>
                                <div class="border-b-2 border-[#337ab7] text-[#337ab7] font-bold text-sm pb-1 mb-3 inline-block">
                                    History
                                </div>
                                <div id="sciCalcHistoryList" class="space-y-2.5 max-h-64 overflow-y-auto text-sm font-mono pr-1">
                                    <p class="text-slate-400 font-sans text-xs">Belum ada riwayat perhitungan.</p>
                                </div>
                            </div>
                            <button type="button" id="clearSciHistoryBtn"
                                class="mt-4 w-fit px-4 py-2 rounded-lg bg-[#286090] hover:bg-[#1e4b73] text-white text-xs font-semibold shadow transition cursor-pointer">
                                Clear History
                            </button>
                        </div>
                    </div>
                </div>
            </div>

            <!-- Dua Alat Tambahan di Bawah Kalkulator: Konversi Satuan (2 Kolom) & Kalkulator Skala Peta -->
            <div class="grid grid-cols-1 lg:grid-cols-2 gap-6">
                <!-- 1. Konversi Satuan Panjang (2 Kolom Dropdown Spesifik) -->
                <div class="geo-subcard rounded-2xl p-5 border border-emerald-500/20">
                    <h3 class="text-base font-bold text-emerald-300 mb-1 flex items-center gap-2">
                        <i class="fas fa-arrow-right-arrow-left"></i> Konversi Satuan Panjang
                    </h3>
                    <p class="text-xs text-slate-400 mb-4">Konversi cepat antar satuan metrik pengukuran lapangan.</p>

                    <div class="space-y-4">
                        <div>
                            <label for="convInputValue" class="block text-xs font-medium text-slate-300 mb-1">Nilai Panjang</label>
                            <input type="number" id="convInputValue" value="1" step="any"
                                class="w-full rounded-xl bg-slate-900 border border-emerald-500/30 text-white px-3.5 py-2.5 font-mono text-sm focus:outline-none focus:border-emerald-400">
                        </div>

                        <!-- 2 Kolom Dropdown sesuai spesifikasi -->
                        <div class="grid grid-cols-1 sm:grid-cols-2 gap-3">
                            <div>
                                <label for="convFromUnit" class="block text-xs font-medium text-slate-300 mb-1">Kolom 1: Dari Satuan</label>
                                <select id="convFromUnit"
                                    class="w-full rounded-xl bg-slate-900 border border-emerald-500/30 text-white px-3 py-2.5 font-mono text-sm focus:outline-none focus:border-emerald-400">
                                    <option value="mm">mm (Milimeter)</option>
                                    <option value="cm">cm (Sentimeter)</option>
                                    <option value="dm">dm (Desimeter)</option>
                                    <option value="m" selected>m (Meter)</option>
                                    <option value="dam">dam (Dekameter)</option>
                                    <option value="hm">hm (Hektometer)</option>
                                </select>
                            </div>
                            <div>
                                <label for="convToUnit" class="block text-xs font-medium text-slate-300 mb-1">Kolom 2: Ke Satuan</label>
                                <select id="convToUnit"
                                    class="w-full rounded-xl bg-slate-900 border border-amber-500/30 text-white px-3 py-2.5 font-mono text-sm focus:outline-none focus:border-amber-400">
                                    <option value="cm" selected>cm (Sentimeter)</option>
                                    <option value="dm">dm (Desimeter)</option>
                                    <option value="m">m (Meter)</option>
                                    <option value="dam">dam (Dekameter)</option>
                                    <option value="hm">hm (Hektometer)</option>
                                    <option value="km">km (Kilometer)</option>
                                </select>
                            </div>
                        </div>

                        <div class="rounded-xl bg-emerald-950/60 border border-emerald-500/30 p-3.5 flex items-center justify-between">
                            <div>
                                <span class="text-[11px] text-emerald-300 block">Hasil Konversi:</span>
                                <span id="convResultDisplay" class="text-lg sm:text-xl font-bold font-mono text-white">100 cm</span>
                            </div>
                            <span id="convFormulaNote" class="text-xs font-mono text-slate-400">1 m = 100 cm</span>
                        </div>
                    </div>
                </div>

                <!-- 2. Kalkulator Skala Peta -->
                <div class="geo-subcard rounded-2xl p-5 border border-amber-500/20">
                    <h3 class="text-base font-bold text-amber-300 mb-1 flex items-center gap-2">
                        <i class="fas fa-map"></i> Kalkulator Skala Peta (1 : N)
                    </h3>
                    <p class="text-xs text-slate-400 mb-4">Hitung skala peta, jarak pada peta, atau jarak sebenarnya di lapangan.</p>

                    <div class="space-y-3.5">
                        <div>
                            <label for="scaleCalcMode" class="block text-xs font-medium text-slate-300 mb-1">Mode Perhitungan</label>
                            <select id="scaleCalcMode"
                                class="w-full rounded-xl bg-slate-900 border border-amber-500/30 text-white px-3 py-2 text-xs sm:text-sm focus:outline-none focus:border-amber-400">
                                <option value="findScale">Hitung Skala Peta (1 : N)</option>
                                <option value="findReal">Hitung Jarak Sebenarnya (m / km)</option>
                                <option value="findMap">Hitung Jarak pada Peta (cm)</option>
                            </select>
                        </div>

                        <div class="grid grid-cols-1 sm:grid-cols-2 gap-3">
                            <div id="scaleFieldA">
                                <label id="scaleLabelA" for="scaleInputA" class="block text-xs font-medium text-slate-300 mb-1">Jarak pada Peta (cm)</label>
                                <input type="number" id="scaleInputA" value="5" min="0.0001" step="any"
                                    class="w-full rounded-xl bg-slate-900 border border-white/15 text-white px-3 py-2 font-mono text-sm focus:outline-none focus:border-amber-400">
                            </div>
                            <div id="scaleFieldB">
                                <label id="scaleLabelB" for="scaleInputB" class="block text-xs font-medium text-slate-300 mb-1">Jarak Sebenarnya (m)</label>
                                <input type="number" id="scaleInputB" value="250" min="0.0001" step="any"
                                    class="w-full rounded-xl bg-slate-900 border border-white/15 text-white px-3 py-2 font-mono text-sm focus:outline-none focus:border-amber-400">
                            </div>
                        </div>

                        <div class="rounded-xl bg-amber-950/50 border border-amber-500/30 p-3.5 flex items-center justify-between">
                            <div>
                                <span class="text-[11px] text-amber-300 block">Hasil Perhitungan Skala:</span>
                                <span id="scaleResultDisplay" class="text-lg sm:text-xl font-bold font-mono text-white">1 : 5.000</span>
                            </div>
                            <span id="scaleSubNote" class="text-xs font-mono text-slate-300">1 cm = 50 m</span>
                        </div>
                    </div>
                </div>
            </div>
        </section>

    </main>

    <!-- Footer -->
    <footer class="bg-[#08150e] border-t border-emerald-500/15 py-5 px-4 text-center text-xs text-slate-400 mt-10">
        <p>&copy; 2026 GeoSurvey & EcoBuild Analytics — Aplikasi Web Geomatika, Pengukuran Lahan & Konstruksi Berkelanjutan.</p>
    </footer>

    <!-- ========================================================================= -->
    <!-- SCRIPT LOGIKA APLIKASI LENGKAP                                            -->
    <!-- ========================================================================= -->
    <script>
        // =========================================================================
        // 0. SISTEM NOTIFIKASI TOAST (TANPA ALERT / CONFIRM)
        // =========================================================================
        function showToast(message, type = 'info') {
            const container = document.getElementById('toastContainer');
            const toast = document.createElement('div');
            
            const colors = {
                success: 'bg-emerald-900/95 border-emerald-400 text-emerald-100',
                warning: 'bg-amber-900/95 border-amber-400 text-amber-100',
                error: 'bg-rose-900/95 border-rose-400 text-rose-100',
                info: 'bg-slate-900/95 border-sky-400 text-sky-100'
            };
            const icons = {
                success: 'fa-circle-check text-emerald-400',
                warning: 'fa-triangle-exclamation text-amber-400',
                error: 'fa-circle-xmark text-rose-400',
                info: 'fa-circle-info text-sky-400'
            };

            toast.className = `pointer-events-auto flex items-center gap-2.5 px-4 py-3 rounded-xl border shadow-xl text-xs font-medium animate-reveal ${colors[type] || colors.info}`;
            toast.innerHTML = `<i class="fas ${icons[type] || icons.info} text-sm"></i><span>${message}</span>`;
            container.appendChild(toast);

            setTimeout(() => {
                toast.style.opacity = '0';
                toast.style.transition = 'opacity 0.3s ease';
                setTimeout(() => toast.remove(), 300);
            }, 3200);
        }

        // =========================================================================
        // 1. MODUL ANALISIS KONDISI LAHAN & PEMANFAATAN SISA BANGUNAN
        // =========================================================================
        const landForm = document.getElementById('landAnalysisForm');

        function runLandAnalysis(notify = false) {
            const p = parseFloat(document.getElementById('inputPanjang').value) || 25;
            const l = parseFloat(document.getElementById('inputLebar').value) || 15;
            const kontur = document.getElementById('inputKontur').value;
            const tanah = document.getElementById('inputTanah').value;

            const luas = p * l;
            const keliling = 2 * (p + l);
            const hektar = luas / 10000;

            document.getElementById('outLuas').textContent = `${luas.toLocaleString('id-ID')} m²`;
            document.getElementById('outHektar').textContent = `${hektar.toFixed(4)} ha`;
            document.getElementById('outKeliling').textContent = `${keliling.toLocaleString('id-ID')} m`;

            const konturLabel = kontur === 'rata' ? 'Rata' : (kontur === 'miring' ? 'Miring' : 'Menanjak');
            const tanahLabel = tanah === 'kering' ? 'Kering' : 'Basah';
            document.getElementById('outStatusTapak').textContent = `${konturLabel} & ${tanahLabel}`;

            let alatUtama = '';
            let metodeItems = [];
            let materialItems = [];

            // Penentuan Metode & Alat Pengukuran Terbaik berdasarkan kontur, luas, dan kondisi tanah
            if (kontur === 'rata') {
                if (luas <= 250) {
                    alatUtama = 'Waterpass & Pita Ukur';
                    metodeItems.push({
                        title: 'Sipat Datar (Waterpass) & Siku Pythagoras 3:4:5',
                        desc: 'Lahan relatif datar dan ringkas. Waterpass optik/selang air sudah sangat akurat untuk mengecek beda tinggi titik pojok serta pemasangan bouwplank.'
                    });
                    metodeItems.push({
                        title: 'Pita Ukur Baja / Laser Distance Meter',
                        desc: 'Mengukur jarak horizontal dan diagonal secara langsung untuk memastikan kesikuan bidang persegi.'
                    });
                } else if (luas <= 2500) {
                    alatUtama = 'Total Station & Waterpass';
                    metodeItems.push({
                        title: 'Poligon Tertutup dengan Total Station',
                        desc: 'Untuk luas > 250 m², Total Station memberikan akurasi sudut dan jarak milimetrik sehingga kesalahan penutup batas lahan minimal.'
                    });
                    metodeItems.push({
                        title: 'Grid Levelling (Sipat Datar Grid)',
                        desc: 'Membagi lahan menjadi kotak grid 5x5 m guna memetakan mikro-elevasi permukaan.'
                    });
                } else {
                    alatUtama = 'GNSS RTK & Drone Fotogrametri';
                    metodeItems.push({
                        title: 'Survei GNSS RTK (Real-Time Kinematic)',
                        desc: 'Efisien untuk luasan besar guna mengikat koordinat batas lahan ke sistem koordinat nasional dengan cepat.'
                    });
                    metodeItems.push({
                        title: 'Pemetaan Udara Drone Fotogrametri',
                        desc: 'Menghasilkan ortofoto resolusi tinggi dan Digital Surface Model (DSM) seluruh area.'
                    });
                }
            } else if (kontur === 'miring') {
                alatUtama = 'Total Station (Metode Tachymetri)';
                metodeItems.push({
                    title: 'Pengukuran Tachymetri 3D (Total Station)',
                    desc: 'Wajib digunakan pada lahan berlereng untuk merekam koordinat (X, Y, Z) secara simultan guna menghitung volume galian dan timbunan (Cut & Fill).'
                });
                metodeItems.push({
                    title: 'Profil Memanjang & Melintang (Cross Section)',
                    desc: 'Mengetahui gradien kemiringan lereng untuk perencanaan terasering dan arah aliran drainase.'
                });
            } else {
                alatUtama = 'Total Station + GNSS RTK / Drone';
                metodeItems.push({
                    title: 'Kombinasi Total Station & Drone LiDAR/Fotogrametri',
                    desc: 'Kontur menanjak curam memiliki sudut vertikal besar dan area sulit dijangkau. Total Station dengan laser tanpa prisma (reflectorless) mencegah risiko keselamatan juru ukur.'
                });
                metodeItems.push({
                    title: 'Pemetaan Garis Kontur Rapat (Interval 0.5 m)',
                    desc: 'Analisis stabilitas lereng dan penentuan posisi dinding penahan tanah (retaining wall).'
                });
            }

            if (tanah === 'basah') {
                alatUtama += ' + Tripod Statif Berat';
                metodeItems.push({
                    title: 'Protokol Lahan Basah / Gambut',
                    desc: 'Gunakan alas papan/patok kayu pancang di bawah kaki statif (tripod) agar alat tidak amblas selama pembacaan sudut dan elevasi.'
                });
            }

            document.getElementById('outAlatUtama').textContent = alatUtama;

            // Penentuan Pemanfaatan Material Sisa Bangunan
            if (tanah === 'basah') {
                materialItems.push({
                    icon: 'fa-cubes text-amber-400',
                    title: 'Puing Beton & Pecahan Bata → Sub-Grade Stabilisasi',
                    desc: 'Hancurkan puing beton/bata menjadi agregat kasar (ukuran 5–10 cm) sebagai lapisan urugan dasar (stabilisasi tanah lembek/berair) sebelum pemadatan sirtu.'
                });
            } else if (kontur !== 'rata') {
                materialItems.push({
                    icon: 'fa-mound text-amber-400',
                    title: 'Puing Batu & Beton → Isian Bronjong (Gabion)',
                    desc: 'Bongkahan beton dan batu sisa sangat ideal mengisi kawat bronjong sebagai dinding penahan lereng untuk mencegah erosi pada kontur miring/menanjak.'
                });
            } else {
                materialItems.push({
                    icon: 'fa-road text-amber-400',
                    title: 'Puing Bata & Beton → Perkerasan Jalan Kerja / Carport',
                    desc: 'Dimanfaatkan sebagai lapisan pondasi bawah (base course) untuk jalan akses proyek, area parkir, atau urugan peninggian lantai kerja.'
                });
            }

            materialItems.push({
                icon: 'fa-tree text-emerald-400',
                title: 'Sisa Kayu Bekisting & Bambu → Patok Bouwplank & Perancah',
                desc: 'Potongan kayu kaso/papan dapat diolah ulang menjadi patok titik ukur lapangan, pagar pengaman sementara, atau kotak kompos tapak.'
            });

            materialItems.push({
                icon: 'fa-hill-rockslide text-orange-400',
                title: 'Tanah Galian Pondasi → Timbunan Cut & Fill / Lanskap',
                desc: kontur !== 'rata'
                    ? 'Gunakan tanah galian sisi atas untuk menimbun sisi kontur rendah (sistem Cut & Fill seimbang) agar hemat biaya angkut material.'
                    : 'Manfaatkan untuk peninggian elevasi halaman, tanggul drainase keliling, dan media tanam penghijauan.'
            });

            materialItems.push({
                icon: 'fa-link text-sky-400',
                title: 'Potongan Besi Tulangan → Terali, Angkur & Penguat Pagar',
                desc: 'Sisa besi beton dapat disambung/dilas untuk tulangan praktis non-struktural, patok permanen (BM lokal), atau rangka rambatan tanaman.'
            });

            // Render ke DOM
            document.getElementById('outMetodeList').innerHTML = metodeItems.map(item => `
                <div class="p-3 rounded-lg bg-slate-900/75 border border-emerald-500/15">
                    <div class="font-semibold text-white mb-0.5">${item.title}</div>
                    <p class="text-xs text-slate-300">${item.desc}</p>
                </div>
            `).join('');

            document.getElementById('outMaterialList').innerHTML = materialItems.map(item => `
                <div class="p-2.5 rounded-lg bg-slate-900/75 border border-amber-500/15 flex items-start gap-2.5">
                    <i class="fas ${item.icon} mt-1"></i>
                    <div>
                        <div class="font-semibold text-white text-xs">${item.title}</div>
                        <p class="text-[11px] text-slate-300 mt-0.5">${item.desc}</p>
                    </div>
                </div>
            `).join('');

            if (notify) {
                showToast(`Analisis selesai untuk lahan ${p}m × ${l}m (${luas} m²).`, 'success');
            }
        }

        landForm.addEventListener('submit', (e) => {
            e.preventDefault();
            runLandAnalysis(true);
        });

        // =========================================================================
        // 2. MODUL VISUAL 2D DENAH LAHAN INTERAKTIF (KANVAS GRID, GARIS & IKON)
        // =========================================================================
        const blueprintCanvas = document.getElementById('blueprintCanvas');
        const bpCtx = blueprintCanvas.getContext('2d');
        const unitSelect2D = document.getElementById('unitSelect2D');
        const gridScale2DInput = document.getElementById('gridScale2D');
        const snapGridCheck = document.getElementById('snapGridCheck');
        const lineVarInput = document.getElementById('lineVarInput');
        const modeDrawLineBtn = document.getElementById('modeDrawLineBtn');
        const activeToolBadge = document.getElementById('activeToolBadge');
        const canvasHintText = document.getElementById('canvasHintText');

        // Faktor konversi dari meter (m) ke satuan pilihan
        const METER_TO_UNIT = {
            mm: 1000,
            cm: 100,
            dm: 10,
            m: 1,
            dam: 0.1,
            hm: 0.01,
            km: 0.001
        };

        const GRID_PX = 40; // 1 kotak grid = 40 CSS pixels
        let bpLines = [];   // { id, varName, x1, y1, x2, y2 } dalam koordinat CSS pixel
        let bpIcons = [];   // { id, tool, label, x, y }
        let showLandBoundary = true;

        // State interaksi menggambar
        let interactionMode = 'line'; // 'line' atau 'icon'
        let selectedInstrument = null; // { tool, label }
        let isDraggingLine = false;
        let pendingStartPoint = null;
        let currentPointerPos = null;
        let varCounter = 1;
        const alphabet = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ';

        function getNextDefaultVarName() {
            const a = alphabet[(varCounter - 1) % 26];
            const b = alphabet[varCounter % 26];
            return `Garis ${a}-${b}`;
        }

        function resizeBlueprintCanvas() {
            const wrapper = document.getElementById('canvas2DWrapper');
            const rect = wrapper.getBoundingClientRect();
            const dpr = window.devicePixelRatio || 1;
            blueprintCanvas.width = rect.width * dpr;
            blueprintCanvas.height = rect.height * dpr;
            bpCtx.setTransform(dpr, 0, 0, dpr, 0, 0);
            renderBlueprint();
        }

        // Format panjang berdasarkan satuan pilihan
        function formatLengthFromGridPixels(pixelDist) {
            const scaleMetersPerGrid = parseFloat(gridScale2DInput.value) || 1;
            const unit = unitSelect2D.value;
            const gridCells = pixelDist / GRID_PX;
            const lengthInMeters = gridCells * scaleMetersPerGrid;
            const convertedVal = lengthInMeters * METER_TO_UNIT[unit];

            let formattedNum;
            if (convertedVal >= 1000) {
                formattedNum = convertedVal.toLocaleString('id-ID', { maximumFractionDigits: 2 });
            } else if (convertedVal >= 1) {
                formattedNum = convertedVal.toLocaleString('id-ID', { maximumFractionDigits: 3 });
            } else {
                formattedNum = convertedVal.toLocaleString('id-ID', { maximumFractionDigits: 5 });
            }
            return `${formattedNum} ${unit}`;
        }

        // Hitung kemiringan (%) dan derajat sudut (°)
        function calculateSlopeAndAngle(x1, y1, x2, y2) {
            const dx = x2 - x1;
            const dy = -(y2 - y1); // Y ke atas positif secara kartesius
            const absDx = Math.abs(dx);
            const absDy = Math.abs(dy);

            // Sudut kemiringan elevasi relatif terhadap bidang horizontal (0° - 90°)
            const angleRad = Math.atan2(absDy, absDx);
            const angleDeg = (angleRad * 180) / Math.PI;

            // Persentase kemiringan (Rise / Run * 100%)
            let slopeText;
            if (absDx < 0.5 && absDy > 0.5) {
                slopeText = '∞% (Tegak)';
            } else if (absDx < 0.5 && absDy < 0.5) {
                slopeText = '0.0%';
            } else {
                const slopePct = (absDy / absDx) * 100;
                slopeText = `${slopePct.toFixed(1)}%`;
            }

            return {
                angleDeg: angleDeg.toFixed(1),
                slopeText
            };
        }

        // Gambar ikon vektor alat ukur geomatika di kanvas
        function drawInstrumentVector(ctx, iconObj) {
            const { x, y, tool, label } = iconObj;
            ctx.save();
            ctx.translate(x, y);

            // Lingkaran latar ikon
            ctx.beginPath();
            ctx.arc(0, 0, 18, 0, Math.PI * 2);
            ctx.fillStyle = 'rgba(15, 23, 42, 0.92)';
            ctx.fill();
            ctx.lineWidth = 2;

            const colors = {
                total_station: '#10b981',
                prisma: '#f59e0b',
                gps_rtk: '#38bdf8',
                waterpass: '#2dd4bf',
                theodolite: '#c084fc',
                drone: '#fb7185'
            };
            const color = colors[tool] || '#10b981';
            ctx.strokeStyle = color;
            ctx.stroke();

            // Simbol teknis alat di dalam lingkaran
            ctx.strokeStyle = color;
            ctx.fillStyle = color;
            ctx.lineWidth = 1.8;

            if (tool === 'total_station' || tool === 'theodolite' || tool === 'waterpass') {
                // Kaki tripod
                ctx.beginPath();
                ctx.moveTo(0, -2);
                ctx.lineTo(-8, 10);
                ctx.moveTo(0, -2);
                ctx.lineTo(8, 10);
                ctx.moveTo(0, -2);
                ctx.lineTo(0, 11);
                ctx.stroke();
                // Kepala optik
                ctx.fillRect(-6, -8, 12, 6);
            } else if (tool === 'prisma') {
                // Tiang & Belah ketupat prisma
                ctx.beginPath();
                ctx.moveTo(0, 2);
                ctx.lineTo(0, 11);
                ctx.stroke();
                ctx.beginPath();
                ctx.moveTo(0, -10);
                ctx.lineTo(7, -3);
                ctx.lineTo(0, 4);
                ctx.lineTo(-7, -3);
                ctx.closePath();
                ctx.fill();
            } else if (tool === 'gps_rtk') {
                // Pole + Antena Dome + Gelombang sinyal
                ctx.beginPath();
                ctx.moveTo(0, -2);
                ctx.lineTo(0, 11);
                ctx.stroke();
                ctx.beginPath();
                ctx.arc(0, -4, 6, Math.PI, 0);
                ctx.fill();
                ctx.beginPath();
                ctx.arc(0, -5, 10, Math.PI * 1.2, Math.PI * 1.8);
                ctx.stroke();
            } else if (tool === 'drone') {
                // X-frame Quadcopter
                ctx.beginPath();
                ctx.moveTo(-8, -8);
                ctx.lineTo(8, 8);
                ctx.moveTo(8, -8);
                ctx.lineTo(-8, 8);
                ctx.stroke();
                [[-8, -8], [8, -8], [-8, 8], [8, 8]].forEach(([rx, ry]) => {
                    ctx.beginPath();
                    ctx.arc(rx, ry, 3, 0, Math.PI * 2);
                    ctx.stroke();
                });
            }

            // Label nama alat di bawah ikon
            ctx.font = '600 10px Inter, sans-serif';
            const textW = ctx.measureText(label).width;
            ctx.fillStyle = 'rgba(2, 6, 23, 0.85)';
            ctx.fillRect(-textW / 2 - 5, 21, textW + 10, 16);
            ctx.fillStyle = '#f8fafc';
            ctx.textAlign = 'center';
            ctx.textBaseline = 'middle';
            ctx.fillText(label, 0, 29);

            ctx.restore();
        }

        // Gambar satu segmen garis beserta badge informasi tepat di atas garis
        function drawAnnotatedLine(ctx, line, isPreview = false) {
            const { x1, y1, x2, y2, varName } = line;
            const pixelDist = Math.hypot(x2 - x1, y2 - y1);
            if (pixelDist < 2) return;

            ctx.save();
            // Garis utama
            ctx.beginPath();
            ctx.moveTo(x1, y1);
            ctx.lineTo(x2, y2);
            ctx.strokeStyle = isPreview ? '#38bdf8' : '#10b981';
            ctx.lineWidth = isPreview ? 2.5 : 3;
            if (isPreview) ctx.setLineDash([6, 4]);
            ctx.stroke();
            ctx.setLineDash([]);

            // Titik simpul awal & akhir
            [[x1, y1], [x2, y2]].forEach(([px, py]) => {
                ctx.beginPath();
                ctx.arc(px, py, 4.5, 0, Math.PI * 2);
                ctx.fillStyle = isPreview ? '#38bdf8' : '#fbbf24';
                ctx.fill();
                ctx.strokeStyle = '#0f172a';
                ctx.lineWidth = 1.5;
                ctx.stroke();
            });

            // Hitung metrik untuk label di atas garis
            const lengthLabel = formatLengthFromGridPixels(pixelDist);
            const { angleDeg, slopeText } = calculateSlopeAndAngle(x1, y1, x2, y2);
            const infoText = `${varName}: ${lengthLabel} | Kemiringan: ${slopeText} | Sudut: ${angleDeg}°`;

            // Posisi tengah garis & rotasi agar teks mengikuti arah garis tanpa terbalik
            const midX = (x1 + x2) / 2;
            const midY = (y1 + y2) / 2;
            let textAngle = Math.atan2(y2 - y1, x2 - x1);
            if (textAngle > Math.PI / 2 || textAngle < -Math.PI / 2) {
                textAngle += Math.PI;
            }

            ctx.translate(midX, midY);
            ctx.rotate(textAngle);

            ctx.font = '600 11px "JetBrains Mono", monospace';
            const metrics = ctx.measureText(infoText);
            const padX = 8;
            const boxW = metrics.width + padX * 2;
            const boxH = 22;
            const offsetY = -18; // Tepat di atas garis

            // Kotak latar label
            ctx.fillStyle = isPreview ? 'rgba(14, 116, 144, 0.92)' : 'rgba(6, 78, 59, 0.94)';
            ctx.strokeStyle = isPreview ? '#38bdf8' : '#34d399';
            ctx.lineWidth = 1;
            ctx.beginPath();
            ctx.roundRect(-boxW / 2, offsetY - boxH / 2, boxW, boxH, 6);
            ctx.fill();
            ctx.stroke();

            // Teks informasi panjang, kemiringan & derajat
            ctx.fillStyle = '#ffffff';
            ctx.textAlign = 'center';
            ctx.textBaseline = 'middle';
            ctx.fillText(infoText, 0, offsetY);

            ctx.restore();
        }

        function renderBlueprint() {
            const width = blueprintCanvas.width / (window.devicePixelRatio || 1);
            const height = blueprintCanvas.height / (window.devicePixelRatio || 1);

            // Latar CAD gelap
            bpCtx.clearRect(0, 0, width, height);
            bpCtx.fillStyle = '#09141c';
            bpCtx.fillRect(0, 0, width, height);

            // Gambar Grid Koordinat
            bpCtx.lineWidth = 1;
            for (let x = 0; x <= width; x += GRID_PX) {
                bpCtx.beginPath();
                bpCtx.moveTo(x, 0);
                bpCtx.lineTo(x, height);
                bpCtx.strokeStyle = (x % (GRID_PX * 5) === 0) ? 'rgba(52, 211, 153, 0.22)' : 'rgba(148, 163, 184, 0.09)';
                bpCtx.stroke();
            }
            for (let y = 0; y <= height; y += GRID_PX) {
                bpCtx.beginPath();
                bpCtx.moveTo(0, y);
                bpCtx.lineTo(width, y);
                bpCtx.strokeStyle = (y % (GRID_PX * 5) === 0) ? 'rgba(52, 211, 153, 0.22)' : 'rgba(148, 163, 184, 0.09)';
                bpCtx.stroke();
            }

            // Indikator Skala Grid di pojok kiri atas
            const scaleVal = parseFloat(gridScale2DInput.value) || 1;
            bpCtx.fillStyle = 'rgba(15, 23, 42, 0.85)';
            bpCtx.fillRect(10, 10, 185, 24);
            bpCtx.strokeStyle = 'rgba(16, 185, 129, 0.4)';
            bpCtx.strokeRect(10, 10, 185, 24);
            bpCtx.fillStyle = '#6ee7b7';
            bpCtx.font = '600 11px "JetBrains Mono", monospace';
            bpCtx.textAlign = 'left';
            bpCtx.textBaseline = 'middle';
            bpCtx.fillText(`1 Grid = ${formatLengthFromGridPixels(GRID_PX)}`, 18, 22);

            // Gambar kotak referensi denah lahan dari input Modul 1 (jika aktif)
            if (showLandBoundary) {
                const pMeters = parseFloat(document.getElementById('inputPanjang').value) || 25;
                const lMeters = parseFloat(document.getElementById('inputLebar').value) || 15;
                const boxPxW = Math.min(width - 80, (pMeters / scaleVal) * GRID_PX);
                const boxPxH = Math.min(height - 80, (lMeters / scaleVal) * GRID_PX);
                const startX = (width - boxPxW) / 2;
                const startY = (height - boxPxH) / 2;

                bpCtx.save();
                bpCtx.strokeStyle = 'rgba(245, 158, 11, 0.55)';
                bpCtx.fillStyle = 'rgba(245, 158, 11, 0.05)';
                bpCtx.lineWidth = 1.5;
                bpCtx.setLineDash([8, 5]);
                bpCtx.fillRect(startX, startY, boxPxW, boxPxH);
                bpCtx.strokeRect(startX, startY, boxPxW, boxPxH);
                bpCtx.setLineDash([]);

                bpCtx.fillStyle = '#fcd34d';
                bpCtx.font = '500 11px Inter, sans-serif';
                bpCtx.textAlign = 'left';
                bpCtx.fillText(`Batas Tapak Referensi (${pMeters}m × ${lMeters}m)`, startX + 6, startY - 8);
                bpCtx.restore();
            }

            // Gambar semua garis tersimpan
            bpLines.forEach(line => drawAnnotatedLine(bpCtx, line, false));

            // Gambar preview garis yang sedang ditarik
            if (pendingStartPoint && currentPointerPos) {
                const varName = lineVarInput.value.trim() || getNextDefaultVarName();
                drawAnnotatedLine(bpCtx, {
                    x1: pendingStartPoint.x,
                    y1: pendingStartPoint.y,
                    x2: currentPointerPos.x,
                    y2: currentPointerPos.y,
                    varName
                }, true);
            }

            // Gambar semua ikon alat ukur terpasang
            bpIcons.forEach(iconObj => drawInstrumentVector(bpCtx, iconObj));
        }

        function updateBlueprintSummaryTables() {
            const tbody = document.getElementById('linesTableBody');
            const lineCountBadge = document.getElementById('lineCountBadge');
            lineCountBadge.textContent = `${bpLines.length} Garis`;

            if (bpLines.length === 0) {
                tbody.innerHTML = `<tr><td colspan="5" class="py-4 text-center text-slate-400 font-sans">Belum ada garis tambahan. Silakan gambar pada kanvas di atas.</td></tr>`;
            } else {
                tbody.innerHTML = bpLines.map((line, idx) => {
                    const dist = Math.hypot(line.x2 - line.x1, line.y2 - line.y1);
                    const lenStr = formatLengthFromGridPixels(dist);
                    const { angleDeg, slopeText } = calculateSlopeAndAngle(line.x1, line.y1, line.x2, line.y2);
                    return `
                        <tr class="hover:bg-white/5">
                            <td class="py-2 px-2 text-emerald-300 font-semibold">${line.varName}</td>
                            <td class="py-2 px-2 text-white">${lenStr}</td>
                            <td class="py-2 px-2 text-amber-300">${slopeText}</td>
                            <td class="py-2 px-2 text-sky-300">${angleDeg}°</td>
                            <td class="py-2 px-2 text-right">
                                <button type="button" onclick="deleteBlueprintLine(${idx})" class="text-rose-400 hover:text-rose-300 px-1.5 py-0.5 rounded bg-rose-950/50 cursor-pointer" title="Hapus Garis">
                                    <i class="fas fa-times"></i>
                                </button>
                            </td>
                        </tr>
                    `;
                }).join('');
            }

            const iconsListEl = document.getElementById('iconsListSummary');
            const iconCountBadge = document.getElementById('iconCountBadge');
            iconCountBadge.textContent = `${bpIcons.length} Alat`;

            if (bpIcons.length === 0) {
                iconsListEl.innerHTML = `<li class="text-slate-400 py-3 text-center">Belum ada ikon alat ukur ditempelkan.</li>`;
            } else {
                iconsListEl.innerHTML = bpIcons.map((ic, idx) => `
                    <li class="flex items-center justify-between bg-slate-900/80 px-2.5 py-1.5 rounded-lg border border-white/10">
                        <span><strong class="text-amber-300">#${idx + 1}</strong> ${ic.label} <span class="text-slate-400 font-mono text-[10px]">(${Math.round(ic.x)}, ${Math.round(ic.y)})</span></span>
                        <button type="button" onclick="deleteBlueprintIcon(${idx})" class="text-rose-400 hover:text-rose-300 cursor-pointer">
                            <i class="fas fa-trash-can"></i>
                        </button>
                    </li>
                `).join('');
            }
        }

        window.deleteBlueprintLine = function(idx) {
            bpLines.splice(idx, 1);
            renderBlueprint();
            updateBlueprintSummaryTables();
        };

        window.deleteBlueprintIcon = function(idx) {
            bpIcons.splice(idx, 1);
            renderBlueprint();
            updateBlueprintSummaryTables();
        };

        function getCanvasCoords(evt) {
            const rect = blueprintCanvas.getBoundingClientRect();
            const clientX = evt.touches ? evt.touches[0].clientX : evt.clientX;
            const clientY = evt.touches ? evt.touches[0].clientY : evt.clientY;
            let x = clientX - rect.left;
            let y = clientY - rect.top;

            if (snapGridCheck.checked) {
                x = Math.round(x / (GRID_PX / 2)) * (GRID_PX / 2);
                y = Math.round(y / (GRID_PX / 2)) * (GRID_PX / 2);
            }
            return { x, y };
        }

        function handlePointerDown2D(evt) {
            if (evt.cancelable) evt.preventDefault();
            const pos = getCanvasCoords(evt);

            if (interactionMode === 'icon' && selectedInstrument) {
                bpIcons.push({
                    id: Date.now(),
                    tool: selectedInstrument.tool,
                    label: selectedInstrument.label,
                    x: pos.x,
                    y: pos.y
                });
                renderBlueprint();
                updateBlueprintSummaryTables();
                showToast(`Ikon ${selectedInstrument.label} ditempatkan pada denah.`, 'info');
                return;
            }

            // Mode gambar garis: dukung klik-tarik maupun klik-titik-awal lalu klik-titik-akhir
            if (!pendingStartPoint) {
                pendingStartPoint = pos;
                currentPointerPos = pos;
                isDraggingLine = true;
            } else {
                // Menyelesaikan garis pada ketukan kedua
                finalizeLine2D(pos);
            }
            renderBlueprint();
        }

        function handlePointerMove2D(evt) {
            if (!pendingStartPoint) return;
            if (evt.cancelable) evt.preventDefault();
            currentPointerPos = getCanvasCoords(evt);
            renderBlueprint();
        }

        function handlePointerUp2D(evt) {
            if (!isDraggingLine || !pendingStartPoint) return;
            isDraggingLine = false;
            const endPos = currentPointerPos || pendingStartPoint;
            const dist = Math.hypot(endPos.x - pendingStartPoint.x, endPos.y - pendingStartPoint.y);

            // Jika pengguna menarik garis sejauh > 12px, langsung selesaikan garis
            // Jika hanya klik di tempat (< 12px), biarkan titik awal menunggu ketukan kedua!
            if (dist > 12) {
                finalizeLine2D(endPos);
            }
            renderBlueprint();
        }

        function finalizeLine2D(endPos) {
            const dist = Math.hypot(endPos.x - pendingStartPoint.x, endPos.y - pendingStartPoint.y);
            if (dist >= 5) {
                const varName = lineVarInput.value.trim() || getNextDefaultVarName();
                bpLines.push({
                    id: Date.now(),
                    varName,
                    x1: pendingStartPoint.x,
                    y1: pendingStartPoint.y,
                    x2: endPos.x,
                    y2: endPos.y
                });
                varCounter++;
                lineVarInput.value = getNextDefaultVarName();
                updateBlueprintSummaryTables();
            }
            pendingStartPoint = null;
            currentPointerPos = null;
        }

        blueprintCanvas.addEventListener('mousedown', handlePointerDown2D);
        blueprintCanvas.addEventListener('mousemove', handlePointerMove2D);
        window.addEventListener('mouseup', handlePointerUp2D);

        blueprintCanvas.addEventListener('touchstart', handlePointerDown2D, { passive: false });
        blueprintCanvas.addEventListener('touchmove', handlePointerMove2D, { passive: false });
        blueprintCanvas.addEventListener('touchend', handlePointerUp2D);

        // Toolbar pemilihan alat ukur vs mode garis
        const toolButtons = document.querySelectorAll('.tool-btn');
        toolButtons.forEach(btn => {
            btn.addEventListener('click', () => {
                interactionMode = 'icon';
                selectedInstrument = {
                    tool: btn.getAttribute('data-tool'),
                    label: btn.getAttribute('data-label')
                };
                pendingStartPoint = null;
                currentPointerPos = null;

                toolButtons.forEach(b => b.classList.remove('ring-2', 'ring-amber-400', 'bg-amber-950/60'));
                btn.classList.add('ring-2', 'ring-amber-400', 'bg-amber-950/60');

                modeDrawLineBtn.classList.replace('bg-emerald-600', 'bg-slate-800');
                modeDrawLineBtn.innerHTML = `<i class="fas fa-pen mr-1"></i> Kembali Gambar Garis`;
                activeToolBadge.textContent = `Mode Alat: ${selectedInstrument.label}`;
                activeToolBadge.className = 'text-[11px] px-2 py-0.5 rounded bg-amber-500/20 text-amber-300 border border-amber-500/30';
                canvasHintText.innerHTML = `<i class="fas fa-location-dot text-amber-400 mr-1"></i> Klik/ketuk posisi pada denah untuk meletakkan <strong>${selectedInstrument.label}</strong>.`;
            });
        });

        modeDrawLineBtn.addEventListener('click', () => {
            interactionMode = 'line';
            selectedInstrument = null;
            toolButtons.forEach(b => b.classList.remove('ring-2', 'ring-amber-400', 'bg-amber-950/60'));

            modeDrawLineBtn.classList.replace('bg-slate-800', 'bg-emerald-600');
            modeDrawLineBtn.innerHTML = `<i class="fas fa-check-circle mr-1"></i> Aktif: Gambar Garis`;
            activeToolBadge.textContent = 'Mode: Gambar Garis';
            activeToolBadge.className = 'text-[11px] px-2 py-0.5 rounded bg-emerald-500/20 text-emerald-300 border border-emerald-500/30';
            canvasHintText.innerHTML = `<i class="fas fa-hand-pointer text-emerald-400 mr-1"></i> Klik & tarik (atau klik titik awal lalu klik titik akhir) untuk menggambar garis bervariabel.`;
        });

        // Update real-time ketika satuan atau faktor skala diubah
        unitSelect2D.addEventListener('change', () => {
            renderBlueprint();
            updateBlueprintSummaryTables();
        });
        gridScale2DInput.addEventListener('input', () => {
            renderBlueprint();
            updateBlueprintSummaryTables();
        });

        document.getElementById('toggleLandBoxBtn').addEventListener('click', () => {
            showLandBoundary = !showLandBoundary;
            renderBlueprint();
        });

        document.getElementById('syncToCanvasBtn').addEventListener('click', () => {
            showLandBoundary = true;
            renderBlueprint();
            document.getElementById('modul-denah').scrollIntoView({ behavior: 'smooth' });
            showToast('Dimensi lahan diterapkan ke kotak referensi Denah 2D.', 'success');
        });

        document.getElementById('undoLineBtn').addEventListener('click', () => {
            if (bpLines.length > 0) {
                bpLines.pop();
                renderBlueprint();
                updateBlueprintSummaryTables();
            }
        });

        document.getElementById('undoIconBtn').addEventListener('click', () => {
            if (bpIcons.length > 0) {
                bpIcons.pop();
                renderBlueprint();
                updateBlueprintSummaryTables();
            }
        });

        document.getElementById('resetCanvas2DBtn').addEventListener('click', () => {
            bpLines = [];
            bpIcons = [];
            pendingStartPoint = null;
            currentPointerPos = null;
            varCounter = 1;
            lineVarInput.value = getNextDefaultVarName();
            renderBlueprint();
            updateBlueprintSummaryTables();
            showToast('Denah 2D berhasil direset.', 'warning');
        });

        // =========================================================================
        // 3. MODUL KAMERA PENGUKUR LAHAN (AR SEDERHANA & AMBIL FOTO)
        // =========================================================================
        const toggleCameraBtn = document.getElementById('toggleCameraBtn');
        const toggleCamIcon = document.getElementById('toggleCamIcon');
        const toggleCamText = document.getElementById('toggleCamText');
        const arVideoFeed = document.getElementById('arVideoFeed');
        const arPhotoPreview = document.getElementById('arPhotoPreview');
        const cameraPlaceholder = document.getElementById('cameraPlaceholder');
        const arOverlayCanvas = document.getElementById('arOverlayCanvas');
        const arCtx = arOverlayCanvas.getContext('2d');
        const arHudBadge = document.getElementById('arHudBadge');
        const liveCameraControls = document.getElementById('liveCameraControls');
        const capturedPhotoControls = document.getElementById('capturedPhotoControls');
        const arScaleFactorInput = document.getElementById('arScaleFactor');

        let arStream = null;
        let isSimulationCam = false;
        let arPoints = []; // Array of { x, y } normalized or CSS pixel coordinates

        function syncArCanvasSize() {
            const container = document.getElementById('cameraViewportContainer');
            const rect = container.getBoundingClientRect();
            const dpr = window.devicePixelRatio || 1;
            arOverlayCanvas.width = rect.width * dpr;
            arOverlayCanvas.height = rect.height * dpr;
            arCtx.setTransform(dpr, 0, 0, dpr, 0, 0);
            renderArOverlay();
        }

        function renderArOverlay() {
            const w = arOverlayCanvas.width / (window.devicePixelRatio || 1);
            const h = arOverlayCanvas.height / (window.devicePixelRatio || 1);
            arCtx.clearRect(0, 0, w, h);

            // Jika mode simulasi tanpa webcam fisik, tampilkan grid bidik halus
            if (isSimulationCam) {
                arCtx.fillStyle = 'rgba(6, 24, 18, 0.85)';
                arCtx.fillRect(0, 0, w, h);
                arCtx.strokeStyle = 'rgba(56, 189, 248, 0.15)';
                arCtx.lineWidth = 1;
                for (let gx = 0; gx < w; gx += 40) {
                    arCtx.beginPath(); arCtx.moveTo(gx, 0); arCtx.lineTo(gx, h); arCtx.stroke();
                }
                for (let gy = 0; gy < h; gy += 40) {
                    arCtx.beginPath(); arCtx.moveTo(0, gy); arCtx.lineTo(w, gy); arCtx.stroke();
                }
                arCtx.fillStyle = 'rgba(148, 163, 184, 0.6)';
                arCtx.font = '500 12px Inter, sans-serif';
                arCtx.textAlign = 'center';
                arCtx.fillText('Mode Simulasi Bidik AR (Ketuk layar untuk menaruh titik +)', w / 2, h - 18);
            }

            const metersPer100Px = parseFloat(arScaleFactorInput.value) || 1.0;
            let cumulativeMeters = 0;
            const segmentsHtml = [];

            // Gambar garis penghubung antar titik
            for (let i = 1; i < arPoints.length; i++) {
                const p1 = arPoints[i - 1];
                const p2 = arPoints[i];
                const pxDist = Math.hypot(p2.x - p1.x, p2.y - p1.y);
                const segMeters = (pxDist / 100) * metersPer100Px;
                cumulativeMeters += segMeters;

                // Garis neon hijau-kuning
                arCtx.beginPath();
                arCtx.moveTo(p1.x, p1.y);
                arCtx.lineTo(p2.x, p2.y);
                arCtx.strokeStyle = '#34d399';
                arCtx.lineWidth = 3;
                arCtx.stroke();

                // Label estimasi jarak di tengah segmen
                const mx = (p1.x + p2.x) / 2;
                const my = (p1.y + p2.y) / 2;
                const label = `${segMeters.toFixed(2)} m`;
                arCtx.font = '700 12px "JetBrains Mono", monospace';
                const tw = arCtx.measureText(label).width;
                arCtx.fillStyle = 'rgba(2, 6, 23, 0.85)';
                arCtx.beginPath();
                arCtx.roundRect(mx - tw / 2 - 6, my - 12, tw + 12, 22, 6);
                arCtx.fill();
                arCtx.strokeStyle = '#34d399';
                arCtx.lineWidth = 1;
                arCtx.stroke();

                arCtx.fillStyle = '#fef08a';
                arCtx.textAlign = 'center';
                arCtx.textBaseline = 'middle';
                arCtx.fillText(label, mx, my - 1);

                segmentsHtml.push(`
                    <li class="flex justify-between bg-slate-900/80 px-2.5 py-1.5 rounded border border-white/10">
                        <span>Titik ${i} → Titik ${i + 1}</span>
                        <strong class="text-emerald-300">${segMeters.toFixed(2)} m</strong>
                    </li>
                `);
            }

            // Gambar setiap tanda '+' titik ukur
            arPoints.forEach((pt, index) => {
                arCtx.save();
                arCtx.translate(pt.x, pt.y);

                // Lingkaran target luar
                arCtx.beginPath();
                arCtx.arc(0, 0, 14, 0, Math.PI * 2);
                arCtx.fillStyle = 'rgba(16, 185, 129, 0.25)';
                arCtx.fill();
                arCtx.strokeStyle = '#fbbf24';
                arCtx.lineWidth = 2;
                arCtx.stroke();

                // Tanda '+'
                arCtx.beginPath();
                arCtx.moveTo(-8, 0);
                arCtx.lineTo(8, 0);
                arCtx.moveTo(0, -8);
                arCtx.lineTo(0, 8);
                arCtx.strokeStyle = '#ffffff';
                arCtx.lineWidth = 2.5;
                arCtx.stroke();

                // Nomor titik
                arCtx.fillStyle = '#fbbf24';
                arCtx.font = '700 11px "JetBrains Mono", monospace';
                arCtx.fillText(`P${index + 1}`, 14, -12);
                arCtx.restore();
            });

            // Update HUD & Daftar segmen
            document.getElementById('arPointCount').textContent = arPoints.length;
            document.getElementById('arTotalDist').textContent = `${cumulativeMeters.toFixed(2)} m`;

            const segListEl = document.getElementById('arSegmentsList');
            if (arPoints.length === 0) {
                segListEl.innerHTML = `<li class="text-slate-400 font-sans">Belum ada titik ditempatkan pada layar kamera.</li>`;
            } else if (arPoints.length === 1) {
                segListEl.innerHTML = `<li class="text-amber-300 font-sans">Titik awal (P1) terpasang. Ketuk titik kedua untuk mengukur jarak.</li>`;
            } else {
                segListEl.innerHTML = segmentsHtml.join('');
            }
        }

        function handleArCanvasTap(evt) {
            if (evt.cancelable) evt.preventDefault();
            const rect = arOverlayCanvas.getBoundingClientRect();
            const clientX = evt.touches ? evt.touches[0].clientX : evt.clientX;
            const clientY = evt.touches ? evt.touches[0].clientY : evt.clientY;
            arPoints.push({
                x: clientX - rect.left,
                y: clientY - rect.top
            });
            renderArOverlay();
        }

        arOverlayCanvas.addEventListener('mousedown', handleArCanvasTap);
        arOverlayCanvas.addEventListener('touchstart', handleArCanvasTap, { passive: false });
        arScaleFactorInput.addEventListener('input', renderArOverlay);

        toggleCameraBtn.addEventListener('click', async () => {
            if (arStream || isSimulationCam) {
                // Matikan Kamera
                if (arStream) {
                    arStream.getTracks().forEach(t => t.stop());
                    arStream = null;
                }
                isSimulationCam = false;
                arVideoFeed.srcObject = null;
                arVideoFeed.classList.add('hidden');
                arPhotoPreview.classList.add('hidden');
                arOverlayCanvas.classList.add('hidden');
                arHudBadge.classList.add('hidden');
                cameraPlaceholder.classList.remove('hidden');
                liveCameraControls.classList.add('hidden');
                capturedPhotoControls.classList.add('hidden');

                toggleCameraBtn.className = 'w-full py-3 px-4 rounded-xl bg-emerald-600 hover:bg-emerald-500 text-white font-semibold text-sm shadow transition flex items-center justify-center gap-2 cursor-pointer';
                toggleCamIcon.className = 'fas fa-play';
                toggleCamText.textContent = 'Mulai Kamera';
                return;
            }

            // Mulai Kamera (Prioritas Kamera Belakang)
            try {
                if (!navigator.mediaDevices || !navigator.mediaDevices.getUserMedia) {
                    throw new Error('MediaDevices tidak didukung');
                }
                try {
                    arStream = await navigator.mediaDevices.getUserMedia({
                        video: { facingMode: { ideal: 'environment' } },
                        audio: false
                    });
                } catch {
                    arStream = await navigator.mediaDevices.getUserMedia({ video: true, audio: false });
                }

                arVideoFeed.srcObject = arStream;
                arVideoFeed.classList.remove('hidden');
                isSimulationCam = false;
                showToast('Kamera aktif! Ketuk layar untuk meletakkan titik + pengukuran.', 'success');
            } catch (err) {
                // Fallback mode simulasi interaktif jika izin kamera ditolak / perangkat tanpa webcam
                isSimulationCam = true;
                arVideoFeed.classList.add('hidden');
                showToast('Kamera fisik tidak terdeteksi/diizinkan. Menggunakan Mode Bidik AR Simulasi.', 'warning');
            }

            cameraPlaceholder.classList.add('hidden');
            arPhotoPreview.classList.add('hidden');
            arOverlayCanvas.classList.remove('hidden');
            arHudBadge.classList.remove('hidden');
            liveCameraControls.classList.remove('hidden');
            capturedPhotoControls.classList.add('hidden');

            toggleCameraBtn.className = 'w-full py-3 px-4 rounded-xl bg-rose-700 hover:bg-rose-600 text-white font-semibold text-sm shadow transition flex items-center justify-center gap-2 cursor-pointer';
            toggleCamIcon.className = 'fas fa-stop';
            toggleCamText.textContent = 'Matikan Kamera';

            syncArCanvasSize();
        });

        document.getElementById('resetArPointsBtn').addEventListener('click', () => {
            arPoints = [];
            renderArOverlay();
            showToast('Titik pengukuran kamera direset.', 'info');
        });

        document.getElementById('captureArPhotoBtn').addEventListener('click', () => {
            const captureCanvas = document.getElementById('arCaptureCanvas');
            const cCtx = captureCanvas.getContext('2d');
            const w = arOverlayCanvas.width;
            const h = arOverlayCanvas.height;
            captureCanvas.width = w;
            captureCanvas.height = h;

            if (arStream && arVideoFeed.videoWidth) {
                cCtx.drawImage(arVideoFeed, 0, 0, w, h);
            } else {
                cCtx.fillStyle = '#091914';
                cCtx.fillRect(0, 0, w, h);
            }
            // Gabungkan lapisan garis & tanda + AR di atas foto
            cCtx.drawImage(arOverlayCanvas, 0, 0, w, h);

            arPhotoPreview.src = captureCanvas.toDataURL('image/jpeg', 0.95);
            arVideoFeed.classList.add('hidden');
            arOverlayCanvas.classList.add('hidden');
            arPhotoPreview.classList.remove('hidden');

            liveCameraControls.classList.add('hidden');
            capturedPhotoControls.classList.remove('hidden');
            showToast('Foto pengukuran berhasil ditangkap!', 'success');
        });

        document.getElementById('retakeArPhotoBtn').addEventListener('click', () => {
            arPhotoPreview.classList.add('hidden');
            if (arStream) arVideoFeed.classList.remove('hidden');
            arOverlayCanvas.classList.remove('hidden');
            capturedPhotoControls.classList.add('hidden');
            liveCameraControls.classList.remove('hidden');
        });

        document.getElementById('downloadArPhotoBtn').addEventListener('click', () => {
            const link = document.createElement('a');
            link.href = arPhotoPreview.src;
            link.download = `Ukur_Lahan_AR_${Date.now()}.jpg`;
            document.body.appendChild(link);
            link.click();
            document.body.removeChild(link);
            showToast('Foto pengukuran diunduh ke perangkat.', 'success');
        });

        // =========================================================================
        // 4. MODUL PETA INTERAKTIF (LEAFLET) & KOMPAS DIGITAL
        // =========================================================================
        // Default koordinat: Palembang, Sumatera Selatan
        const defaultCoords = [-2.9761, 104.7754];
        const map = L.map('leafletMap').setView(defaultCoords, 14);

        const osmLayer = L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
            maxZoom: 19,
            attribution: '&copy; OpenStreetMap'
        }).addTo(map);

        const satelliteLayer = L.tileLayer('https://server.arcgisonline.com/ArcGIS/rest/services/World_Imagery/MapServer/tile/{z}/{y}/{x}', {
            maxZoom: 19,
            attribution: 'Tiles &copy; Esri'
        });

        L.control.layers({ 'Peta Jalan (OSM)': osmLayer, 'Citra Satelit': satelliteLayer }).addTo(map);

        let userLocationMarker = null;
        let customMarkers = []; // { id, marker, lat, lng }

        function updateMarkersListUI() {
            const listEl = document.getElementById('mapMarkersList');
            document.getElementById('markerCountLabel').textContent = `${customMarkers.length} Titik`;

            if (customMarkers.length === 0) {
                listEl.innerHTML = `<span class="text-slate-400">Belum ada penanda titik lahan pada peta.</span>`;
                return;
            }

            listEl.innerHTML = customMarkers.map((item, idx) => `
                <div class="inline-flex items-center gap-2 bg-slate-900 border border-emerald-500/30 rounded-lg px-2.5 py-1 font-mono text-[11px] text-slate-200">
                    <button type="button" onclick="focusMapMarker(${idx})" class="hover:text-emerald-300 cursor-pointer">
                        <strong class="text-emerald-400">P${idx + 1}:</strong> ${item.lat.toFixed(5)}, ${item.lng.toFixed(5)}
                    </button>
                    <button type="button" onclick="removeMapMarker(${idx})" class="text-rose-400 hover:text-rose-300 cursor-pointer" title="Hapus Titik">
                        <i class="fas fa-xmark"></i>
                    </button>
                </div>
            `).join('');
        }

        window.focusMapMarker = function(idx) {
            const item = customMarkers[idx];
            if (item) {
                map.setView([item.lat, item.lng], 17);
                item.marker.openPopup();
            }
        };

        window.removeMapMarker = function(idx) {
            const item = customMarkers[idx];
            if (item) {
                map.removeLayer(item.marker);
                customMarkers.splice(idx, 1);
                updateMarkersListUI();
            }
        };

        // Klik pada peta untuk memberi tanda marker batas lahan
        map.on('click', (e) => {
            const { lat, lng } = e.latlng;
            const num = customMarkers.length + 1;
            const marker = L.marker([lat, lng]).addTo(map)
                .bindPopup(`<strong>Penanda Lahan #${num}</strong><br>Lat: ${lat.toFixed(6)}<br>Lng: ${lng.toFixed(6)}`)
                .openPopup();

            customMarkers.push({ id: Date.now(), marker, lat, lng });
            updateMarkersListUI();
        });

        // Tombol Lokasi Saya (Geolocation)
        document.getElementById('findMyLocationBtn').addEventListener('click', () => {
            if (!navigator.geolocation) {
                showToast('Browser Anda tidak mendukung fitur geolokasi.', 'error');
                return;
            }
            showToast('Mencari koordinat GPS Anda...', 'info');
            navigator.geolocation.getCurrentPosition(
                (pos) => {
                    const { latitude, longitude, accuracy } = pos.coords;
                    map.setView([latitude, longitude], 17);
                    if (userLocationMarker) map.removeLayer(userLocationMarker);
                    userLocationMarker = L.circleMarker([latitude, longitude], {
                        radius: 9,
                        fillColor: '#0ea5e9',
                        color: '#ffffff',
                        weight: 2.5,
                        opacity: 1,
                        fillOpacity: 0.9
                    }).addTo(map)
                      .bindPopup(`<strong>Lokasi Anda Saat Ini</strong><br>Lat: ${latitude.toFixed(6)}<br>Lng: ${longitude.toFixed(6)}<br>Akurasi: ±${Math.round(accuracy)} m`)
                      .openPopup();
                    showToast('Lokasi pengguna ditemukan!', 'success');
                },
                () => {
                    showToast('Gagal mengakses lokasi GPS. Pastikan izin lokasi aktif.', 'error');
                },
                { enableHighAccuracy: true, timeout: 10000 }
            );
        });

        document.getElementById('clearMapMarkersBtn').addEventListener('click', () => {
            customMarkers.forEach(m => map.removeLayer(m.marker));
            customMarkers = [];
            updateMarkersListUI();
            showToast('Semua penanda peta dihapus.', 'warning');
        });

        // --- Kompas Digital Logic ---
        const compassNeedleEl = document.getElementById('compassNeedleEl');
        const compassAzimuthText = document.getElementById('compassAzimuthText');
        const compassDirectionText = document.getElementById('compassDirectionText');
        const manualCompassSlider = document.getElementById('manualCompassSlider');
        const manualSliderVal = document.getElementById('manualSliderVal');

        function getCardinalDirectionId(deg) {
            const dirs = [
                'Utara (N)', 'Timur Laut (NE)', 'Timur (E)', 'Tenggara (SE)',
                'Selatan (S)', 'Barat Daya (SW)', 'Barat (W)', 'Barat Laut (NW)'
            ];
            const index = Math.round(((deg %= 360) < 0 ? deg + 360 : deg) / 45) % 8;
            return dirs[index];
        }

        function setCompassHeading(deg) {
            const normalized = ((Math.round(deg) % 360) + 360) % 360;
            compassNeedleEl.style.transform = `rotate(${normalized}deg)`;
            compassAzimuthText.textContent = `${String(normalized).padStart(3, '0')}°`;
            compassDirectionText.textContent = getCardinalDirectionId(normalized);
            manualCompassSlider.value = normalized;
            manualSliderVal.textContent = `${normalized}°`;
        }

        manualCompassSlider.addEventListener('input', (e) => {
            setCompassHeading(parseFloat(e.target.value));
        });

        function handleDeviceOrientation(event) {
            let heading = null;
            if (typeof event.webkitCompassHeading === 'number') {
                heading = event.webkitCompassHeading;
            } else if (event.alpha !== null) {
                heading = 360 - event.alpha;
            }
            if (heading !== null) {
                setCompassHeading(heading);
            }
        }

        document.getElementById('activateCompassSensorBtn').addEventListener('click', async () => {
            try {
                if (typeof DeviceOrientationEvent !== 'undefined' && typeof DeviceOrientationEvent.requestPermission === 'function') {
                    const perm = await DeviceOrientationEvent.requestPermission();
                    if (perm === 'granted') {
                        window.addEventListener('deviceorientation', handleDeviceOrientation);
                        showToast('Sensor kompas perangkat aktif!', 'success');
                    } else {
                        showToast('Izin sensor orientasi ditolak.', 'error');
                    }
                } else {
                    window.addEventListener('deviceorientationabsolute', handleDeviceOrientation, true);
                    window.addEventListener('deviceorientation', handleDeviceOrientation, true);
                    showToast('Sensor kompas diaktifkan (gunakan slider jika di desktop).', 'info');
                }
            } catch {
                showToast('Sensor orientasi tidak tersedia pada perangkat ini.', 'warning');
            }
        });

        // =========================================================================
        // 5. MODUL KALKULATOR ILMIAH, KONVERSI SATUAN & HITUNG SKALA
        // =========================================================================
        const sciCalcInput = document.getElementById('sciCalcInput');
        const sciCalcMainResult = document.getElementById('sciCalcMainResult');
        const sciCalcHistoryList = document.getElementById('sciCalcHistoryList');
        let currentExpr = '';
        let historyRecords = [];

        function calcFactorial(n) {
            if (n < 0 || !Number.isInteger(n) || n > 170) return NaN;
            if (n === 0 || n === 1) return 1;
            let res = 1;
            for (let i = 2; i <= n; i++) res *= i;
            return res;
        }

        function evaluateScientificExpression(rawExpr) {
            const isDeg = document.querySelector('input[name="calcAngleMode"]:checked').value === 'deg';
            let expr = rawExpr
                .replace(/×/g, '*')
                .replace(/÷/g, '/')
                .replace(/−/g, '-')
                .replace(/π/g, 'Math.PI')
                .replace(/\be\b/g, 'Math.E')
                .replace(/10\^\(/g, '10**(')
                .replace(/e\^\(/g, 'Math.exp(')
                .replace(/cbrt\(/g, 'Math.cbrt(')
                .replace(/√\(/g, 'Math.sqrt(')
                .replace(/ln\(/g, 'Math.log(')
                .replace(/log\(/g, 'Math.log10(');

            // Tangani akar pangkat y: contoh 3y√27 -> Math.pow(27, 1/3)
            expr = expr.replace(/(\d+(?:\.\d+)?)\s*y√\s*(\d+(?:\.\d+)?)/g, 'Math.pow($2, 1/($1))');

            // Tangani operator pangkat ^
            expr = expr.replace(/\^/g, '**');

            // Tangani persentase angka (misal 50% -> (50/100))
            expr = expr.replace(/(\d+(?:\.\d+)?)%/g, '($1/100)');

            // Tangani faktorial (misal 5! -> calcFactorial(5))
            expr = expr.replace(/(\d+)!/g, 'calcFactorial($1)');

            // Helper trigonometri Deg / Rad
            const sinFn = (x) => isDeg ? Math.sin((x * Math.PI) / 180) : Math.sin(x);
            const cosFn = (x) => isDeg ? Math.cos((x * Math.PI) / 180) : Math.cos(x);
            const tanFn = (x) => isDeg ? Math.tan((x * Math.PI) / 180) : Math.tan(x);
            const asinFn = (x) => isDeg ? (Math.asin(x) * 180) / Math.PI : Math.asin(x);
            const acosFn = (x) => isDeg ? (Math.acos(x) * 180) / Math.PI : Math.acos(x);
            const atanFn = (x) => isDeg ? (Math.atan(x) * 180) / Math.PI : Math.atan(x);

            // Ganti nama fungsi trig agar memanggil helper lokal
            expr = expr
                .replace(/asin\(/g, 'asinFn(')
                .replace(/acos\(/g, 'acosFn(')
                .replace(/atan\(/g, 'atanFn(')
                .replace(/(?<!a)sin\(/g, 'sinFn(')
                .replace(/(?<!a)cos\(/g, 'cosFn(')
                .replace(/(?<!a)tan\(/g, 'tanFn(');

            // Tutup kurung yang belum lengkap secara otomatis
            const openCount = (expr.match(/\(/g) || []).length;
            const closeCount = (expr.match(/\)/g) || []).length;
            if (openCount > closeCount) {
                expr += ')'.repeat(openCount - closeCount);
            }

            const fn = new Function(
                'sinFn', 'cosFn', 'tanFn', 'asinFn', 'acosFn', 'atanFn', 'calcFactorial',
                `"use strict"; return (${expr});`
            );
            const result = fn(sinFn, cosFn, tanFn, asinFn, acosFn, atanFn, calcFactorial);
            if (typeof result !== 'number' || Number.isNaN(result) || !Number.isFinite(result)) {
                throw new Error('Math Error');
            }
            return Number.isInteger(result) ? result : parseFloat(result.toPrecision(10));
        }

        function renderSciHistory() {
            if (historyRecords.length === 0) {
                sciCalcHistoryList.innerHTML = `<p class="text-slate-400 font-sans text-xs">Belum ada riwayat perhitungan.</p>`;
                return;
            }
            sciCalcHistoryList.innerHTML = historyRecords.map(item => `
                <div class="border-b border-slate-100 pb-1.5">
                    <div class="text-xs text-slate-500">${item.expr} =</div>
                    <div class="text-sm font-bold text-slate-800">${item.result}</div>
                </div>
            `).join('');
        }

        document.querySelectorAll('.sci-btn').forEach(btn => {
            btn.addEventListener('click', () => {
                const val = btn.getAttribute('data-val');
                const action = btn.getAttribute('data-action');

                if (action === 'clear') {
                    currentExpr = '';
                    sciCalcInput.value = '';
                    sciCalcMainResult.textContent = '0';
                } else if (action === 'backspace') {
                    currentExpr = currentExpr.slice(0, -1);
                    sciCalcInput.value = currentExpr;
                } else if (action === 'equals') {
                    if (!currentExpr.trim()) return;
                    try {
                        const res = evaluateScientificExpression(currentExpr);
                        sciCalcMainResult.textContent = res;
                        historyRecords.unshift({ expr: currentExpr, result: res });
                        renderSciHistory();
                        currentExpr = String(res);
                        sciCalcInput.value = currentExpr;
                    } catch {
                        sciCalcMainResult.textContent = 'Error';
                        sciCalcInput.value = 'Error';
                        currentExpr = '';
                    }
                } else if (val) {
                    if (sciCalcInput.value === 'Error') currentExpr = '';
                    currentExpr += val;
                    sciCalcInput.value = currentExpr;
                }
            });
        });

        document.getElementById('clearSciHistoryBtn').addEventListener('click', () => {
            historyRecords = [];
            renderSciHistory();
            sciCalcMainResult.textContent = '0';
        });

        // --- Konversi Satuan Panjang (2 Kolom Dropdown) ---
        const convInputValue = document.getElementById('convInputValue');
        const convFromUnit = document.getElementById('convFromUnit');
        const convToUnit = document.getElementById('convToUnit');
        const convResultDisplay = document.getElementById('convResultDisplay');
        const convFormulaNote = document.getElementById('convFormulaNote');

        const UNIT_IN_METERS = {
            mm: 0.001,
            cm: 0.01,
            dm: 0.1,
            m: 1,
            dam: 10,
            hm: 100,
            km: 1000
        };

        function updateLengthConversion() {
            const val = parseFloat(convInputValue.value);
            const fromU = convFromUnit.value;
            const toU = convToUnit.value;

            if (Number.isNaN(val)) {
                convResultDisplay.textContent = `0 ${toU}`;
                return;
            }

            const valueInMeters = val * UNIT_IN_METERS[fromU];
            const converted = valueInMeters / UNIT_IN_METERS[toU];
            const factor = UNIT_IN_METERS[fromU] / UNIT_IN_METERS[toU];

            const formattedRes = converted.toLocaleString('id-ID', { maximumFractionDigits: 8 });
            const formattedFactor = factor.toLocaleString('id-ID', { maximumFractionDigits: 8 });

            convResultDisplay.textContent = `${formattedRes} ${toU}`;
            convFormulaNote.textContent = `1 ${fromU} = ${formattedFactor} ${toU}`;
        }

        convInputValue.addEventListener('input', updateLengthConversion);
        convFromUnit.addEventListener('change', updateLengthConversion);
        convToUnit.addEventListener('change', updateLengthConversion);

        // --- Kalkulator Skala Peta ---
        const scaleCalcMode = document.getElementById('scaleCalcMode');
        const scaleLabelA = document.getElementById('scaleLabelA');
        const scaleLabelB = document.getElementById('scaleLabelB');
        const scaleInputA = document.getElementById('scaleInputA');
        const scaleInputB = document.getElementById('scaleInputB');
        const scaleResultDisplay = document.getElementById('scaleResultDisplay');
        const scaleSubNote = document.getElementById('scaleSubNote');

        function configureScaleInputs() {
            const mode = scaleCalcMode.value;
            if (mode === 'findScale') {
                scaleLabelA.textContent = 'Jarak pada Peta (cm)';
                scaleLabelB.textContent = 'Jarak Sebenarnya (m)';
                scaleInputA.value = '5';
                scaleInputB.value = '250';
            } else if (mode === 'findReal') {
                scaleLabelA.textContent = 'Jarak pada Peta (cm)';
                scaleLabelB.textContent = 'Penyebut Skala (N pada 1:N)';
                scaleInputA.value = '4';
                scaleInputB.value = '10000';
            } else {
                scaleLabelA.textContent = 'Jarak Sebenarnya (m)';
                scaleLabelB.textContent = 'Penyebut Skala (N pada 1:N)';
                scaleInputA.value = '500';
                scaleInputB.value = '2500';
            }
            calculateMapScale();
        }

        function calculateMapScale() {
            const mode = scaleCalcMode.value;
            const a = parseFloat(scaleInputA.value);
            const b = parseFloat(scaleInputB.value);

            if (!a || !b || a <= 0 || b <= 0) {
                scaleResultDisplay.textContent = '-';
                scaleSubNote.textContent = 'Masukkan nilai > 0';
                return;
            }

            if (mode === 'findScale') {
                // a = cm peta, b = meter sebenarnya -> ubah b ke cm (b * 100)
                const denom = (b * 100) / a;
                scaleResultDisplay.textContent = `1 : ${Math.round(denom).toLocaleString('id-ID')}`;
                scaleSubNote.textContent = `1 cm peta = ${(denom / 100).toLocaleString('id-ID', { maximumFractionDigits: 2 })} m`;
            } else if (mode === 'findReal') {
                // a = cm peta, b = penyebut skala N
                const realCm = a * b;
                const realM = realCm / 100;
                const realKm = realM / 1000;
                scaleResultDisplay.textContent = realM >= 1000
                    ? `${realKm.toLocaleString('id-ID', { maximumFractionDigits: 3 })} km`
                    : `${realM.toLocaleString('id-ID', { maximumFractionDigits: 2 })} m`;
                scaleSubNote.textContent = `${realM.toLocaleString('id-ID')} meter`;
            } else {
                // a = meter sebenarnya, b = penyebut skala N
                const mapCm = (a * 100) / b;
                scaleResultDisplay.textContent = `${mapCm.toLocaleString('id-ID', { maximumFractionDigits: 3 })} cm`;
                scaleSubNote.textContent = `Skala 1 : ${b.toLocaleString('id-ID')}`;
            }
        }

        scaleCalcMode.addEventListener('change', configureScaleInputs);
        scaleInputA.addEventListener('input', calculateMapScale);
        scaleInputB.addEventListener('input', calculateMapScale);

        // =========================================================================
        // INISIALISASI SAAT HALAMAN DIMUAT
        // =========================================================================
        window.addEventListener('resize', () => {
            resizeBlueprintCanvas();
            if (!arOverlayCanvas.classList.contains('hidden')) {
                syncArCanvasSize();
            }
            map.invalidateSize();
        });

        window.addEventListener('DOMContentLoaded', () => {
            runLandAnalysis(false);
            resizeBlueprintCanvas();
            updateBlueprintSummaryTables();
            updateLengthConversion();
            calculateMapScale();
        });
    </script>
</body>
</html>
