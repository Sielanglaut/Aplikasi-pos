<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>inventoryQu - Sistem Manajemen Stok & Kasir</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Lucide Icons -->
    <script src="https://unpkg.com/lucide@latest"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap');
        body { font-family: 'Inter', sans-serif; }
        
        /* Custom Print Styling */
        @media print {
            body * { visibility: hidden; }
            #print-area, #print-area * { visibility: visible; }
            #print-area { position: absolute; left: 0; top: 0; width: 100%; }
            .no-print { display: none !important; }
        }
        
        /* Custom Scrollbar */
        ::-webkit-scrollbar { width: 6px; height: 6px; }
        ::-webkit-scrollbar-track { background: #f1f5f9; }
        ::-webkit-scrollbar-thumb { background: #cbd5e1; border-radius: 4px; }
        ::-webkit-scrollbar-thumb:hover { background: #94a3b8; }
    </style>
</head>
<body class="bg-slate-50 text-slate-800 antialiased min-h-screen flex flex-col md:flex-row">

    <!-- SIDEBAR MOBILE TOP NAV -->
    <div class="md:hidden bg-slate-900 text-white p-4 flex justify-between items-center sticky top-0 z-40 shadow-md no-print">
        <div class="flex items-center space-x-2">
            <div class="bg-indigo-600 p-2 rounded-lg text-white">
                <i data-lucide="box" class="w-5 h-5"></i>
            </div>
            <span class="font-bold text-lg tracking-wide app-title">inventoryQu</span>
        </div>
        <button id="mobile-menu-btn" class="p-2 text-slate-300 hover:text-white focus:outline-none">
            <i data-lucide="menu" class="w-6 h-6"></i>
        </button>
    </div>

    <!-- SIDEBAR NAVIGATION -->
    <aside id="sidebar" class="fixed md:static inset-y-0 left-0 transform -translate-x-full md:translate-x-0 transition-transform duration-200 ease-in-out z-50 w-64 bg-slate-900 text-slate-300 flex flex-col justify-between h-screen no-print shadow-xl">
        <div>
            <!-- Logo Header -->
            <div class="p-5 border-b border-slate-800 flex items-center space-x-3">
                <div class="bg-indigo-600 p-2.5 rounded-xl text-white shadow-lg shadow-indigo-500/30">
                    <i data-lucide="box" class="w-6 h-6"></i>
                </div>
                <div>
                    <h1 class="font-bold text-lg text-white tracking-wide app-title">inventoryQu</h1>
                    <p class="text-xs text-slate-400">Inventory & POS System</p>
                </div>
            </div>

            <!-- Navigation Links -->
            <nav class="p-4 space-y-1 text-sm font-medium">
                <button onclick="switchTab('dashboard')" class="nav-btn w-full flex items-center space-x-3 px-3.5 py-2.5 rounded-xl transition text-slate-400 hover:bg-slate-800 hover:text-white" data-tab="dashboard">
                    <i data-lucide="layout-dashboard" class="w-5 h-5"></i>
                    <span>Dashboard</span>
                </button>
                <button onclick="switchTab('pos')" class="nav-btn w-full flex items-center space-x-3 px-3.5 py-2.5 rounded-xl transition text-slate-400 hover:bg-slate-800 hover:text-white" data-tab="pos">
                    <i data-lucide="shopping-cart" class="w-5 h-5"></i>
                    <span>Kasir / POS</span>
                </button>
                <button onclick="switchTab('inventory')" class="nav-btn w-full flex items-center space-x-3 px-3.5 py-2.5 rounded-xl transition text-slate-400 hover:bg-slate-800 hover:text-white" data-tab="inventory">
                    <i data-lucide="package" class="w-5 h-5"></i>
                    <span>Data Barang</span>
                </button>
                <button onclick="switchTab('closing')" class="nav-btn w-full flex items-center space-x-3 px-3.5 py-2.5 rounded-xl transition text-slate-400 hover:bg-slate-800 hover:text-white" data-tab="closing">
                    <i data-lucide="archive" class="w-5 h-5"></i>
                    <span>Closing Stock</span>
                </button>
                <button onclick="switchTab('reports')" class="nav-btn w-full flex items-center space-x-3 px-3.5 py-2.5 rounded-xl transition text-slate-400 hover:bg-slate-800 hover:text-white" data-tab="reports">
                    <i data-lucide="file-text" class="w-5 h-5"></i>
                    <span>Laporan Penjualan</span>
                </button>
                <button onclick="switchTab('settings')" class="nav-btn w-full flex items-center space-x-3 px-3.5 py-2.5 rounded-xl transition text-slate-400 hover:bg-slate-800 hover:text-white" data-tab="settings">
                    <i data-lucide="settings" class="w-5 h-5"></i>
                    <span>Pengaturan</span>
                </button>
            </nav>
        </div>

        <!-- User Info & Logout Footer -->
        <div class="p-4 border-t border-slate-800">
            <div class="bg-slate-800/60 p-3 rounded-xl flex items-center justify-between">
                <div class="flex items-center space-x-3 overflow-hidden">
                    <div id="user-avatar" class="w-9 h-9 rounded-lg bg-indigo-500 flex items-center justify-center text-white font-bold text-sm flex-shrink-0">
                        A
                    </div>
                    <div class="truncate">
                        <p id="user-name-display" class="text-sm font-semibold text-white truncate">Admin Default</p>
                        <p id="user-role-display" class="text-xs text-indigo-400 capitalize">Administrator</p>
                    </div>
                </div>
                <button onclick="logout()" class="text-slate-400 hover:text-rose-400 transition p-1.5" title="Keluar">
                    <i data-lucide="log-out" class="w-5 h-5"></i>
                </button>
            </div>
        </div>
    </aside>

    <!-- MAIN CONTENT AREA -->
    <main class="flex-1 flex flex-col min-w-0 overflow-y-auto h-screen">
        
        <!-- TOPBAR HEADER -->
        <header class="bg-white border-b border-slate-200 px-6 py-4 flex items-center justify-between no-print sticky top-0 z-30">
            <div>
                <h2 id="page-title" class="text-xl font-bold text-slate-800">Dashboard</h2>
                <p id="store-name-top" class="text-xs text-slate-500 font-medium">Toko Kelontong Berkah</p>
            </div>
            <div class="flex items-center space-x-4">
                <div class="hidden sm:flex items-center bg-slate-100 text-slate-600 px-3 py-1.5 rounded-lg text-xs font-medium space-x-2">
                    <i data-lucide="calendar" class="w-4 h-4 text-slate-400"></i>
                    <span id="current-date-display">-</span>
                </div>
            </div>
        </header>

        <!-- CONTENT TAB PANELS -->
        <div class="p-4 md:p-6 flex-1 space-y-6">

            <!-- TAB 1: DASHBOARD -->
            <section id="tab-dashboard" class="tab-content space-y-6">
                <!-- Stat Cards -->
                <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4">
                    <div class="bg-white p-5 rounded-2xl border border-slate-200/80 shadow-sm flex items-center space-x-4">
                        <div class="p-3 bg-emerald-50 text-emerald-600 rounded-xl">
                            <i data-lucide="dollar-sign" class="w-6 h-6"></i>
                        </div>
                        <div>
                            <p class="text-xs font-medium text-slate-500 uppercase tracking-wider">Pendapatan Hari Ini</p>
                            <h3 id="dash-today-income" class="text-xl font-bold text-slate-800 mt-0.5">Rp 0</h3>
                        </div>
                    </div>
                    <div class="bg-white p-5 rounded-2xl border border-slate-200/80 shadow-sm flex items-center space-x-4">
                        <div class="p-3 bg-indigo-50 text-indigo-600 rounded-xl">
                            <i data-lucide="shopping-bag" class="w-6 h-6"></i>
                        </div>
                        <div>
                            <p class="text-xs font-medium text-slate-500 uppercase tracking-wider">Transaksi Hari Ini</p>
                            <h3 id="dash-today-count" class="text-xl font-bold text-slate-800 mt-0.5">0</h3>
                        </div>
                    </div>
                    <div class="bg-white p-5 rounded-2xl border border-slate-200/80 shadow-sm flex items-center space-x-4">
                        <div class="p-3 bg-blue-50 text-blue-600 rounded-xl">
                            <i data-lucide="boxes" class="w-6 h-6"></i>
                        </div>
                        <div>
                            <p class="text-xs font-medium text-slate-500 uppercase tracking-wider">Total Jenis Barang</p>
                            <h3 id="dash-total-items" class="text-xl font-bold text-slate-800 mt-0.5">0</h3>
                        </div>
                    </div>
                    <div class="bg-white p-5 rounded-2xl border border-slate-200/80 shadow-sm flex items-center space-x-4">
                        <div class="p-3 bg-amber-50 text-amber-600 rounded-xl">
                            <i data-lucide="alert-triangle" class="w-6 h-6"></i>
                        </div>
                        <div>
                            <p class="text-xs font-medium text-slate-500 uppercase tracking-wider">Stok Menipis (≤5)</p>
                            <h3 id="dash-low-stock" class="text-xl font-bold text-amber-600 mt-0.5">0</h3>
                        </div>
                    </div>
                </div>

                <!-- Sales Chart & Low Stock Alert List -->
                <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
                    <div class="lg:col-span-2 bg-white p-5 rounded-2xl border border-slate-200/80 shadow-sm">
                        <h3 class="text-base font-bold text-slate-800 mb-4 flex items-center justify-between">
                            <span>Grafik Penjualan 7 Hari Terakhir</span>
                            <span class="text-xs font-normal text-slate-500">Nominal Penjualan</span>
                        </h3>
                        <div class="h-64">
                            <canvas id="salesChart"></canvas>
                        </div>
                    </div>
                    <div class="bg-white p-5 rounded-2xl border border-slate-200/80 shadow-sm flex flex-col">
                        <h3 class="text-base font-bold text-slate-800 mb-3 flex items-center space-x-2">
                            <i data-lucide="alert-circle" class="w-5 h-5 text-amber-500"></i>
                            <span>Peringatan Stok Menipis</span>
                        </h3>
                        <div class="flex-1 overflow-y-auto max-h-60 space-y-2 pr-1" id="dash-low-stock-list">
                            <!-- Populated via JS -->
                        </div>
                    </div>
                </div>
            </section>

            <!-- TAB 2: KASIR / POS -->
            <section id="tab-pos" class="tab-content hidden space-y-6">
                <div class="grid grid-cols-1 lg:grid-cols-12 gap-6 items-start">
                    
                    <!-- Left Column: Product Selection -->
                    <div class="lg:col-span-7 space-y-4">
                        <div class="bg-white p-4 rounded-2xl border border-slate-200/80 shadow-sm flex flex-col sm:flex-row gap-3">
                            <div class="relative flex-1">
                                <i data-lucide="search" class="w-5 h-5 absolute left-3.5 top-3 text-slate-400"></i>
                                <input type="text" id="pos-search" oninput="renderPosProducts()" placeholder="Cari nama barang atau barcode..." class="w-full pl-10 pr-4 py-2.5 rounded-xl border border-slate-200 focus:outline-none focus:ring-2 focus:ring-indigo-500 text-sm">
                            </div>
                            <select id="pos-category-filter" onchange="renderPosProducts()" class="px-4 py-2.5 rounded-xl border border-slate-200 bg-white text-sm focus:outline-none focus:ring-2 focus:ring-indigo-500">
                                <option value="">Semua Kategori</option>
                            </select>
                        </div>

                        <!-- Product Grid -->
                        <div id="pos-product-grid" class="grid grid-cols-2 sm:grid-cols-3 gap-3 max-h-[560px] overflow-y-auto p-1">
                            <!-- Dynamic Cards -->
                        </div>
                    </div>

                    <!-- Right Column: Cart & Checkout -->
                    <div class="lg:col-span-5 bg-white rounded-2xl border border-slate-200/80 shadow-sm flex flex-col h-[650px]">
                        <div class="p-4 border-b border-slate-100 flex justify-between items-center bg-slate-50/50 rounded-t-2xl">
                            <div class="flex items-center space-x-2">
                                <i data-lucide="shopping-cart" class="w-5 h-5 text-indigo-600"></i>
                                <h3 class="font-bold text-slate-800">Keranjang Belanja</h3>
                            </div>
                            <button onclick="clearCart()" class="text-xs text-rose-500 hover:text-rose-700 font-medium">Kosongkan</button>
                        </div>

                        <!-- Cart Items List -->
                        <div id="pos-cart-items" class="flex-1 overflow-y-auto p-4 space-y-3">
                            <!-- Dynamic Cart Items -->
                        </div>

                        <!-- Cart Summary Footer -->
                        <div class="p-4 border-t border-slate-100 bg-slate-50/50 rounded-b-2xl space-y-3">
                            <div class="space-y-1.5 text-sm">
                                <div class="flex justify-between text-slate-600">
                                    <span>Subtotal</span>
                                    <span id="pos-subtotal" class="font-semibold">Rp 0</span>
                                </div>
                                <div class="flex justify-between text-slate-800 font-bold text-lg pt-1 border-t border-slate-200">
                                    <span>Total</span>
                                    <span id="pos-total" class="text-indigo-600">Rp 0</span>
                                </div>
                            </div>

                            <!-- Customer & Payment inputs -->
                            <div class="grid grid-cols-2 gap-2 pt-2">
                                <div>
                                    <label class="block text-xs font-medium text-slate-500 mb-1">Nama Pelanggan</label>
                                    <input type="text" id="pos-customer-name" placeholder="Umum" class="w-full px-3 py-2 border rounded-xl text-xs focus:ring-2 focus:ring-indigo-500 outline-none">
                                </div>
                                <div>
                                    <label class="block text-xs font-medium text-slate-500 mb-1">Nominal Bayar (Rp)</label>
                                    <input type="number" id="pos-pay-amount" oninput="calculateKembalian()" placeholder="0" class="w-full px-3 py-2 border rounded-xl text-xs font-semibold focus:ring-2 focus:ring-indigo-500 outline-none">
                                </div>
                            </div>
                            
                            <div class="flex justify-between text-xs font-semibold text-slate-600 px-1">
                                <span>Kembalian:</span>
                                <span id="pos-change-amount" class="text-emerald-600">Rp 0</span>
                            </div>

                            <button onclick="openReceiptPreview()" class="w-full bg-indigo-600 hover:bg-indigo-700 active:bg-indigo-800 text-white font-bold py-3 rounded-xl transition shadow-lg shadow-indigo-500/25 flex items-center justify-center space-x-2 text-sm">
                                <i data-lucide="printer" class="w-4 h-4"></i>
                                <span>Preview & Bayar</span>
                            </button>
                        </div>
                    </div>
                </div>
            </section>

            <!-- TAB 3: DATA BARANG -->
            <section id="tab-inventory" class="tab-content hidden space-y-6">
                <!-- Action Bar -->
                <div class="bg-white p-4 rounded-2xl border border-slate-200/80 shadow-sm flex flex-col md:flex-row justify-between items-center gap-4">
                    <div class="flex flex-1 gap-3 w-full md:w-auto">
                        <div class="relative flex-1">
                            <i data-lucide="search" class="w-5 h-5 absolute left-3.5 top-2.5 text-slate-400"></i>
                            <input type="text" id="inv-search" oninput="renderInventoryTable()" placeholder="Cari nama / kode barang..." class="w-full pl-10 pr-4 py-2 rounded-xl border border-slate-200 focus:outline-none focus:ring-2 focus:ring-indigo-500 text-sm">
                        </div>
                        <select id="inv-category-filter" onchange="renderInventoryTable()" class="px-4 py-2 rounded-xl border border-slate-200 bg-white text-sm focus:outline-none focus:ring-2 focus:ring-indigo-500">
                            <option value="">Semua Kategori</option>
                        </select>
                    </div>
                    <button onclick="openProductModal()" class="w-full md:w-auto bg-indigo-600 hover:bg-indigo-700 text-white font-semibold px-4 py-2 rounded-xl text-sm transition flex items-center justify-center space-x-2">
                        <i data-lucide="plus" class="w-4 h-4"></i>
                        <span>Tambah Barang</span>
                    </button>
                </div>

                <!-- Table -->
                <div class="bg-white rounded-2xl border border-slate-200/80 shadow-sm overflow-hidden">
                    <div class="overflow-x-auto">
                        <table class="w-full text-left text-sm text-slate-600">
                            <thead class="bg-slate-50 text-slate-700 font-semibold border-b border-slate-200 uppercase text-xs">
                                <tr>
                                    <th class="p-4">Kode</th>
                                    <th class="p-4">Nama Barang</th>
                                    <th class="p-4">Kategori</th>
                                    <th class="p-4 text-right">Harga Beli</th>
                                    <th class="p-4 text-right">Harga Jual</th>
                                    <th class="p-4 text-center">Stok</th>
                                    <th class="p-4 text-center">Aksi</th>
                                </tr>
                            </thead>
                            <tbody id="inv-table-body" class="divide-y divide-slate-100">
                                <!-- Dynamic content -->
                            </tbody>
                        </table>
                    </div>
                </div>
            </section>

            <!-- TAB 4: CLOSING STOCK AKHIR BULAN -->
            <section id="tab-closing" class="tab-content hidden space-y-6">
                <!-- Action Header -->
                <div class="bg-white p-5 rounded-2xl border border-slate-200/80 shadow-sm flex flex-col md:flex-row justify-between items-start md:items-center gap-4">
                    <div>
                        <h3 class="text-base font-bold text-slate-800">Closing Stock & Stock Opname Akhir Bulan</h3>
                        <p class="text-xs text-slate-500 mt-1">Lakukan penyesuaian stok fisik dan stok sistem untuk rekapitulasi penutupan buku bulanan.</p>
                    </div>
                    <div class="flex gap-2">
                        <button onclick="renderClosingForm()" class="bg-indigo-600 hover:bg-indigo-700 text-white font-semibold px-4 py-2 rounded-xl text-sm transition flex items-center space-x-2">
                            <i data-lucide="file-plus" class="w-4 h-4"></i>
                            <span>Buat Closing Bulan Ini</span>
                        </button>
                    </div>
                </div>

                <!-- Input Closing Area -->
                <div id="closing-input-container" class="bg-white p-5 rounded-2xl border border-slate-200/80 shadow-sm space-y-4">
                    <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center gap-3 border-b border-slate-100 pb-3">
                        <div>
                            <h4 class="font-bold text-slate-800 text-sm">Form Penyesuaian Stok Fisik Periode: <span id="closing-period-label" class="text-indigo-600">Oktober 2026</span></h4>
                            <p class="text-xs text-slate-500">Masukkan jumlah stok fisik hasil hitungan manual toko.</p>
                        </div>
                        <button onclick="saveClosingStock()" class="bg-emerald-600 hover:bg-emerald-700 text-white font-semibold px-4 py-2 rounded-xl text-sm transition flex items-center space-x-2">
                            <i data-lucide="check-circle" class="w-4 h-4"></i>
                            <span>Simpan & Finalisasi Closing</span>
                        </button>
                    </div>

                    <div class="overflow-x-auto">
                        <table class="w-full text-left text-sm text-slate-600">
                            <thead class="bg-slate-50 text-slate-700 font-semibold border-b border-slate-200 text-xs uppercase">
                                <tr>
                                    <th class="p-3">Barang</th>
                                    <th class="p-3 text-center">Stok Awal</th>
                                    <th class="p-3 text-center">Masuk</th>
                                    <th class="p-3 text-center">Terjual</th>
                                    <th class="p-3 text-center">Stok Sistem</th>
                                    <th class="p-3 text-center w-32">Stok Fisik (Aktual)</th>
                                    <th class="p-3 text-center">Selisih</th>
                                </tr>
                            </thead>
                            <tbody id="closing-table-body" class="divide-y divide-slate-100">
                                <!-- Populated dynamically -->
                            </tbody>
                        </table>
                    </div>
                </div>

                <!-- Closing History List -->
                <div class="bg-white p-5 rounded-2xl border border-slate-200/80 shadow-sm space-y-4">
                    <h3 class="font-bold text-slate-800 text-base">Riwayat Closing Stock Bulanan</h3>
                    <div class="overflow-x-auto">
                        <table class="w-full text-left text-sm text-slate-600">
                            <thead class="bg-slate-50 text-slate-700 font-semibold border-b border-slate-200 text-xs uppercase">
                                <tr>
                                    <th class="p-3">Tanggal Closing</th>
                                    <th class="p-3">Periode Bulan</th>
                                    <th class="p-3">Petugas / Admin</th>
                                    <th class="p-3 text-center">Total Item</th>
                                    <th class="p-3 text-center">Total Selisih</th>
                                    <th class="p-3 text-center">Aksi</th>
                                </tr>
                            </thead>
                            <tbody id="closing-history-body" class="divide-y divide-slate-100">
                                <!-- Populated dynamically -->
                            </tbody>
                        </table>
                    </div>
                </div>
            </section>

            <!-- TAB 5: LAPORAN PENJUALAN -->
            <section id="tab-reports" class="tab-content hidden space-y-6">
                <!-- Filter bar -->
                <div class="bg-white p-4 rounded-2xl border border-slate-200/80 shadow-sm flex flex-wrap justify-between items-center gap-4">
                    <div class="flex flex-wrap items-center gap-3">
                        <div>
                            <label class="block text-xs font-medium text-slate-500 mb-1">Mulai Tanggal</label>
                            <input type="date" id="rep-start-date" class="px-3 py-1.5 rounded-xl border border-slate-200 text-sm focus:ring-2 focus:ring-indigo-500 outline-none">
                        </div>
                        <div>
                            <label class="block text-xs font-medium text-slate-500 mb-1">Sampai Tanggal</label>
                            <input type="date" id="rep-end-date" class="px-3 py-1.5 rounded-xl border border-slate-200 text-sm focus:ring-2 focus:ring-indigo-500 outline-none">
                        </div>
                        <button onclick="renderReports()" class="mt-5 bg-indigo-600 hover:bg-indigo-700 text-white font-medium px-4 py-2 rounded-xl text-sm transition">
                            Filter
                        </button>
                    </div>
                    <button onclick="printReport()" class="mt-2 sm:mt-0 bg-slate-800 hover:bg-slate-900 text-white font-semibold px-4 py-2 rounded-xl text-sm transition flex items-center space-x-2">
                        <i data-lucide="printer" class="w-4 h-4"></i>
                        <span>Cetak Laporan</span>
                    </button>
                </div>

                <!-- Sales Summary Cards -->
                <div class="grid grid-cols-1 sm:grid-cols-3 gap-4">
                    <div class="bg-white p-4 rounded-2xl border border-slate-200/80 shadow-sm">
                        <p class="text-xs text-slate-500 font-medium">Total Omset</p>
                        <h4 id="rep-total-revenue" class="text-xl font-bold text-slate-800 mt-1">Rp 0</h4>
                    </div>
                    <div class="bg-white p-4 rounded-2xl border border-slate-200/80 shadow-sm">
                        <p class="text-xs text-slate-500 font-medium">Total Keuntungan (Profit)</p>
                        <h4 id="rep-total-profit" class="text-xl font-bold text-emerald-600 mt-1">Rp 0</h4>
                    </div>
                    <div class="bg-white p-4 rounded-2xl border border-slate-200/80 shadow-sm">
                        <p class="text-xs text-slate-500 font-medium">Jumlah Transaksi</p>
                        <h4 id="rep-total-tx" class="text-xl font-bold text-indigo-600 mt-1">0</h4>
                    </div>
                </div>

                <!-- Transaction History Table -->
                <div class="bg-white rounded-2xl border border-slate-200/80 shadow-sm overflow-hidden">
                    <div class="p-4 border-b border-slate-100">
                        <h3 class="font-bold text-slate-800 text-base">Riwayat Transaksi</h3>
                    </div>
                    <div class="overflow-x-auto">
                        <table class="w-full text-left text-sm text-slate-600">
                            <thead class="bg-slate-50 text-slate-700 font-semibold border-b border-slate-200 text-xs uppercase">
                                <tr>
                                    <th class="p-4">No. Nota</th>
                                    <th class="p-4">Tanggal</th>
                                    <th class="p-4">Pelanggan</th>
                                    <th class="p-4 text-right">Total Nominal</th>
                                    <th class="p-4 text-right">Profit</th>
                                    <th class="p-4 text-center">Aksi</th>
                                </tr>
                            </thead>
                            <tbody id="rep-table-body" class="divide-y divide-slate-100">
                                <!-- Dynamic rows -->
                            </tbody>
                        </table>
                    </div>
                </div>
            </section>

            <!-- TAB 6: PENGATURAN & MANAGEMENT AKUN -->
            <section id="tab-settings" class="tab-content hidden space-y-6">
                <!-- Sub Tabs Navigation -->
                <div class="flex border-b border-slate-200 space-x-6">
                    <button onclick="switchSettingSubTab('store')" id="set-tab-store" class="setting-subtab-btn pb-3 text-sm font-bold border-b-2 border-indigo-600 text-indigo-600">
                        Pengaturan Usaha & Header Nota
                    </button>
                    <button onclick="switchSettingSubTab('accounts')" id="set-tab-accounts" class="setting-subtab-btn pb-3 text-sm font-medium text-slate-500 border-b-2 border-transparent hover:text-slate-700">
                        Manajemen Akun & Keamanan
                    </button>
                    <button onclick="switchSettingSubTab('reset')" id="set-tab-reset" class="setting-subtab-btn pb-3 text-sm font-medium text-rose-500 border-b-2 border-transparent hover:text-rose-700">
                        Reset All Data
                    </button>
                </div>

                <!-- SUB-TAB 1: STORE CONFIG -->
                <div id="subtab-store" class="setting-subpanel space-y-6">
                    <div class="bg-white p-6 rounded-2xl border border-slate-200/80 shadow-sm max-w-3xl space-y-5">
                        <h3 class="font-bold text-slate-800 text-base border-b border-slate-100 pb-3">Profil Toko / Usaha</h3>
                        
                        <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                            <div>
                                <label class="block text-xs font-semibold text-slate-600 mb-1">Nama Toko / Usaha</label>
                                <input type="text" id="cfg-store-name" class="w-full px-3 py-2 border border-slate-200 rounded-xl text-sm focus:ring-2 focus:ring-indigo-500 outline-none">
                            </div>
                            <div>
                                <label class="block text-xs font-semibold text-slate-600 mb-1">Nomor Telepon / WA</label>
                                <input type="text" id="cfg-store-phone" class="w-full px-3 py-2 border border-slate-200 rounded-xl text-sm focus:ring-2 focus:ring-indigo-500 outline-none">
                            </div>
                            <div class="md:col-span-2">
                                <label class="block text-xs font-semibold text-slate-600 mb-1">Alamat Lengkap</label>
                                <textarea id="cfg-store-address" rows="2" class="w-full px-3 py-2 border border-slate-200 rounded-xl text-sm focus:ring-2 focus:ring-indigo-500 outline-none"></textarea>
                            </div>
                            <div class="md:col-span-2">
                                <label class="block text-xs font-semibold text-slate-600 mb-1">Catatan Kaki Struk Nota (Footer)</label>
                                <input type="text" id="cfg-store-footer" placeholder="Terima kasih telah berbelanja!" class="w-full px-3 py-2 border border-slate-200 rounded-xl text-sm focus:ring-2 focus:ring-indigo-500 outline-none">
                            </div>
                        </div>

                        <div class="pt-3 border-t border-slate-100 flex justify-end">
                            <button onclick="saveStoreSettings()" class="bg-indigo-600 hover:bg-indigo-700 text-white font-semibold px-5 py-2.5 rounded-xl text-sm transition flex items-center space-x-2">
                                <i data-lucide="save" class="w-4 h-4"></i>
                                <span>Simpan Pengaturan Usaha</span>
                            </button>
                        </div>
                    </div>
                </div>

                <!-- SUB-TAB 2: MANAGEMENT AKUN & GANTI PASSWORD -->
                <div id="subtab-accounts" class="setting-subpanel hidden space-y-6">
                    <div class="grid grid-cols-1 lg:grid-cols-12 gap-6">
                        
                        <!-- Account List -->
                        <div class="lg:col-span-7 bg-white p-6 rounded-2xl border border-slate-200/80 shadow-sm space-y-4">
                            <div class="flex justify-between items-center border-b border-slate-100 pb-3">
                                <h3 class="font-bold text-slate-800 text-base">Daftar Akun / Pengguna</h3>
                                <button onclick="openAddAccountModal()" class="bg-indigo-600 hover:bg-indigo-700 text-white text-xs font-semibold px-3 py-2 rounded-xl transition flex items-center space-x-1">
                                    <i data-lucide="user-plus" class="w-4 h-4"></i>
                                    <span>Tambah Akun</span>
                                </button>
                            </div>

                            <div class="overflow-x-auto">
                                <table class="w-full text-left text-sm text-slate-600">
                                    <thead class="bg-slate-50 text-slate-700 font-semibold border-b border-slate-200 text-xs uppercase">
                                        <tr>
                                            <th class="p-3">Username</th>
                                            <th class="p-3">Nama Lengkap</th>
                                            <th class="p-3">Role</th>
                                            <th class="p-3 text-center">Aksi</th>
                                        </tr>
                                    </thead>
                                    <tbody id="accounts-table-body" class="divide-y divide-slate-100">
                                        <!-- Dynamic content -->
                                    </tbody>
                                </table>
                            </div>
                        </div>

                        <!-- Change Password Form -->
                        <div class="lg:col-span-5 bg-white p-6 rounded-2xl border border-slate-200/80 shadow-sm space-y-4">
                            <h3 class="font-bold text-slate-800 text-base border-b border-slate-100 pb-3">Ganti Password Saya</h3>
                            
                            <form onsubmit="handlePassChange(event)" class="space-y-4">
                                <div>
                                    <label class="block text-xs font-semibold text-slate-600 mb-1">Password Lama</label>
                                    <input type="password" id="pass-old" required class="w-full px-3 py-2 border border-slate-200 rounded-xl text-sm focus:ring-2 focus:ring-indigo-500 outline-none">
                                </div>
                                <div>
                                    <label class="block text-xs font-semibold text-slate-600 mb-1">Password Baru</label>
                                    <input type="password" id="pass-new" required class="w-full px-3 py-2 border border-slate-200 rounded-xl text-sm focus:ring-2 focus:ring-indigo-500 outline-none">
                                </div>
                                <div>
                                    <label class="block text-xs font-semibold text-slate-600 mb-1">Konfirmasi Password Baru</label>
                                    <input type="password" id="pass-confirm" required class="w-full px-3 py-2 border border-slate-200 rounded-xl text-sm focus:ring-2 focus:ring-indigo-500 outline-none">
                                </div>
                                <button type="submit" class="w-full bg-slate-800 hover:bg-slate-900 text-white font-semibold py-2.5 rounded-xl text-sm transition">
                                    Update Password
                                </button>
                            </form>
                        </div>
                    </div>
                </div>

                <!-- SUB-TAB 3: RESET ALL DATA -->
                <div id="subtab-reset" class="setting-subpanel hidden">
                    <div class="bg-rose-50 border border-rose-200 p-6 rounded-2xl max-w-2xl space-y-4">
                        <div class="flex items-center space-x-3 text-rose-700">
                            <i data-lucide="alert-octagon" class="w-7 h-7"></i>
                            <h3 class="font-bold text-lg">Zona Bahaya: Reset Seluruh Data</h3>
                        </div>
                        <p class="text-sm text-rose-600 leading-relaxed">
                            Tindakan ini akan menghapus **seluruh data transaksi, histori closing stock, dan daftar barang bawaan**, lalu mengembalikan aplikasi ke kondisi awal / data dummy pabrik. Akun admin default tidak akan terhapus.
                        </p>
                        <div class="pt-2">
                            <button onclick="triggerResetAllData()" class="bg-rose-600 hover:bg-rose-700 text-white font-bold px-5 py-2.5 rounded-xl text-sm transition shadow-md shadow-rose-500/20 flex items-center space-x-2">
                                <i data-lucide="trash-2" class="w-4 h-4"></i>
                                <span>Reset All Data Sekarang</span>
                            </button>
                        </div>
                    </div>
                </div>
            </section>

        </div>
    </main>

    <!-- MODAL 1: ADD/EDIT PRODUCT -->
    <div id="product-modal" class="fixed inset-0 bg-slate-900/50 backdrop-blur-sm z-50 flex items-center justify-center hidden p-4">
        <div class="bg-white rounded-2xl max-w-md w-full p-6 shadow-2xl space-y-4">
            <div class="flex justify-between items-center border-b border-slate-100 pb-3">
                <h3 id="product-modal-title" class="font-bold text-slate-800">Tambah Barang Baru</h3>
                <button onclick="closeProductModal()" class="text-slate-400 hover:text-slate-600"><i data-lucide="x" class="w-5 h-5"></i></button>
            </div>
            <form onsubmit="saveProduct(event)" class="space-y-3">
                <input type="hidden" id="prod-id">
                <div>
                    <label class="block text-xs font-medium text-slate-600 mb-1">Kode Barang / Barcode</label>
                    <input type="text" id="prod-code" required class="w-full px-3 py-2 border border-slate-200 rounded-xl text-sm focus:ring-2 focus:ring-indigo-500 outline-none">
                </div>
                <div>
                    <label class="block text-xs font-medium text-slate-600 mb-1">Nama Barang</label>
                    <input type="text" id="prod-name" required class="w-full px-3 py-2 border border-slate-200 rounded-xl text-sm focus:ring-2 focus:ring-indigo-500 outline-none">
                </div>
                <div>
                    <label class="block text-xs font-medium text-slate-600 mb-1">Kategori</label>
                    <input type="text" id="prod-category" required placeholder="Contoh: Makanan, Minuman" class="w-full px-3 py-2 border border-slate-200 rounded-xl text-sm focus:ring-2 focus:ring-indigo-500 outline-none">
                </div>
                <div class="grid grid-cols-2 gap-3">
                    <div>
                        <label class="block text-xs font-medium text-slate-600 mb-1">Harga Beli (Rp)</label>
                        <input type="number" id="prod-buy-price" required class="w-full px-3 py-2 border border-slate-200 rounded-xl text-sm focus:ring-2 focus:ring-indigo-500 outline-none">
                    </div>
                    <div>
                        <label class="block text-xs font-medium text-slate-600 mb-1">Harga Jual (Rp)</label>
                        <input type="number" id="prod-sell-price" required class="w-full px-3 py-2 border border-slate-200 rounded-xl text-sm focus:ring-2 focus:ring-indigo-500 outline-none">
                    </div>
                </div>
                <div>
                    <label class="block text-xs font-medium text-slate-600 mb-1">Jumlah Stok</label>
                    <input type="number" id="prod-stock" required class="w-full px-3 py-2 border border-slate-200 rounded-xl text-sm focus:ring-2 focus:ring-indigo-500 outline-none">
                </div>
                <div class="pt-3 flex justify-end space-x-2">
                    <button type="button" onclick="closeProductModal()" class="px-4 py-2 border border-slate-200 rounded-xl text-sm text-slate-600 hover:bg-slate-50">Batal</button>
                    <button type="submit" class="px-4 py-2 bg-indigo-600 hover:bg-indigo-700 text-white rounded-xl text-sm font-semibold">Simpan</button>
                </div>
            </form>
        </div>
    </div>

    <!-- MODAL 2: ADD USER ACCOUNT -->
    <div id="account-modal" class="fixed inset-0 bg-slate-900/50 backdrop-blur-sm z-50 flex items-center justify-center hidden p-4">
        <div class="bg-white rounded-2xl max-w-md w-full p-6 shadow-2xl space-y-4">
            <div class="flex justify-between items-center border-b border-slate-100 pb-3">
                <h3 class="font-bold text-slate-800">Tambah Akun Pengguna</h3>
                <button onclick="closeAccountModal()" class="text-slate-400 hover:text-slate-600"><i data-lucide="x" class="w-5 h-5"></i></button>
            </div>
            <form onsubmit="saveNewAccount(event)" class="space-y-3">
                <div>
                    <label class="block text-xs font-medium text-slate-600 mb-1">Nama Lengkap</label>
                    <input type="text" id="acc-fullname" required class="w-full px-3 py-2 border rounded-xl text-sm focus:ring-2 focus:ring-indigo-500 outline-none">
                </div>
                <div>
                    <label class="block text-xs font-medium text-slate-600 mb-1">Username</label>
                    <input type="text" id="acc-username" required class="w-full px-3 py-2 border rounded-xl text-sm focus:ring-2 focus:ring-indigo-500 outline-none">
                </div>
                <div>
                    <label class="block text-xs font-medium text-slate-600 mb-1">Password</label>
                    <input type="password" id="acc-password" required class="w-full px-3 py-2 border rounded-xl text-sm focus:ring-2 focus:ring-indigo-500 outline-none">
                </div>
                <div>
                    <label class="block text-xs font-medium text-slate-600 mb-1">Role / Peran</label>
                    <select id="acc-role" class="w-full px-3 py-2 border rounded-xl text-sm focus:ring-2 focus:ring-indigo-500 outline-none bg-white">
                        <option value="kasir">Kasir</option>
                        <option value="admin">Administrator</option>
                    </select>
                </div>
                <div class="pt-3 flex justify-end space-x-2">
                    <button type="button" onclick="closeAccountModal()" class="px-4 py-2 border rounded-xl text-sm text-slate-600 hover:bg-slate-50">Batal</button>
                    <button type="submit" class="px-4 py-2 bg-indigo-600 text-white rounded-xl text-sm font-semibold hover:bg-indigo-700">Simpan Akun</button>
                </div>
            </form>
        </div>
    </div>

    <!-- MODAL 3: PREVIEW STRUK NOTA PENJUALAN -->
    <div id="receipt-modal" class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm z-50 flex items-center justify-center hidden p-4">
        <div class="bg-white rounded-2xl max-w-sm w-full p-5 shadow-2xl space-y-4 max-h-[90vh] flex flex-col">
            <div class="flex justify-between items-center border-b border-slate-100 pb-2">
                <h3 class="font-bold text-slate-800 text-sm flex items-center space-x-1">
                    <i data-lucide="file-check" class="w-4 h-4 text-emerald-600"></i>
                    <span>Preview Struk Penjualan</span>
                </h3>
                <button onclick="closeReceiptModal()" class="text-slate-400 hover:text-slate-600"><i data-lucide="x" class="w-5 h-5"></i></button>
            </div>
            
            <!-- Printable Receipt Layout -->
            <div id="receipt-preview-content" class="flex-1 overflow-y-auto bg-slate-50 p-4 rounded-xl text-xs font-mono border border-slate-200 text-slate-800 space-y-3">
                <!-- Populated via JS -->
            </div>

            <!-- Modal Action Buttons -->
            <div class="flex justify-end space-x-2 pt-2 border-t border-slate-100">
                <button onclick="closeReceiptModal()" class="px-4 py-2 border rounded-xl text-xs font-medium text-slate-600 hover:bg-slate-50">Batal</button>
                <button onclick="executeCheckoutAndPrint()" class="px-4 py-2 bg-indigo-600 hover:bg-indigo-700 text-white rounded-xl text-xs font-bold flex items-center space-x-1.5">
                    <i data-lucide="printer" class="w-4 h-4"></i>
                    <span>Cetak & Selesaikan</span>
                </button>
            </div>
        </div>
    </div>

    <!-- PRINT ONLY AREA -->
    <div id="print-area" class="hidden"></div>

    <!-- JAVASCRIPT APP LOGIC -->
    <script>
        // INITIAL DEFAULT DATA (FACTORY DEFAULT)
        const DEFAULT_STORE = {
            name: "Toko Kelontong Berkah",
            phone: "0812-3456-7890",
            address: "Jl. Merdeka No. 45, Jakarta Selatan",
            footer: "Terima kasih atas kunjungan Anda! Barang yang sudah dibeli tidak dapat ditukar."
        };

        const DEFAULT_USERS = [
            { id: 1, name: "Admin Utama", username: "admin", password: "123", role: "admin" },
            { id: 2, name: "Kasir Shift A", username: "kasir", password: "123", role: "kasir" }
        ];

        const DEFAULT_PRODUCTS = [
            { id: "P1", code: "8991001", name: "Beras Premium 5kg", category: "Sembako", buyPrice: 60000, sellPrice: 68000, stock: 25 },
            { id: "P2", code: "8991002", name: "Minyak Goreng 2L", category: "Sembako", buyPrice: 28000, sellPrice: 34000, stock: 12 },
            { id: "P3", code: "8991003", name: "Gula Pasir 1kg", category: "Sembako", buyPrice: 13500, sellPrice: 16000, stock: 4 },
            { id: "P4", code: "8991004", name: "Kopi Instant Sachet", category: "Minuman", buyPrice: 1200, sellPrice: 1800, stock: 50 },
            { id: "P5", code: "8991005", name: "Mie Instan Goreng", category: "Makanan", buyPrice: 2500, sellPrice: 3100, stock: 3 }
        ];

        // STATE MANAGEMENT
        let storeConfig = JSON.parse(localStorage.getItem('iq_store')) || DEFAULT_STORE;
        let users = JSON.parse(localStorage.getItem('iq_users')) || DEFAULT_USERS;
        let products = JSON.parse(localStorage.getItem('iq_products')) || DEFAULT_PRODUCTS;
        let transactions = JSON.parse(localStorage.getItem('iq_transactions')) || [];
        let closings = JSON.parse(localStorage.getItem('iq_closings')) || [];
        let currentUser = JSON.parse(localStorage.getItem('iq_current_user')) || DEFAULT_USERS[0];

        let cart = [];
        let salesChartInstance = null;

        // INITIALIZATION
        window.onload = function() {
            lucide.createIcons();
            document.getElementById('current-date-display').textContent = new Date().toLocaleDateString('id-ID', { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' });
            
            updateHeaderStoreInfo();
            updateUserInfo();
            initChart();
            renderDashboard();
            renderPosProducts();
            renderInventoryTable();
            populateCategoryDropdowns();
            loadStoreConfigToForm();
            renderAccountsTable();

            // Default dates for reports
            const today = new Date().toISOString().split('T')[0];
            document.getElementById('rep-start-date').value = today;
            document.getElementById('rep-end-date').value = today;

            // Mobile menu toggle
            document.getElementById('mobile-menu-btn').onclick = function() {
                const sidebar = document.getElementById('sidebar');
                sidebar.classList.toggle('-translate-x-full');
            };
        };

        // SAVE STATE TO LOCAL STORAGE
        function saveData() {
            localStorage.setItem('iq_store', JSON.stringify(storeConfig));
            localStorage.setItem('iq_users', JSON.stringify(users));
            localStorage.setItem('iq_products', JSON.stringify(products));
            localStorage.setItem('iq_transactions', JSON.stringify(transactions));
            localStorage.setItem('iq_closings', JSON.stringify(closings));
            localStorage.setItem('iq_current_user', JSON.stringify(currentUser));
        }

        // NAVIGATION TABS
        function switchTab(tabId) {
            document.querySelectorAll('.tab-content').forEach(el => el.classList.add('hidden'));
            document.getElementById(`tab-${tabId}`).classList.remove('hidden');

            document.querySelectorAll('.nav-btn').forEach(btn => {
                btn.classList.remove('bg-indigo-600', 'text-white');
                btn.classList.add('text-slate-400');
            });

            const activeBtn = document.querySelector(`.nav-btn[data-tab="${tabId}"]`);
            if (activeBtn) {
                activeBtn.classList.add('bg-indigo-600', 'text-white');
                activeBtn.classList.remove('text-slate-400');
            }

            const titles = {
                dashboard: 'Dashboard Utama',
                pos: 'Kasir & Transaksi',
                inventory: 'Manajemen Data Barang',
                closing: 'Closing Stock Akhir Bulan',
                reports: 'Laporan Penjualan',
                settings: 'Pengaturan Sistem'
            };
            document.getElementById('page-title').textContent = titles[tabId];

            if (tabId === 'dashboard') renderDashboard();
            if (tabId === 'pos') renderPosProducts();
            if (tabId === 'inventory') renderInventoryTable();
            if (tabId === 'closing') renderClosingForm();
            if (tabId === 'reports') renderReports();
        }

        // HELPER UTILS
        function formatRp(amount) {
            return 'Rp ' + Number(amount || 0).toLocaleString('id-ID');
        }

        function updateHeaderStoreInfo() {
            document.querySelectorAll('.app-title').forEach(e => e.textContent = storeConfig.name || "inventoryQu");
            document.getElementById('store-name-top').textContent = storeConfig.address || "";
        }

        function updateUserInfo() {
            document.getElementById('user-name-display').textContent = currentUser.name;
            document.getElementById('user-role-display').textContent = currentUser.role;
            document.getElementById('user-avatar').textContent = currentUser.name.charAt(0).toUpperCase();
        }

        function logout() {
            alert('Logout berhasil.');
        }

        // DASHBOARD LOGIC
        function renderDashboard() {
            const todayStr = new Date().toISOString().split('T')[0];
            const todayTxs = transactions.filter(t => t.date.startsWith(todayStr));
            
            const todayIncome = todayTxs.reduce((sum, t) => sum + t.total, 0);
            const lowStockCount = products.filter(p => p.stock <= 5).length;

            document.getElementById('dash-today-income').textContent = formatRp(todayIncome);
            document.getElementById('dash-today-count').textContent = todayTxs.length;
            document.getElementById('dash-total-items').textContent = products.length;
            document.getElementById('dash-low-stock').textContent = lowStockCount;

            // Low Stock Items List
            const lowStockList = document.getElementById('dash-low-stock-list');
            const lowItems = products.filter(p => p.stock <= 5);
            
            if (lowItems.length === 0) {
                lowStockList.innerHTML = '<p class="text-xs text-slate-400 italic">Semua stok barang mencukupi.</p>';
            } else {
                lowStockList.innerHTML = lowItems.map(p => `
                    <div class="flex justify-between items-center p-2.5 bg-amber-50/60 rounded-xl border border-amber-200/50 text-xs">
                        <div>
                            <p class="font-semibold text-slate-800">${p.name}</p>
                            <p class="text-slate-500">${p.code}</p>
                        </div>
                        <span class="px-2 py-1 rounded-lg bg-amber-200 text-amber-800 font-bold">Stok: ${p.stock}</span>
                    </div>
                `).join('');
            }

            updateChartData();
        }

        function initChart() {
            const ctx = document.getElementById('salesChart').getContext('2d');
            salesChartInstance = new Chart(ctx, {
                type: 'line',
                data: {
                    labels: [],
                    datasets: [{
                        label: 'Penjualan (Rp)',
                        data: [],
                        borderColor: '#4f46e5',
                        backgroundColor: 'rgba(79, 70, 229, 0.1)',
                        fill: true,
                        tension: 0.3
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: { legend: { display: false } },
                    scales: { y: { beginAtZero: true } }
                }
            });
        }

        function updateChartData() {
            if (!salesChartInstance) return;
            const labels = [];
            const data = [];
            
            for (let i = 6; i >= 0; i--) {
                const d = new Date();
                d.setDate(d.getDate() - i);
                const dateStr = d.toISOString().split('T')[0];
                labels.push(d.toLocaleDateString('id-ID', { weekday: 'short', day: 'numeric' }));
                
                const total = transactions
                    .filter(t => t.date.startsWith(dateStr))
                    .reduce((sum, t) => sum + t.total, 0);
                data.push(total);
            }

            salesChartInstance.data.labels = labels;
            salesChartInstance.data.datasets[0].data = data;
            salesChartInstance.update();
        }

        // POS KASIR LOGIC
        function populateCategoryDropdowns() {
            const categories = [...new Set(products.map(p => p.category))];
            const posSelect = document.getElementById('pos-category-filter');
            const invSelect = document.getElementById('inv-category-filter');
            
            const optionsHtml = '<option value="">Semua Kategori</option>' + categories.map(c => `<option value="${c}">${c}</option>`).join('');
            posSelect.innerHTML = optionsHtml;
            invSelect.innerHTML = optionsHtml;
        }

        function renderPosProducts() {
            const search = document.getElementById('pos-search').value.toLowerCase();
            const category = document.getElementById('pos-category-filter').value;
            const grid = document.getElementById('pos-product-grid');

            const filtered = products.filter(p => {
                const matchSearch = p.name.toLowerCase().includes(search) || p.code.toLowerCase().includes(search);
                const matchCategory = !category || p.category === category;
                return matchSearch && matchCategory;
            });

            grid.innerHTML = filtered.map(p => `
                <div onclick="addToCart('${p.id}')" class="bg-white p-3.5 rounded-2xl border border-slate-200/80 shadow-sm hover:border-indigo-400 cursor-pointer transition flex flex-col justify-between space-y-2">
                    <div>
                        <span class="text-[10px] font-semibold px-2 py-0.5 rounded-full bg-slate-100 text-slate-600">${p.category}</span>
                        <h4 class="font-bold text-slate-800 text-sm mt-1.5 leading-snug">${p.name}</h4>
                        <p class="text-xs text-slate-400">${p.code}</p>
                    </div>
                    <div class="flex justify-between items-center pt-2 border-t border-slate-100">
                        <span class="font-bold text-indigo-600 text-sm">${formatRp(p.sellPrice)}</span>
                        <span class="text-xs font-medium ${p.stock <= 5 ? 'text-amber-600 font-bold' : 'text-slate-500'}">Stok: ${p.stock}</span>
                    </div>
                </div>
            `).join('');
        }

        function addToCart(productId) {
            const product = products.find(p => p.id === productId);
            if (!product) return;

            const existing = cart.find(item => item.id === productId);
            const currentQty = existing ? existing.qty : 0;

            if (currentQty + 1 > product.stock) {
                alert(`Stok ${product.name} tidak mencukupi! (Sisa: ${product.stock})`);
                return;
            }

            if (existing) {
                existing.qty++;
            } else {
                cart.push({ ...product, qty: 1 });
            }

            renderCart();
        }

        function updateCartQty(productId, delta) {
            const item = cart.find(i => i.id === productId);
            if (!item) return;

            const product = products.find(p => p.id === productId);
            if (delta > 0 && item.qty + delta > product.stock) {
                alert(`Stok tidak mencukupi! (Sisa: ${product.stock})`);
                return;
            }

            item.qty += delta;
            if (item.qty <= 0) {
                cart = cart.filter(i => i.id !== productId);
            }
            renderCart();
        }

        function clearCart() {
            cart = [];
            renderCart();
        }

        function renderCart() {
            const container = document.getElementById('pos-cart-items');
            if (cart.length === 0) {
                container.innerHTML = `
                    <div class="text-center py-12 text-slate-400 space-y-2">
                        <i data-lucide="shopping-bag" class="w-10 h-10 mx-auto stroke-1"></i>
                        <p class="text-xs">Keranjang masih kosong</p>
                    </div>
                `;
                lucide.createIcons();
            } else {
                container.innerHTML = cart.map(item => `
                    <div class="flex justify-between items-center bg-slate-50 p-2.5 rounded-xl border border-slate-200/60 text-xs">
                        <div class="flex-1 pr-2">
                            <p class="font-bold text-slate-800">${item.name}</p>
                            <p class="text-slate-500">${formatRp(item.sellPrice)}</p>
                        </div>
                        <div class="flex items-center space-x-2">
                            <button onclick="updateCartQty('${item.id}', -1)" class="w-6 h-6 rounded-lg bg-white border border-slate-200 flex items-center justify-center text-slate-600 hover:bg-slate-100 font-bold">-</button>
                            <span class="font-semibold text-slate-800 w-5 text-center">${item.qty}</span>
                            <button onclick="updateCartQty('${item.id}', 1)" class="w-6 h-6 rounded-lg bg-white border border-slate-200 flex items-center justify-center text-slate-600 hover:bg-slate-100 font-bold">+</button>
                        </div>
                    </div>
                `).join('');
            }

            const subtotal = cart.reduce((sum, item) => sum + (item.sellPrice * item.qty), 0);
            document.getElementById('pos-subtotal').textContent = formatRp(subtotal);
            document.getElementById('pos-total').textContent = formatRp(subtotal);
            calculateKembalian();
        }

        function calculateKembalian() {
            const total = cart.reduce((sum, item) => sum + (item.sellPrice * item.qty), 0);
            const pay = parseFloat(document.getElementById('pos-pay-amount').value) || 0;
            const change = pay - total;
            const changeEl = document.getElementById('pos-change-amount');
            
            if (change >= 0) {
                changeEl.textContent = formatRp(change);
                changeEl.className = "text-emerald-600 font-bold";
            } else {
                changeEl.textContent = "Kurang " + formatRp(Math.abs(change));
                changeEl.className = "text-rose-500 font-bold";
            }
        }

        // PREVIEW RECEIPT MODAL
        function openReceiptPreview() {
            if (cart.length === 0) {
                alert("Keranjang masih kosong!");
                return;
            }

            const total = cart.reduce((sum, item) => sum + (item.sellPrice * item.qty), 0);
            const pay = parseFloat(document.getElementById('pos-pay-amount').value) || 0;
            if (pay < total) {
                alert("Nominal pembayaran masih kurang!");
                return;
            }

            const customer = document.getElementById('pos-customer-name').value || "Umum";
            const invoiceNo = "INV-" + Date.now().toString().slice(-6);
            const dateStr = new Date().toLocaleString('id-ID');

            let itemsHtml = cart.map(i => `
                <div class="flex justify-between text-xs">
                    <span class="truncate flex-1">${i.name} x${i.qty}</span>
                    <span>${formatRp(i.sellPrice * i.qty)}</span>
                </div>
            `).join('');

            const receiptHtml = `
                <div class="text-center space-y-1 pb-3 border-b border-dashed border-slate-300">
                    <h4 class="font-bold text-sm tracking-wide uppercase text-slate-900">${storeConfig.name}</h4>
                    <p class="text-[10px] text-slate-500 leading-tight">${storeConfig.address}</p>
                    <p class="text-[10px] text-slate-500">Telp: ${storeConfig.phone}</p>
                </div>
                <div class="py-2 border-b border-dashed border-slate-300 space-y-0.5 text-[11px] text-slate-600">
                    <div class="flex justify-between"><span>No Nota:</span><span>${invoiceNo}</span></div>
                    <div class="flex justify-between"><span>Tanggal:</span><span>${dateStr}</span></div>
                    <div class="flex justify-between"><span>Pelanggan:</span><span>${customer}</span></div>
                    <div class="flex justify-between"><span>Kasir:</span><span>${currentUser.name}</span></div>
                </div>
                <div class="py-2 border-b border-dashed border-slate-300 space-y-1.5">
                    ${itemsHtml}
                </div>
                <div class="py-2 border-b border-dashed border-slate-300 space-y-1 text-xs font-bold">
                    <div class="flex justify-between"><span>Total:</span><span>${formatRp(total)}</span></div>
                    <div class="flex justify-between font-normal"><span>Bayar:</span><span>${formatRp(pay)}</span></div>
                    <div class="flex justify-between font-normal"><span>Kembali:</span><span>${formatRp(pay - total)}</span></div>
                </div>
                <div class="text-center pt-2 text-[10px] text-slate-500 italic">
                    <p>${storeConfig.footer}</p>
                </div>
            `;

            document.getElementById('receipt-preview-content').innerHTML = receiptHtml;
            document.getElementById('receipt-modal').classList.remove('hidden');
        }

        function closeReceiptModal() {
            document.getElementById('receipt-modal').classList.add('hidden');
        }

        function executeCheckoutAndPrint() {
            const total = cart.reduce((sum, item) => sum + (item.sellPrice * item.qty), 0);
            const totalBuy = cart.reduce((sum, item) => sum + (item.buyPrice * item.qty), 0);
            const pay = parseFloat(document.getElementById('pos-pay-amount').value) || 0;
            const customer = document.getElementById('pos-customer-name').value || "Umum";
            const invoiceNo = "INV-" + Date.now().toString().slice(-6);

            // Deduct Stocks
            cart.forEach(cartItem => {
                const prod = products.find(p => p.id === cartItem.id);
                if (prod) prod.stock -= cartItem.qty;
            });

            // Save Transaction
            const tx = {
                id: invoiceNo,
                date: new Date().toISOString(),
                customer: customer,
                items: [...cart],
                total: total,
                profit: total - totalBuy,
                pay: pay,
                cashier: currentUser.name
            };
            transactions.unshift(tx);
            saveData();

            // Print Area Trigger
            const printContent = document.getElementById('receipt-preview-content').innerHTML;
            const printArea = document.getElementById('print-area');
            printArea.innerHTML = `<div class="p-6 max-w-xs mx-auto font-mono text-xs">${printContent}</div>`;
            
            closeReceiptModal();
            window.print();

            // Reset POS State
            cart = [];
            document.getElementById('pos-pay-amount').value = '';
            document.getElementById('pos-customer-name').value = '';
            renderCart();
            renderPosProducts();
            renderDashboard();
            alert('Transaksi berhasil disimpan!');
        }

        // INVENTORY MANAGEMENT LOGIC
        function renderInventoryTable() {
            const search = document.getElementById('inv-search').value.toLowerCase();
            const category = document.getElementById('inv-category-filter').value;
            const tbody = document.getElementById('inv-table-body');

            const filtered = products.filter(p => {
                const matchSearch = p.name.toLowerCase().includes(search) || p.code.toLowerCase().includes(search);
                const matchCategory = !category || p.category === category;
                return matchSearch && matchCategory;
            });

            tbody.innerHTML = filtered.map(p => `
                <tr class="hover:bg-slate-50/80 transition">
                    <td class="p-4 font-semibold text-slate-800">${p.code}</td>
                    <td class="p-4 font-bold text-slate-800">${p.name}</td>
                    <td class="p-4"><span class="px-2.5 py-1 rounded-lg bg-slate-100 text-slate-600 text-xs font-medium">${p.category}</span></td>
                    <td class="p-4 text-right">${formatRp(p.buyPrice)}</td>
                    <td class="p-4 text-right font-semibold text-indigo-600">${formatRp(p.sellPrice)}</td>
                    <td class="p-4 text-center">
                        <span class="px-2.5 py-1 rounded-lg font-bold text-xs ${p.stock <= 5 ? 'bg-amber-100 text-amber-700' : 'bg-emerald-100 text-emerald-700'}">${p.stock}</span>
                    </td>
                    <td class="p-4 text-center space-x-2">
                        <button onclick="editProduct('${p.id}')" class="text-slate-400 hover:text-indigo-600 p-1"><i data-lucide="edit-3" class="w-4 h-4"></i></button>
                        <button onclick="deleteProduct('${p.id}')" class="text-slate-400 hover:text-rose-600 p-1"><i data-lucide="trash-2" class="w-4 h-4"></i></button>
                    </td>
                </tr>
            `).join('');

            lucide.createIcons();
        }

        function openProductModal(isEdit = false) {
            document.getElementById('product-modal-title').textContent = isEdit ? 'Edit Barang' : 'Tambah Barang Baru';
            if (!isEdit) {
                document.getElementById('prod-id').value = '';
                document.getElementById('prod-code').value = 'PRD-' + Math.floor(1000 + Math.random() * 9000);
                document.getElementById('prod-name').value = '';
                document.getElementById('prod-category').value = '';
                document.getElementById('prod-buy-price').value = '';
                document.getElementById('prod-sell-price').value = '';
                document.getElementById('prod-stock').value = '';
            }
            document.getElementById('product-modal').classList.remove('hidden');
        }

        function closeProductModal() {
            document.getElementById('product-modal').classList.add('hidden');
        }

        function saveProduct(e) {
            e.preventDefault();
            const id = document.getElementById('prod-id').value;
            const code = document.getElementById('prod-code').value;
            const name = document.getElementById('prod-name').value;
            const category = document.getElementById('prod-category').value;
            const buyPrice = parseFloat(document.getElementById('prod-buy-price').value);
            const sellPrice = parseFloat(document.getElementById('prod-sell-price').value);
            const stock = parseInt(document.getElementById('prod-stock').value);

            if (id) {
                const prod = products.find(p => p.id === id);
                if (prod) {
                    prod.code = code; prod.name = name; prod.category = category;
                    prod.buyPrice = buyPrice; prod.sellPrice = sellPrice; prod.stock = stock;
                }
            } else {
                products.push({ id: 'P-' + Date.now(), code, name, category, buyPrice, sellPrice, stock });
            }

            saveData();
            populateCategoryDropdowns();
            renderInventoryTable();
            renderPosProducts();
            closeProductModal();
        }

        function editProduct(id) {
            const p = products.find(item => item.id === id);
            if (!p) return;
            document.getElementById('prod-id').value = p.id;
            document.getElementById('prod-code').value = p.code;
            document.getElementById('prod-name').value = p.name;
            document.getElementById('prod-category').value = p.category;
            document.getElementById('prod-buy-price').value = p.buyPrice;
            document.getElementById('prod-sell-price').value = p.sellPrice;
            document.getElementById('prod-stock').value = p.stock;
            openProductModal(true);
        }

        function deleteProduct(id) {
            if (confirm("Apakah Anda yakin ingin menghapus barang ini?")) {
                products = products.filter(p => p.id !== id);
                saveData();
                renderInventoryTable();
                renderPosProducts();
            }
        }

        // CLOSING STOCK AKHIR BULAN LOGIC
        function renderClosingForm() {
            const now = new Date();
            const monthNames = ["Januari", "Februari", "Maret", "April", "Mei", "Juni", "Juli", "Agustus", "September", "Oktober", "November", "Desember"];
            document.getElementById('closing-period-label').textContent = `${monthNames[now.getMonth()]} ${now.getFullYear()}`;

            const tbody = document.getElementById('closing-table-body');
            tbody.innerHTML = products.map(p => `
                <tr>
                    <td class="p-3 font-semibold text-slate-800">${p.name} <br><span class="text-xs font-normal text-slate-400">${p.code}</span></td>
                    <td class="p-3 text-center text-slate-500">${p.stock + 5}</td>
                    <td class="p-3 text-center text-emerald-600 font-medium">+5</td>
                    <td class="p-3 text-center text-indigo-600 font-medium">10</td>
                    <td class="p-3 text-center font-bold text-slate-700">${p.stock}</td>
                    <td class="p-3 text-center">
                        <input type="number" data-pid="${p.id}" value="${p.stock}" oninput="calculateClosingDiff(this, ${p.stock})" class="closing-physical-input w-20 text-center px-2 py-1 border border-slate-300 rounded-lg text-sm font-bold focus:ring-2 focus:ring-indigo-500 outline-none">
                    </td>
                    <td class="p-3 text-center font-bold text-xs" id="diff-${p.id}">0</td>
                </tr>
            `).join('');

            renderClosingHistory();
        }

        function calculateClosingDiff(inputEl, systemStock) {
            const physical = parseInt(inputEl.value) || 0;
            const diff = physical - systemStock;
            const diffEl = document.getElementById(`diff-${inputEl.dataset.pid}`);
            
            diffEl.textContent = diff > 0 ? `+${diff}` : diff;
            if (diff === 0) diffEl.className = "p-3 text-center font-bold text-xs text-slate-500";
            else if (diff < 0) diffEl.className = "p-3 text-center font-bold text-xs text-rose-600";
            else diffEl.className = "p-3 text-center font-bold text-xs text-emerald-600";
        }

        function saveClosingStock() {
            const now = new Date();
            const period = document.getElementById('closing-period-label').textContent;
            const inputs = document.querySelectorAll('.closing-physical-input');
            let totalDiff = 0;

            inputs.forEach(inp => {
                const pid = inp.dataset.pid;
                const physical = parseInt(inp.value) || 0;
                const prod = products.find(p => p.id === pid);
                if (prod) {
                    totalDiff += Math.abs(physical - prod.stock);
                    prod.stock = physical; // Update actual stock
                }
            });

            const newClosing = {
                id: 'CLS-' + Date.now(),
                date: now.toLocaleString('id-ID'),
                period: period,
                officer: currentUser.name,
                itemCount: products.length,
                totalDiff: totalDiff
            };

            closings.unshift(newClosing);
            saveData();
            renderClosingHistory();
            renderInventoryTable();
            alert(`Closing Stock untuk periode ${period} berhasil disimpan dan disesuaikan!`);
        }

        function renderClosingHistory() {
            const tbody = document.getElementById('closing-history-body');
            if (closings.length === 0) {
                tbody.innerHTML = '<tr><td colspan="6" class="p-4 text-center text-xs text-slate-400">Belum ada riwayat closing stock.</td></tr>';
                return;
            }

            tbody.innerHTML = closings.map(c => `
                <tr class="hover:bg-slate-50">
                    <td class="p-3 text-slate-600 text-xs">${c.date}</td>
                    <td class="p-3 font-semibold text-indigo-600">${c.period}</td>
                    <td class="p-3 text-slate-700">${c.officer}</td>
                    <td class="p-3 text-center font-semibold">${c.itemCount} Item</td>
                    <td class="p-3 text-center"><span class="px-2 py-0.5 rounded-md text-xs font-bold ${c.totalDiff > 0 ? 'bg-amber-100 text-amber-700' : 'bg-emerald-100 text-emerald-700'}">${c.totalDiff} Selisih</span></td>
                    <td class="p-3 text-center">
                        <button onclick="alert('Cetak ulang Closing Periode: ${c.period}')" class="text-xs bg-slate-100 hover:bg-slate-200 text-slate-700 font-medium px-2.5 py-1 rounded-lg">Cetak</button>
                    </td>
                </tr>
            `).join('');
        }

        // LAPORAN PENJUALAN LOGIC
        function renderReports() {
            const start = document.getElementById('rep-start-date').value;
            const end = document.getElementById('rep-end-date').value;
            
            const filtered = transactions.filter(t => {
                const dateStr = t.date.split('T')[0];
                return dateStr >= start && dateStr <= end;
            });

            const totalRev = filtered.reduce((sum, t) => sum + t.total, 0);
            const totalProfit = filtered.reduce((sum, t) => sum + t.profit, 0);

            document.getElementById('rep-total-revenue').textContent = formatRp(totalRev);
            document.getElementById('rep-total-profit').textContent = formatRp(totalProfit);
            document.getElementById('rep-total-tx').textContent = filtered.length;

            const tbody = document.getElementById('rep-table-body');
            tbody.innerHTML = filtered.map(t => `
                <tr class="hover:bg-slate-50/80">
                    <td class="p-4 font-bold text-slate-800">${t.id}</td>
                    <td class="p-4 text-xs text-slate-500">${new Date(t.date).toLocaleString('id-ID')}</td>
                    <td class="p-4">${t.customer}</td>
                    <td class="p-4 text-right font-bold text-slate-800">${formatRp(t.total)}</td>
                    <td class="p-4 text-right font-semibold text-emerald-600">${formatRp(t.profit)}</td>
                    <td class="p-4 text-center">
                        <button onclick="reprintTx('${t.id}')" class="text-slate-400 hover:text-indigo-600 p-1"><i data-lucide="printer" class="w-4 h-4"></i></button>
                    </td>
                </tr>
            `).join('');

            lucide.createIcons();
        }

        function reprintTx(txId) {
            const tx = transactions.find(t => t.id === txId);
            if (!tx) return;

            let itemsHtml = tx.items.map(i => `
                <div class="flex justify-between text-xs">
                    <span class="truncate flex-1">${i.name} x${i.qty}</span>
                    <span>${formatRp(i.sellPrice * i.qty)}</span>
                </div>
            `).join('');

            const receiptHtml = `
                <div class="text-center space-y-1 pb-3 border-b border-dashed border-slate-300">
                    <h4 class="font-bold text-sm tracking-wide uppercase text-slate-900">${storeConfig.name}</h4>
                    <p class="text-[10px] text-slate-500 leading-tight">${storeConfig.address}</p>
                    <p class="text-[10px] text-slate-500">Telp: ${storeConfig.phone}</p>
                </div>
                <div class="py-2 border-b border-dashed border-slate-300 space-y-0.5 text-[11px] text-slate-600">
                    <div class="flex justify-between"><span>No Nota:</span><span>${tx.id}</span></div>
                    <div class="flex justify-between"><span>Tanggal:</span><span>${new Date(tx.date).toLocaleString('id-ID')}</span></div>
                    <div class="flex justify-between"><span>Pelanggan:</span><span>${tx.customer}</span></div>
                    <div class="flex justify-between"><span>Kasir:</span><span>${tx.cashier}</span></div>
                </div>
                <div class="py-2 border-b border-dashed border-slate-300 space-y-1.5">
                    ${itemsHtml}
                </div>
                <div class="py-2 border-b border-dashed border-slate-300 space-y-1 text-xs font-bold">
                    <div class="flex justify-between"><span>Total:</span><span>${formatRp(tx.total)}</span></div>
                    <div class="flex justify-between font-normal"><span>Bayar:</span><span>${formatRp(tx.pay)}</span></div>
                    <div class="flex justify-between font-normal"><span>Kembali:</span><span>${formatRp(tx.pay - tx.total)}</span></div>
                </div>
                <div class="text-center pt-2 text-[10px] text-slate-500 italic">
                    <p>${storeConfig.footer}</p>
                </div>
            `;

            document.getElementById('print-area').innerHTML = `<div class="p-6 max-w-xs mx-auto font-mono text-xs">${receiptHtml}</div>`;
            window.print();
        }

        function printReport() {
            const start = document.getElementById('rep-start-date').value;
            const end = document.getElementById('rep-end-date').value;
            alert(`Mencetak Laporan Penjualan periode ${start} s/d ${end}`);
            window.print();
        }

        // SETTINGS & USER ACCOUNTS LOGIC
        function switchSettingSubTab(subTab) {
            document.querySelectorAll('.setting-subpanel').forEach(p => p.classList.add('hidden'));
            document.querySelectorAll('.setting-subtab-btn').forEach(b => {
                b.classList.remove('border-indigo-600', 'text-indigo-600', 'border-rose-600', 'text-rose-600');
                b.classList.add('border-transparent', 'text-slate-500');
            });

            document.getElementById(`subtab-${subTab}`).classList.remove('hidden');
            const activeBtn = document.getElementById(`set-tab-${subTab}`);
            
            if (subTab === 'reset') {
                activeBtn.classList.add('border-rose-600', 'text-rose-600');
            } else {
                activeBtn.classList.add('border-indigo-600', 'text-indigo-600');
            }
        }

        function loadStoreConfigToForm() {
            document.getElementById('cfg-store-name').value = storeConfig.name;
            document.getElementById('cfg-store-phone').value = storeConfig.phone;
            document.getElementById('cfg-store-address').value = storeConfig.address;
            document.getElementById('cfg-store-footer').value = storeConfig.footer;
        }

        function saveStoreSettings() {
            storeConfig.name = document.getElementById('cfg-store-name').value;
            storeConfig.phone = document.getElementById('cfg-store-phone').value;
            storeConfig.address = document.getElementById('cfg-store-address').value;
            storeConfig.footer = document.getElementById('cfg-store-footer').value;

            saveData();
            updateHeaderStoreInfo();
            alert("Pengaturan Toko & Header Nota berhasil diperbarui!");
        }

        function renderAccountsTable() {
            const tbody = document.getElementById('accounts-table-body');
            tbody.innerHTML = users.map(u => `
                <tr>
                    <td class="p-3 font-bold text-slate-800">${u.username}</td>
                    <td class="p-3 text-slate-600">${u.name}</td>
                    <td class="p-3"><span class="px-2 py-0.5 rounded-md text-xs font-bold uppercase ${u.role === 'admin' ? 'bg-indigo-100 text-indigo-700' : 'bg-slate-100 text-slate-600'}">${u.role}</span></td>
                    <td class="p-3 text-center">
                        ${u.username !== 'admin' ? `<button onclick="deleteAccount('${u.id}')" class="text-slate-400 hover:text-rose-600 p-1"><i data-lucide="trash-2" class="w-4 h-4"></i></button>` : '<span class="text-xs text-slate-300">Utama</span>'}
                    </td>
                </tr>
            `).join('');
            lucide.createIcons();
        }

        function openAddAccountModal() {
            document.getElementById('account-modal').classList.remove('hidden');
        }

        function closeAccountModal() {
            document.getElementById('account-modal').classList.add('hidden');
        }

        function saveNewAccount(e) {
            e.preventDefault();
            const name = document.getElementById('acc-fullname').value;
            const username = document.getElementById('acc-username').value;
            const password = document.getElementById('acc-password').value;
            const role = document.getElementById('acc-role').value;

            if (users.some(u => u.username === username)) {
                alert("Username sudah digunakan!");
                return;
            }

            users.push({ id: Date.now(), name, username, password, role });
            saveData();
            renderAccountsTable();
            closeAccountModal();
            alert("Akun pengguna baru berhasil ditambahkan!");
        }

        function deleteAccount(id) {
            if (confirm("Hapus akun pengguna ini?")) {
                users = users.filter(u => u.id != id);
                saveData();
                renderAccountsTable();
            }
        }

        function handlePassChange(e) {
            e.preventDefault();
            const oldPass = document.getElementById('pass-old').value;
            const newPass = document.getElementById('pass-new').value;
            const confirmPass = document.getElementById('pass-confirm').value;

            if (oldPass !== currentUser.password) {
                alert("Password lama salah!");
                return;
            }

            if (newPass !== confirmPass) {
                alert("Konfirmasi password baru tidak cocok!");
                return;
            }

            currentUser.password = newPass;
            const uIdx = users.findIndex(u => u.id === currentUser.id);
            if (uIdx !== -1) users[uIdx].password = newPass;

            saveData();
            alert("Password Anda berhasil diperbarui!");
            document.getElementById('pass-old').value = '';
            document.getElementById('pass-new').value = '';
            document.getElementById('pass-confirm').value = '';
        }

        // RESET ALL DATA LOGIC
        function triggerResetAllData() {
            const confirmKey = prompt("Ketik 'RESET' untuk mengonfirmasi reset seluruh data aplikasi ke kondisi awal:");
            if (confirmKey === 'RESET') {
                localStorage.clear();
                storeConfig = DEFAULT_STORE;
                users = DEFAULT_USERS;
                products = DEFAULT_PRODUCTS;
                transactions = [];
                closings = [];
                currentUser = DEFAULT_USERS[0];

                saveData();
                location.reload();
            } else if (confirmKey !== null) {
                alert("Kata kunci konfirmasi salah. Reset dibatalkan.");
            }
        }
    </script>
</body>
</html>
