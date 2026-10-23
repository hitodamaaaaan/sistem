<!DOCTYPE html>
<html lang="ja" class="h-full">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>TechFeed Complete - ITニュース収集・トレンド分析</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <script>
        tailwind.config = {
            darkMode: 'class',
            theme: {
                extend: {
                    colors: {
                        brand: {
                            50: '#f0f5ff',
                            100: '#e0ebff',
                            500: '#3b82f6',
                            600: '#2563eb',
                            700: '#1d4ed8',
                            900: '#1e3a8a',
                        }
                    }
                }
            }
        }
    </script>
    <!-- Lucide Icons -->
    <script src="https://unpkg.com/lucide@latest"></script>
    <!-- Chart.js -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        /* Custom scrollbar styles */
        ::-webkit-scrollbar {
            width: 6px;
            height: 6px;
        }
        ::-webkit-scrollbar-track {
            background: rgba(0, 0, 0, 0.05);
        }
        .dark ::-webkit-scrollbar-track {
            background: rgba(255, 255, 255, 0.05);
        }
        ::-webkit-scrollbar-thumb {
            background: rgba(0, 0, 0, 0.2);
            border-radius: 9999px;
        }
        .dark ::-webkit-scrollbar-thumb {
            background: rgba(255, 255, 255, 0.2);
        }
        ::-webkit-scrollbar-thumb:hover {
            background: rgba(0, 0, 0, 0.3);
        }
        .dark ::-webkit-scrollbar-thumb:hover {
            background: rgba(255, 255, 255, 0.3);
        }
    </style>
</head>
<body class="h-screen overflow-hidden bg-slate-100 dark:bg-slate-950 text-slate-900 dark:text-slate-100 font-sans antialiased selection:bg-brand-500 selection:text-white transition-colors duration-200">

    <!-- App Container -->
    <div id="app" class="flex h-full w-full overflow-hidden">
        
        <!-- ========================================== -->
        <!-- LEFT SIDEBAR                               -->
        <!-- ========================================== -->
        <aside class="w-64 md:w-72 bg-white dark:bg-slate-900 border-r border-slate-200 dark:border-slate-800 flex flex-col shrink-0 z-20 transition-all duration-300">
            <!-- Brand Header -->
            <div class="p-4 border-b border-slate-200 dark:border-slate-800 flex items-center justify-between">
                <div class="flex items-center space-x-3">
                    <div class="w-10 h-10 rounded-xl bg-gradient-to-tr from-brand-600 to-indigo-600 flex items-center justify-center text-white shadow-md shadow-brand-500/20">
                        <i data-lucide="cpu" class="w-6 h-6"></i>
                    </div>
                    <div>
                        <h1 class="font-extrabold text-lg leading-none text-slate-900 dark:text-white tracking-tight">TechFeed</h1>
                        <span class="text-[10px] font-bold text-brand-600 dark:text-brand-400 tracking-wider uppercase">Complete v1.4</span>
                    </div>
                </div>
                <button id="themeToggleBtn" onclick="toggleDarkMode()" class="p-2 rounded-lg text-slate-500 hover:bg-slate-100 dark:hover:bg-slate-800 dark:text-slate-400 transition-colors" title="テーマ切り替え">
                    <i data-lucide="sun" class="w-5 h-5 hidden dark:block"></i>
                    <i data-lucide="moon" class="w-5 h-5 block dark:hidden"></i>
                </button>
            </div>

            <!-- Region Switcher -->
            <div class="px-4 pt-4 pb-2">
                <div class="bg-slate-100 dark:bg-slate-800 p-1 rounded-xl flex text-xs font-semibold">
                    <button id="regionJpBtn" onclick="setRegion('jp')" class="flex-1 py-1.5 rounded-lg flex items-center justify-center space-x-1.5 transition-all bg-white dark:bg-slate-700 shadow-sm text-slate-900 dark:text-white">
                        <span>🇯🇵</span>
                        <span>日本 (JP)</span>
                    </button>
                    <button id="regionGlobalBtn" onclick="setRegion('global')" class="flex-1 py-1.5 rounded-lg flex items-center justify-center space-x-1.5 transition-all text-slate-600 dark:text-slate-400 hover:text-slate-900 dark:hover:text-white">
                        <span>🌐</span>
                        <span>Global</span>
                    </button>
                </div>
            </div>

            <!-- Scrollable Navigation Area -->
            <div class="flex-1 overflow-y-auto px-3 py-2 space-y-6">
                <!-- Primary Navigation -->
                <div>
                    <p class="px-3 text-[11px] font-bold uppercase tracking-wider text-slate-400 dark:text-slate-500 mb-2">ビュー</p>
                    <nav class="space-y-1">
                        <button onclick="setViewFilter('all')" id="nav-all" class="w-full flex items-center justify-between px-3 py-2 text-sm font-medium rounded-xl bg-brand-50 dark:bg-brand-900/30 text-brand-700 dark:text-brand-400 transition-colors">
                            <span class="flex items-center space-x-2.5">
                                <i data-lucide="newspaper" class="w-4 h-4"></i>
                                <span>すべての記事</span>
                            </span>
                            <span id="badge-all-count" class="text-xs bg-brand-100 dark:bg-brand-800 text-brand-800 dark:text-brand-200 px-2 py-0.5 rounded-full font-bold">0</span>
                        </button>

                        <button onclick="setViewFilter('bookmarks')" id="nav-bookmarks" class="w-full flex items-center justify-between px-3 py-2 text-sm font-medium rounded-xl text-slate-700 dark:text-slate-300 hover:bg-slate-100 dark:hover:bg-slate-800 transition-colors">
                            <span class="flex items-center space-x-2.5">
                                <i data-lucide="bookmark" class="w-4 h-4"></i>
                                <span>ブックマーク</span>
                            </span>
                            <span id="badge-bookmark-count" class="text-xs bg-slate-200 dark:bg-slate-700 text-slate-700 dark:text-slate-300 px-2 py-0.5 rounded-full font-bold">0</span>
                        </button>

                        <button onclick="setViewFilter('unread')" id="nav-unread" class="w-full flex items-center justify-between px-3 py-2 text-sm font-medium rounded-xl text-slate-700 dark:text-slate-300 hover:bg-slate-100 dark:hover:bg-slate-800 transition-colors">
                            <span class="flex items-center space-x-2.5">
                                <i data-lucide="circle" class="w-4 h-4 text-emerald-500 fill-emerald-500"></i>
                                <span>未読のみ</span>
                            </span>
                            <span id="badge-unread-count" class="text-xs bg-slate-200 dark:bg-slate-700 text-slate-700 dark:text-slate-300 px-2 py-0.5 rounded-full font-bold">0</span>
                        </button>

                        <button onclick="setViewFilter('analytics')" id="nav-analytics" class="w-full flex items-center justify-between px-3 py-2 text-sm font-medium rounded-xl text-slate-700 dark:text-slate-300 hover:bg-slate-100 dark:hover:bg-slate-800 transition-colors">
                            <span class="flex items-center space-x-2.5">
                                <i data-lucide="trending-up" class="w-4 h-4 text-indigo-500"></i>
                                <span>ITトレンド分析</span>
                            </span>
                            <span class="text-[10px] font-bold bg-indigo-100 dark:bg-indigo-900/50 text-indigo-600 dark:text-indigo-400 px-2 py-0.5 rounded-full">Chart</span>
                        </button>
                    </nav>
                </div>

                <!-- Categories -->
                <div>
                    <p class="px-3 text-[11px] font-bold uppercase tracking-wider text-slate-400 dark:text-slate-500 mb-2">カテゴリ</p>
                    <nav class="space-y-1" id="categoryNav">
                        <!-- Dynamic Categories -->
                    </nav>
                </div>

                <!-- System Info Box -->
                <div class="p-3 bg-slate-50 dark:bg-slate-800/60 rounded-xl border border-slate-200 dark:border-slate-800 text-xs text-slate-500 dark:text-slate-400 space-y-1.5">
                    <div class="flex items-center font-bold text-slate-700 dark:text-slate-300 space-x-1.5">
                        <i data-lucide="shield-check" class="w-4 h-4 text-emerald-500"></i>
                        <span>コンプライアンス準拠</span>
                    </div>
                    <p class="text-[11px] leading-tight">GNews APIメタデータ制限準拠。AI推論はローカル処理のみ（学習利用なし）。</p>
                </div>
            </div>

            <!-- User Auth Footer -->
            <div class="p-3 border-t border-slate-200 dark:border-slate-800 bg-slate-50 dark:bg-slate-900/50">
                <div id="userInfoBox" class="flex items-center justify-between">
                    <div class="flex items-center space-x-2.5 min-w-0">
                        <div id="userAvatar" class="w-9 h-9 rounded-full bg-slate-200 dark:bg-slate-700 flex items-center justify-center font-bold text-sm text-slate-700 dark:text-slate-200 shrink-0">
                            U
                        </div>
                        <div class="min-w-0">
                            <p id="userName" class="text-sm font-semibold truncate text-slate-800 dark:text-slate-200">ゲストユーザー</p>
                            <p id="userRole" class="text-[11px] text-slate-500 dark:text-slate-400 truncate">未ログイン</p>
                        </div>
                    </div>
                    <button onclick="openAuthModal()" class="p-2 text-slate-500 hover:text-brand-600 dark:hover:text-brand-400 rounded-lg hover:bg-white dark:hover:bg-slate-800 transition-colors" title="ログイン / 設定">
                        <i data-lucide="log-in" id="authIcon" class="w-5 h-5"></i>
                    </button>
                </div>
            </div>
        </aside>

        <!-- ========================================== -->
        <!-- MAIN CONTENT AREA                          -->
        <!-- ========================================== -->
        <main class="flex-1 flex flex-col min-w-0 h-full overflow-hidden bg-slate-100 dark:bg-slate-950">
            
            <!-- Top Controls Header -->
            <header class="bg-white dark:bg-slate-900 border-b border-slate-200 dark:border-slate-800 px-6 py-4 shrink-0 shadow-sm">
                <div class="flex flex-col md:flex-row md:items-center md:justify-between gap-4">
                    
                    <!-- Search Bar -->
                    <div class="relative flex-1 max-w-xl">
                        <i data-lucide="search" class="w-5 h-5 absolute left-3.5 top-1/2 -translate-y-1/2 text-slate-400"></i>
                        <input type="text" id="searchInput" oninput="handleSearch()" placeholder="タイトル・概要・キーワードから検索..." class="w-full pl-10 pr-10 py-2 bg-slate-100 dark:bg-slate-800 text-slate-900 dark:text-slate-100 text-sm rounded-xl border border-transparent focus:border-brand-500 focus:bg-white dark:focus:bg-slate-900 focus:outline-none transition-all placeholder-slate-400">
                        <button id="clearSearchBtn" onclick="clearSearch()" class="hidden absolute right-3 top-1/2 -translate-y-1/2 text-slate-400 hover:text-slate-600 dark:hover:text-slate-200">
                            <i data-lucide="x" class="w-4 h-4"></i>
                        </button>
                    </div>

                    <!-- Filters & Controls -->
                    <div class="flex items-center space-x-3 overflow-x-auto pb-1 md:pb-0">
                        <!-- Date Filter -->
                        <div class="flex items-center space-x-1 bg-slate-100 dark:bg-slate-800 p-1 rounded-xl text-xs font-medium">
                            <button onclick="setDateFilter('all')" id="date-all" class="px-2.5 py-1 rounded-lg transition-all bg-white dark:bg-slate-700 shadow-sm text-slate-900 dark:text-white">全期間</button>
                            <button onclick="setDateFilter('24h')" id="date-24h" class="px-2.5 py-1 rounded-lg transition-all text-slate-600 dark:text-slate-400 hover:text-slate-900 dark:hover:text-white">24時間</button>
                            <button onclick="setDateFilter('7d')" id="date-7d" class="px-2.5 py-1 rounded-lg transition-all text-slate-600 dark:text-slate-400 hover:text-slate-900 dark:hover:text-white">7日間</button>
                            <button onclick="setDateFilter('30d')" id="date-30d" class="px-2.5 py-1 rounded-lg transition-all text-slate-600 dark:text-slate-400 hover:text-slate-900 dark:hover:text-white">30日</button>
                        </div>

                        <!-- Sync Feed Button -->
                        <button onclick="refreshArticlesFromAPI()" id="syncBtn" class="flex items-center space-x-1.5 px-3 py-2 bg-brand-600 hover:bg-brand-700 text-white text-xs font-semibold rounded-xl transition-all active:scale-95 shrink-0 shadow-sm" title="最新フィードを取得">
                            <i data-lucide="refresh-cw" id="syncIcon" class="w-4 h-4"></i>
                            <span class="hidden sm:inline">同期</span>
                        </button>
                    </div>
                </div>
            </header>

            <!-- Scrollable View Content -->
            <div class="flex-1 overflow-y-auto p-6" id="mainScrollArea">
                
                <!-- View Header Banner -->
                <div class="mb-6 flex items-center justify-between">
                    <div>
                        <h2 id="viewTitle" class="text-xl font-bold text-slate-900 dark:text-white flex items-center space-x-2">
                            <span>すべての記事</span>
                        </h2>
                        <p id="viewDescription" class="text-xs text-slate-500 dark:text-slate-400 mt-1">収集された最新ITニュース・技術メタデータ一覧</p>
                    </div>
                    <span id="resultCountBadge" class="text-xs font-semibold px-3 py-1 bg-slate-200 dark:bg-slate-800 text-slate-700 dark:text-slate-300 rounded-full">
                        0 件を表示中
                    </span>
                </div>

                <!-- FEED VIEW CONTAINER -->
                <div id="feedView" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-2 xl:grid-cols-3 gap-5">
                    <!-- Dynamic Article Cards Generated Here -->
                </div>

                <!-- ANALYTICS / TREND VIEW CONTAINER (Hidden by default) -->
                <div id="analyticsView" class="hidden space-y-6">
                    <!-- Analytics Summary Cards -->
                    <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4">
                        <div class="p-4 bg-white dark:bg-slate-900 rounded-2xl border border-slate-200 dark:border-slate-800 shadow-sm">
                            <div class="flex items-center justify-between text-slate-500 dark:text-slate-400 text-xs font-bold uppercase">
                                <span>今週の最頻出トピック</span>
                                <i data-lucide="sparkles" class="w-4 h-4 text-amber-500"></i>
                            </div>
                            <p class="text-2xl font-black text-slate-900 dark:text-white mt-2">AI / LLM</p>
                            <span class="text-xs font-medium text-emerald-600 dark:text-emerald-400 flex items-center mt-1">
                                <i data-lucide="arrow-up-right" class="w-3.5 h-3.5 mr-0.5"></i> +42% 先週比増加
                            </span>
                        </div>

                        <div class="p-4 bg-white dark:bg-slate-900 rounded-2xl border border-slate-200 dark:border-slate-800 shadow-sm">
                            <div class="flex items-center justify-between text-slate-500 dark:text-slate-400 text-xs font-bold uppercase">
                                <span>総蓄積記事数</span>
                                <i data-lucide="database" class="w-4 h-4 text-brand-500"></i>
                            </div>
                            <p id="analyticsTotalCount" class="text-2xl font-black text-slate-900 dark:text-white mt-2">1,240</p>
                            <span class="text-xs text-slate-500 dark:text-slate-400 mt-1 block">GNews API キャッシュ経由</span>
                        </div>

                        <div class="p-4 bg-white dark:bg-slate-900 rounded-2xl border border-slate-200 dark:border-slate-800 shadow-sm">
                            <div class="flex items-center justify-between text-slate-500 dark:text-slate-400 text-xs font-bold uppercase">
                                <span>急上昇フレームワーク</span>
                                <i data-lucide="zap" class="w-4 h-4 text-indigo-500"></i>
                            </div>
                            <p class="text-2xl font-black text-slate-900 dark:text-white mt-2">Rust / WebAssembly</p>
                            <span class="text-xs text-emerald-600 dark:text-emerald-400 font-medium mt-1 block">+28% 言及度UP</span>
                        </div>

                        <div class="p-4 bg-white dark:bg-slate-900 rounded-2xl border border-slate-200 dark:border-slate-800 shadow-sm">
                            <div class="flex items-center justify-between text-slate-500 dark:text-slate-400 text-xs font-bold uppercase">
                                <span>マイブックマーク数</span>
                                <i data-lucide="star" class="w-4 h-4 text-amber-400 fill-amber-400"></i>
                            </div>
                            <p id="analyticsBookmarkCount" class="text-2xl font-black text-slate-900 dark:text-white mt-2">0</p>
                            <span class="text-xs text-slate-500 dark:text-slate-400 mt-1 block">ローカル同期済み</span>
                        </div>
                    </div>

                    <!-- Main Chart Canvas Container -->
                    <div class="bg-white dark:bg-slate-900 p-6 rounded-2xl border border-slate-200 dark:border-slate-800 shadow-sm">
                        <div class="flex items-center justify-between mb-4">
                            <div>
                                <h3 class="font-bold text-lg text-slate-900 dark:text-white">主要技術スタックの言及数トレンド推移 (日次バッチ集計)</h3>
                                <p class="text-xs text-slate-500 dark:text-slate-400">GNews APIから取得したメタデータ内での出現回数をサーバー側で独自集計</p>
                            </div>
                            <div class="flex items-center space-x-2 text-xs">
                                <span class="flex items-center text-slate-600 dark:text-slate-400"><span class="w-3 h-3 rounded-full bg-blue-500 mr-1.5 inline-block"></span> AI / LLM</span>
                                <span class="flex items-center text-slate-600 dark:text-slate-400"><span class="w-3 h-3 rounded-full bg-cyan-500 mr-1.5 inline-block"></span> React</span>
                                <span class="flex items-center text-slate-600 dark:text-slate-400"><span class="w-3 h-3 rounded-full bg-amber-500 mr-1.5 inline-block"></span> Rust</span>
                                <span class="flex items-center text-slate-600 dark:text-slate-400"><span class="w-3 h-3 rounded-full bg-purple-500 mr-1.5 inline-block"></span> Kubernetes</span>
                            </div>
                        </div>
                        <div class="h-80 w-full relative">
                            <canvas id="trendChartCanvas"></canvas>
                        </div>
                    </div>
                </div>

                <!-- Empty State -->
                <div id="emptyState" class="hidden py-16 text-center space-y-3">
                    <div class="w-16 h-16 mx-auto rounded-full bg-slate-200 dark:bg-slate-800 flex items-center justify-center text-slate-400">
                        <i data-lucide="inbox" class="w-8 h-8"></i>
                    </div>
                    <h3 class="text-base font-bold text-slate-700 dark:text-slate-300">条件に該当する記事がありません</h3>
                    <p class="text-xs text-slate-500 max-w-sm mx-auto">検索キーワードを変更するか、フィルター条件をリセットしてみてください。</p>
                    <button onclick="resetFilters()" class="mt-2 px-4 py-2 bg-brand-600 text-white text-xs font-semibold rounded-xl hover:bg-brand-700 transition-colors">
                        フィルターをクリア
                    </button>
                </div>

            </div>
        </main>

        <!-- ========================================== -->
        <!-- RIGHT SLIDE PANEL (ARTICLE DETAIL & AI)   -->
        <!-- ========================================== -->
        <div id="detailBackdrop" onclick="closeDetailPanel()" class="fixed inset-0 bg-slate-900/40 backdrop-blur-sm z-30 opacity-0 pointer-events-none transition-opacity duration-300"></div>
        
        <aside id="detailPanel" class="fixed top-0 right-0 bottom-0 w-full max-w-lg bg-white dark:bg-slate-900 border-l border-slate-200 dark:border-slate-800 z-40 translate-x-full transition-transform duration-300 ease-in-out flex flex-col shadow-2xl">
            <!-- Detail Header -->
            <div class="p-4 border-b border-slate-200 dark:border-slate-800 flex items-center justify-between shrink-0 bg-slate-50/50 dark:bg-slate-900/50">
                <div class="flex items-center space-x-2">
                    <span id="detailCategoryBadge" class="text-xs font-bold px-2.5 py-1 bg-brand-100 dark:bg-brand-900/60 text-brand-700 dark:text-brand-300 rounded-lg">Category</span>
                    <span id="detailRegionBadge" class="text-xs font-semibold px-2 py-0.5 bg-slate-200 dark:bg-slate-800 text-slate-600 dark:text-slate-400 rounded-md">JP</span>
                </div>
                <div class="flex items-center space-x-1">
                    <button id="detailBookmarkBtn" onclick="toggleBookmarkFromDetail()" class="p-2 text-slate-400 hover:text-amber-400 rounded-lg transition-colors">
                        <i data-lucide="star" class="w-5 h-5"></i>
                    </button>
                    <button onclick="closeDetailPanel()" class="p-2 text-slate-400 hover:text-slate-600 dark:hover:text-slate-200 rounded-lg transition-colors">
                        <i data-lucide="x" class="w-5 h-5"></i>
                    </button>
                </div>
            </div>

            <!-- Detail Body -->
            <div class="flex-1 overflow-y-auto p-6 space-y-6">
                
                <!-- Article Title & Meta -->
                <div>
                    <h2 id="detailTitle" class="text-xl font-bold text-slate-900 dark:text-white leading-snug">記事タイトル</h2>
                    <div class="mt-3 flex items-center space-x-3 text-xs text-slate-500 dark:text-slate-400">
                        <span id="detailSource" class="font-semibold text-slate-700 dark:text-slate-300">情報源</span>
                        <span>•</span>
                        <span id="detailDate">日時</span>
                    </div>
                </div>

                <!-- Raw GNews Description Snippet -->
                <div class="bg-slate-50 dark:bg-slate-800/50 p-4 rounded-xl border border-slate-200 dark:border-slate-800 text-sm leading-relaxed text-slate-700 dark:text-slate-300">
                    <p class="text-xs font-bold text-slate-400 uppercase tracking-wider mb-1">GNews 取得概要メタデータ</p>
                    <p id="detailDescription">記事の概要テキストがここに表示されます。</p>
                </div>

                <!-- Local AI Inference Panel -->
                <div class="bg-gradient-to-br from-indigo-50/80 to-purple-50/80 dark:from-indigo-950/30 dark:to-purple-950/30 p-5 rounded-2xl border border-indigo-200/80 dark:border-indigo-800/50 space-y-4">
                    <div class="flex items-center justify-between">
                        <div class="flex items-center space-x-2">
                            <i data-lucide="sparkles" class="w-5 h-5 text-indigo-600 dark:text-indigo-400"></i>
                            <h3 class="font-bold text-slate-900 dark:text-white text-sm">ローカルAI要約 & 翻訳推論</h3>
                        </div>
                        <span class="text-[10px] bg-indigo-200 dark:bg-indigo-900/80 text-indigo-800 dark:text-indigo-200 px-2 py-0.5 rounded-full font-semibold">Tauri Local AI</span>
                    </div>

                    <!-- AI Controls -->
                    <div class="flex items-center space-x-2">
                        <button onclick="generateAiSummary()" id="aiSummaryBtn" class="flex-1 py-2 bg-indigo-600 hover:bg-indigo-700 text-white text-xs font-semibold rounded-xl flex items-center justify-center space-x-1.5 transition-all shadow-sm">
                            <i data-lucide="wand-2" class="w-3.5 h-3.5"></i>
                            <span>AI要約を生成</span>
                        </button>
                        <button onclick="toggleAiTranslation()" id="aiTranslateBtn" class="px-3 py-2 bg-white dark:bg-slate-800 border border-slate-200 dark:border-slate-700 hover:bg-slate-50 dark:hover:bg-slate-700 text-slate-700 dark:text-slate-200 text-xs font-semibold rounded-xl flex items-center space-x-1 transition-all">
                            <i data-lucide="languages" class="w-3.5 h-3.5"></i>
                            <span id="transLangLabel">英語に翻訳</span>
                        </button>
                    </div>

                    <!-- AI Output Result Container -->
                    <div id="aiResultBox" class="hidden bg-white/80 dark:bg-slate-900/80 backdrop-blur p-4 rounded-xl border border-indigo-100 dark:border-indigo-900/50 text-xs space-y-2">
                        <div class="flex items-center justify-between text-indigo-600 dark:text-indigo-400 font-bold">
                            <span>要点ピックアップ (3ポイント)</span>
                            <span class="text-[10px] text-slate-400 font-normal">※学習なしリアルタイム推論</span>
                        </div>
                        <ul id="aiSummaryPoints" class="list-disc list-inside space-y-1 text-slate-700 dark:text-slate-300 leading-relaxed">
                            <!-- Bullet points injected here -->
                        </ul>
                    </div>
                </div>

                <!-- My Personal Memo Section (Auto-saved) -->
                <div class="space-y-2">
                    <div class="flex items-center justify-between">
                        <label for="myMemoArea" class="text-xs font-bold text-slate-700 dark:text-slate-300 flex items-center space-x-1.5">
                            <i data-lucide="edit-3" class="w-4 h-4 text-brand-600"></i>
                            <span>マイメモ (専用同期メモ)</span>
                        </label>
                        <span id="memoSavedBadge" class="text-[10px] text-emerald-600 dark:text-emerald-400 font-semibold opacity-0 transition-opacity">
                            ✓ 自動保存済み
                        </span>
                    </div>
                    <textarea id="myMemoArea" oninput="handleMemoInput()" placeholder="この記事に関する思考、備忘録、関連プロジェクトへの影響などを入力..." class="w-full h-28 p-3 text-xs bg-slate-50 dark:bg-slate-800/80 border border-slate-200 dark:border-slate-700 rounded-xl text-slate-900 dark:text-slate-100 focus:outline-none focus:border-brand-500 resize-none transition-colors"></textarea>
                </div>

                <!-- External Source Link Button -->
                <div class="pt-2">
                    <a id="detailExternalUrl" href="#" target="_blank" rel="noopener noreferrer" class="w-full py-3 bg-slate-900 hover:bg-slate-800 dark:bg-slate-100 dark:hover:bg-white dark:text-slate-900 text-white text-xs font-bold rounded-xl flex items-center justify-center space-x-2 transition-colors shadow-sm">
                        <span>元記事を読む (外部サイトへ移動)</span>
                        <i data-lucide="external-link" class="w-4 h-4"></i>
                    </a>
                    <p class="text-[10px] text-slate-400 text-center mt-2">著作権保護のため、本文全文は発行元ウェブサイトにてご確認ください。</p>
                </div>

            </div>
        </aside>

        <!-- ========================================== -->
        <!-- MOCK AUTH & LOGIN MODAL                    -->
        <!-- ========================================== -->
        <div id="authModal" class="fixed inset-0 bg-slate-900/50 backdrop-blur-sm z-50 flex items-center justify-center p-4 opacity-0 pointer-events-none transition-opacity duration-200">
            <div class="bg-white dark:bg-slate-900 rounded-2xl border border-slate-200 dark:border-slate-800 w-full max-w-md p-6 shadow-2xl relative space-y-5">
                <button onclick="closeAuthModal()" class="absolute top-4 right-4 text-slate-400 hover:text-slate-600 dark:hover:text-slate-200">
                    <i data-lucide="x" class="w-5 h-5"></i>
                </button>

                <div class="text-center">
                    <div class="w-12 h-12 bg-brand-100 dark:bg-brand-900/50 text-brand-600 dark:text-brand-400 rounded-2xl flex items-center justify-center mx-auto mb-3">
                        <i data-lucide="lock" class="w-6 h-6"></i>
                    </div>
                    <h3 class="text-lg font-bold text-slate-900 dark:text-white">TechFeed アカウント認証</h3>
                    <p class="text-xs text-slate-500 dark:text-slate-400 mt-1">ブックマーク・メモをサーバー(MariaDB)へ同期</p>
                </div>

                <form onsubmit="handleAuthSubmit(event)" class="space-y-4">
                    <div>
                        <label class="block text-xs font-bold text-slate-700 dark:text-slate-300 mb-1">ユーザーID / メールアドレス</label>
                        <input type="text" id="authUserId" required placeholder="例: dev_user01" class="w-full px-3.5 py-2 bg-slate-100 dark:bg-slate-800 border border-transparent focus:border-brand-500 rounded-xl text-sm outline-none text-slate-900 dark:text-slate-100">
                    </div>
                    <button type="submit" class="w-full py-2.5 bg-brand-600 hover:bg-brand-700 text-white font-bold text-xs rounded-xl shadow-sm transition-all">
                        ログインして同期開始
                    </button>
                </form>

                <div id="logoutSection" class="hidden border-t border-slate-200 dark:border-slate-800 pt-4 text-center">
                    <button onclick="handleLogout()" class="text-xs font-bold text-red-500 hover:text-red-600">
                        ログアウト (ローカルデータ消去)
                    </button>
                </div>
            </div>
        </div>

    </div>

    <!-- ========================================== -->
    <!-- APPLICATION JAVASCRIPT LOGIC               -->
    <!-- ========================================== -->
    <script>
        // --- SAMPLE DATASETS matching Schema ---
        const SAMPLE_ARTICLES = [
            {
                id: "art-jp-001",
                region: "jp",
                category: "AI/LLM",
                title: "ローカルLLM開発フレームワークの新バージョンが公開、軽量化と高速化を実現",
                description: "TauriおよびRustベースのネイティブクライアントで動作可能な軽量AIモデルの最適化ライブラリがリリースされました。GPUメモリ消費量を従来比30%削減。",
                source: "TechNews Japan",
                url: "https://example.com/news/art-jp-001",
                date: "2026-10-23T09:30:00Z"
            },
            {
                id: "art-jp-002",
                region: "jp",
                category: "Frontend",
                title: "React 19の新機能とパフォーマンス最適化手法の解説まとめ",
                description: "React 19で正式導入されたServer Actionsおよび自動メモ化コンパイラの仕組みについて、実践的なコード例とともにプロダクション適用事例を紹介します。",
                source: "WebDev Weekly JP",
                url: "https://example.com/news/art-jp-002",
                date: "2026-10-22T14:15:00Z"
            },
            {
                id: "art-jp-003",
                region: "jp",
                category: "Cloud/DevOps",
                title: "Kubernetes 1.32リリース：マルチクラウド運用の省力化と自動スケール強化",
                description: "最新のKubernetesリリースでは、エッジノードの自動リカバリ機能およびAIワークロードに最適化したDynamic Resource Allocation機能が大幅更新されました。",
                source: "Cloud Native Watch",
                url: "https://example.com/news/art-jp-003",
                date: "2026-10-21T18:00:00Z"
            },
            {
                id: "art-jp-004",
                region: "jp",
                category: "Security",
                title: "サプライチェーン攻撃を防ぐSBOM（ソフトウェア部品表）運用の自動化ベストプラクティス",
                description: "オープンソース依存関係の脆弱性をGitHub Actions等で自動検出し、CISA規約に適合したSBOM出力を実現するCI/CDパイプライン構築手順。",
                source: "CyberSec Times",
                url: "https://example.com/news/art-jp-004",
                date: "2026-10-20T11:40:00Z"
            },
            {
                id: "art-global-001",
                region: "global",
                category: "AI/LLM",
                title: "Next-Gen AI Models Achieve Breakthrough in Local Context Reasoning",
                description: "New lightweight LLMs enable multi-modal processing directly on end-user desktop machines without sending sensitive user data to external cloud servers.",
                source: "Global Tech Insider",
                url: "https://example.com/news/art-global-001",
                date: "2026-10-23T08:00:00Z"
            },
            {
                id: "art-global-002",
                region: "global",
                category: "Backend",
                title: "Rust Ecosystem Gains Momentum in High-Throughput Financial Systems",
                description: "Major financial institutions report a 40% latency drop after migrating core microservices from C++ and Java to Rust and Axum web frameworks.",
                source: "Systems Architecture Today",
                url: "https://example.com/news/art-global-002",
                date: "2026-10-22T16:20:00Z"
            },
            {
                id: "art-global-003",
                region: "global",
                category: "Mobile",
                title: "Cross-Platform Mobile Development Trends for 2027",
                description: "Comparison of performance metrics between Flutter, React Native, and WebAssembly-driven mobile native wrappers in heavy graphics scenarios.",
                source: "Mobile Dev Review",
                url: "https://example.com/news/art-global-003",
                date: "2026-10-19T10:00:00Z"
            }
        ];

        const CATEGORIES = ["すべて", "AI/LLM", "Frontend", "Backend", "Cloud/DevOps", "Security", "Mobile"];

        // --- APP STATE ---
        let state = {
            currentRegion: 'jp',
            currentView: 'all',
            selectedCategory: 'すべて',
            dateFilter: 'all',
            searchQuery: '',
            
            user: {
                isLoggedIn: false,
                userId: 'ゲストユーザー'
            },
            bookmarks: new Set(),
            readArticles: new Set(),
            memos: {},
            
            activeArticleId: null,
            aiSummaryActive: false,
            aiTranslated: false
        };

        let memoSaveTimeout = null;
        let trendChartInstance = null;

        // --- FILTERING LOGIC ---
        function getFilteredArticles() {
            return SAMPLE_ARTICLES.filter(art => {
                if (art.region !== state.currentRegion) return false;
                if (state.selectedCategory !== 'すべて' && art.category !== state.selectedCategory) return false;
                if (state.currentView === 'bookmarks' && !state.bookmarks.has(art.id)) return false;
                if (state.currentView === 'unread' && state.readArticles.has(art.id)) return false;

                if (state.dateFilter !== 'all') {
                    const artDate = new Date(art.date).getTime();
                    const now = Date.now();
                    const hoursDiff = (now - artDate) / (1000 * 60 * 60);

                    if (state.dateFilter === '24h' && hoursDiff > 24) return false;
                    if (state.dateFilter === '7d' && hoursDiff > 24 * 7) return false;
                    if (state.dateFilter === '30d' && hoursDiff > 24 * 30) return false;
                }

                if (state.searchQuery) {
                    const q = state.searchQuery.toLowerCase();
                    const titleMatch = art.title.toLowerCase().includes(q);
                    const descMatch = art.description.toLowerCase().includes(q);
                    const sourceMatch = art.source.toLowerCase().includes(q);
                    const catMatch = art.category.toLowerCase().includes(q);
                    if (!titleMatch && !descMatch && !sourceMatch && !catMatch) return false;
                }

                return true;
            });
        }

        // --- RENDER CATEGORY NAVIGATION ---
        function renderCategoryNav() {
            const nav = document.getElementById('categoryNav');
            if (!nav) return;

            const regionArticles = SAMPLE_ARTICLES.filter(a => a.region === state.currentRegion);

            nav.innerHTML = CATEGORIES.map(cat => {
                const count = cat === 'すべて' 
                    ? regionArticles.length 
                    : regionArticles.filter(a => a.category === cat).length;
                const isActive = state.selectedCategory === cat;

                return `
                    <button onclick="setCategory('${cat}')" class="w-full flex items-center justify-between px-3 py-1.5 text-xs font-medium rounded-lg transition-colors ${isActive ? 'bg-slate-200 dark:bg-slate-800 text-brand-600 dark:text-brand-400 font-bold' : 'text-slate-600 dark:text-slate-400 hover:bg-slate-100 dark:hover:bg-slate-800/60'}">
                        <span>${cat}</span>
                        <span class="text-[10px] px-1.5 py-0.2 rounded-full ${isActive ? 'bg-brand-500 text-white' : 'bg-slate-100 dark:bg-slate-800 text-slate-500'}">${count}</span>
                    </button>
                `;
            }).join('');
        }

        // --- LOCAL STORAGE STATE PERSISTENCE ---
        function saveLocalState() {
            try {
                const payload = {
                    bookmarks: Array.from(state.bookmarks),
                    readArticles: Array.from(state.readArticles),
                    memos: state.memos,
                    user: state.user
                };
                localStorage.setItem('techfeed_state_v1', JSON.stringify(payload));
            } catch (e) {
                console.warn('LocalStorage save failed:', e);
            }
        }

        function loadLocalState() {
            try {
                const data = localStorage.getItem('techfeed_state_v1');
                if (data) {
                    const parsed = JSON.parse(data);
                    if (parsed.bookmarks) state.bookmarks = new Set(parsed.bookmarks);
                    if (parsed.readArticles) state.readArticles = new Set(parsed.readArticles);
                    if (parsed.memos) state.memos = parsed.memos;
                    if (parsed.user) state.user = parsed.user;
                }
            } catch (e) {
                console.warn('LocalStorage load failed:', e);
            }
        }

        // --- INITIALIZATION ---
        window.onload = function() {
            loadLocalState();
            renderCategoryNav();
            updateUI();
            initChart();
            fetchLiveNewsAPI();
        };

        // --- DYNAMIC API FETCHING LOGIC ---
        async function fetchLiveNewsAPI() {
            const syncIcon = document.getElementById('syncIcon');
            if (syncIcon) syncIcon.classList.add('animate-spin');

            try {
                const res = await fetch('https://dev.to/api/articles?per_page=6&top=7');
                if (!res.ok) throw new Error('API fetch failed');
                const rawData = await res.json();

                const apiArticles = rawData.map((item, idx) => {
                    const catMap = ['AI/LLM', 'Frontend', 'Backend', 'Cloud/DevOps', 'Security', 'Mobile'];
                    const category = catMap[idx % catMap.length];
                    const region = idx % 2 === 0 ? 'global' : 'jp';

                    const dynamicImage = item.cover_image || item.social_image || null;

                    return {
                        id: `api-${item.id}`,
                        region: region,
                        category: category,
                        title: item.title,
                        description: item.description || (item.title + " - 最新の技術動向とエンジニア向け解説。"),
                        source: item.organization?.name || item.user?.name || "Tech Feed Live",
                        url: item.url,
                        image: dynamicImage,
                        date: new Date(item.published_at || Date.now()).toISOString()
                    };
                });

                apiArticles.forEach(fetchedArt => {
                    if (!SAMPLE_ARTICLES.some(a => a.id === fetchedArt.id)) {
                        SAMPLE_ARTICLES.unshift(fetchedArt);
                    }
                });

                showToast('APIから最新ニュースを取得しました');
            } catch (err) {
                console.warn("API取得代替モード（サンプルデータ使用）:", err);
            } finally {
                if (syncIcon) syncIcon.classList.remove('animate-spin');
                updateUI();
            }
        }

        function refreshArticlesFromAPI() {
            fetchLiveNewsAPI();
        }

        function getSVGPlaceholder(category) {
            const encodedCat = encodeURIComponent(category || 'Tech');
            return `data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="600" height="300" viewBox="0 0 600 300"><rect width="100%" height="100%" fill="%231e293b"/><text x="50%" y="50%" dominant-baseline="middle" text-anchor="middle" font-family="sans-serif" font-size="24" font-weight="bold" fill="%236366f1">${encodedCat}</text></svg>`;
        }

        function showToast(msg) {
            const toast = document.createElement('div');
            toast.className = 'fixed bottom-5 right-5 z-50 bg-slate-900 text-white dark:bg-brand-600 text-xs px-4 py-3 rounded-xl shadow-lg border border-slate-700/50 flex items-center space-x-2 transition-all transform translate-y-2 opacity-0';
            toast.innerHTML = `<i data-lucide="check-circle" class="w-4 h-4 text-emerald-400"></i><span>${msg}</span>`;
            document.body.appendChild(toast);
            if (window.lucide) lucide.createIcons();
            
            setTimeout(() => {
                toast.classList.remove('translate-y-2', 'opacity-0');
            }, 50);

            setTimeout(() => {
                toast.classList.add('opacity-0', 'translate-y-2');
                setTimeout(() => toast.remove(), 300);
            }, 3000);
        }

        // --- RENDER MAIN UI ---
        function updateUI() {
            document.getElementById('userName').innerText = state.user.userId;
            document.getElementById('userRole').innerText = state.user.isLoggedIn ? 'MariaDB 同期中' : '未ログイン (ローカル保存)';
            document.getElementById('userAvatar').innerText = state.user.userId.charAt(0).toUpperCase();

            const regionArticles = SAMPLE_ARTICLES.filter(a => a.region === state.currentRegion);
            document.getElementById('badge-all-count').innerText = regionArticles.length;
            
            const bookmarkCount = regionArticles.filter(a => state.bookmarks.has(a.id)).length;
            document.getElementById('badge-bookmark-count').innerText = bookmarkCount;

            const unreadCount = regionArticles.filter(a => !state.readArticles.has(a.id)).length;
            document.getElementById('badge-unread-count').innerText = unreadCount;

            document.getElementById('analyticsBookmarkCount').innerText = state.bookmarks.size;

            const isJp = state.currentRegion === 'jp';
            document.getElementById('regionJpBtn').className = `flex-1 py-1.5 rounded-lg flex items-center justify-center space-x-1.5 transition-all ${isJp ? 'bg-white dark:bg-slate-700 shadow-sm text-slate-900 dark:text-white font-bold' : 'text-slate-600 dark:text-slate-400'}`;
            document.getElementById('regionGlobalBtn').className = `flex-1 py-1.5 rounded-lg flex items-center justify-center space-x-1.5 transition-all ${!isJp ? 'bg-white dark:bg-slate-700 shadow-sm text-slate-900 dark:text-white font-bold' : 'text-slate-600 dark:text-slate-400'}`;

            const titles = {
                'all': 'すべての記事',
                'bookmarks': 'ブックマーク一覧',
                'unread': '未読記事一覧',
                'analytics': 'ITトレンド分析ダッシュボード'
            };
            document.getElementById('viewTitle').innerHTML = `<span>${titles[state.currentView]}</span>`;

            const feedView = document.getElementById('feedView');
            const analyticsView = document.getElementById('analyticsView');
            const emptyState = document.getElementById('emptyState');

            if (state.currentView === 'analytics') {
                feedView.classList.add('hidden');
                analyticsView.classList.remove('hidden');
                emptyState.classList.add('hidden');
                document.getElementById('resultCountBadge').innerText = 'チャート表示中';
            } else {
                analyticsView.classList.add('hidden');
                feedView.classList.remove('hidden');

                const articles = getFilteredArticles();
                document.getElementById('resultCountBadge').innerText = `${articles.length} 件を表示中`;

                if (articles.length === 0) {
                    feedView.classList.add('hidden');
                    emptyState.classList.remove('hidden');
                } else {
                    emptyState.classList.add('hidden');
                    renderArticleCards(articles);
                }
            }

            renderCategoryNav();
            saveLocalState();
            if (window.lucide) lucide.createIcons();
        }

        // --- ARTICLE CARDS RENDER ---
        function renderArticleCards(articles) {
            const container = document.getElementById('feedView');

            container.innerHTML = articles.map(art => {
                const isBookmarked = state.bookmarks.has(art.id);
                const isRead = state.readArticles.has(art.id);
                const hasMemo = !!state.memos[art.id];
                const formattedDate = new Date(art.date).toLocaleDateString('ja-JP', { month: 'short', day: 'numeric', hour: '2-digit', minute: '2-digit' });
                const fallbackImg = getSVGPlaceholder(art.category);
                const displayImg = art.image || fallbackImg;

                // Title and Description escaped safely
                const safeTitle = art.title.replace(/"/g, '&quot;');

                return `
                    <div class="bg-white dark:bg-slate-900 rounded-2xl border border-slate-200 dark:border-slate-800 overflow-hidden shadow-sm hover:shadow-md transition-all flex flex-col justify-between group relative ${isRead ? 'opacity-80' : ''}">
                        
                        <!-- Article Thumbnail Image -->
                        <div onclick="openDetailPanel('${art.id}')" class="w-full h-44 overflow-hidden bg-slate-100 dark:bg-slate-800/80 cursor-pointer relative group">
                            <img src="${displayImg}" 
                                 onerror="this.onerror=null; this.src='${fallbackImg}';" 
                                 loading="lazy"
                                 alt="${safeTitle}" 
                                 class="w-full h-full object-cover group-hover:scale-105 transition-transform duration-500 ease-out">
                            <div class="absolute inset-0 bg-gradient-to-t from-slate-950/60 via-transparent to-transparent opacity-60 group-hover:opacity-40 transition-opacity"></div>
                            
                            <div class="absolute top-3 left-3 flex items-center space-x-1.5 z-10">
                                <span class="text-[10px] font-bold px-2.5 py-1 bg-slate-900/80 backdrop-blur text-white rounded-lg shadow-sm border border-white/10">
                                    ${art.category}
                                </span>
                                ${!isRead ? '<span class="w-2.5 h-2.5 rounded-full bg-emerald-500 ring-2 ring-white dark:ring-slate-900" title="未読"></span>' : ''}
                            </div>
                        </div>

                        <div class="p-5 flex-1 flex flex-col justify-between">
                            <div>
                                <!-- Header Info -->
                                <div class="flex items-center justify-between mb-2">
                                    <div class="flex items-center space-x-2">
                                        ${hasMemo ? '<span class="text-[10px] bg-amber-100 dark:bg-amber-900/40 text-amber-700 dark:text-amber-300 px-1.5 py-0.2 rounded font-semibold" title="メモあり">📝 メモ</span>' : ''}
                                    </div>
                                    <span class="text-[11px] text-slate-400 font-mono">${formattedDate}</span>
                                </div>

                                <!-- Title -->
                                <h3 onclick="openDetailPanel('${art.id}')" class="font-bold text-slate-900 dark:text-white text-base hover:text-brand-600 dark:hover:text-brand-400 cursor-pointer line-clamp-2 transition-colors leading-snug mb-2">
                                    ${art.title}
                                </h3>

                                <!-- Snippet -->
                                <p class="text-xs text-slate-600 dark:text-slate-400 line-clamp-2 leading-relaxed mb-4">
                                    ${art.description}
                                </p>
                            </div>

                            <!-- Footer actions -->
                            <div class="pt-3 border-t border-slate-100 dark:border-slate-800/80 flex items-center justify-between text-xs text-slate-500">
                                <span class="font-medium text-slate-600 dark:text-slate-400 truncate max-w-[130px]">${art.source}</span>
                                
                                <div class="flex items-center space-x-1">
                                    <button onclick="toggleBookmark('${art.id}', event)" class="p-1.5 text-slate-400 hover:text-amber-400 transition-colors" title="ブックマーク">
                                        <i data-lucide="star" class="w-4 h-4 ${isBookmarked ? 'text-amber-400 fill-amber-400' : ''}"></i>
                                    </button>
                                    <button onclick="openDetailPanel('${art.id}')" class="px-2.5 py-1 bg-slate-100 dark:bg-slate-800 hover:bg-brand-50 dark:hover:bg-brand-900/30 hover:text-brand-600 dark:hover:text-brand-400 rounded-lg text-xs font-semibold transition-colors flex items-center space-x-1">
                                        <span>詳細・AI</span>
                                        <i data-lucide="chevron-right" class="w-3 h-3"></i>
                                    </button>
                                </div>
                            </div>
                        </div>

                    </div>
                `;
            }).join('');
        }

        // --- ACTIONS ---
        function setRegion(r) {
            state.currentRegion = r;
            updateUI();
        }

        function setCategory(cat) {
            state.selectedCategory = cat;
            updateUI();
        }

        function setViewFilter(view) {
            state.currentView = view;
            ['all', 'bookmarks', 'unread', 'analytics'].forEach(v => {
                const btn = document.getElementById(`nav-${v}`);
                if (btn) {
                    if (v === view) {
                        btn.className = 'w-full flex items-center justify-between px-3 py-2 text-sm font-medium rounded-xl bg-brand-50 dark:bg-brand-900/30 text-brand-700 dark:text-brand-400 font-bold transition-colors';
                    } else {
                        btn.className = 'w-full flex items-center justify-between px-3 py-2 text-sm font-medium rounded-xl text-slate-700 dark:text-slate-300 hover:bg-slate-100 dark:hover:bg-slate-800 transition-colors';
                    }
                }
            });
            updateUI();
        }

        function setDateFilter(d) {
            state.dateFilter = d;
            ['all', '24h', '7d', '30d'].forEach(k => {
                const btn = document.getElementById(`date-${k}`);
                if (k === d) {
                    btn.className = 'px-2.5 py-1 rounded-lg transition-all bg-white dark:bg-slate-700 shadow-sm text-slate-900 dark:text-white font-bold';
                } else {
                    btn.className = 'px-2.5 py-1 rounded-lg transition-all text-slate-600 dark:text-slate-400 hover:text-slate-900 dark:hover:text-white';
                }
            });
            updateUI();
        }

        function handleSearch() {
            const input = document.getElementById('searchInput');
            state.searchQuery = input.value.trim();
            document.getElementById('clearSearchBtn').classList.toggle('hidden', !state.searchQuery);
            updateUI();
        }

        function clearSearch() {
            document.getElementById('searchInput').value = '';
            state.searchQuery = '';
            document.getElementById('clearSearchBtn').classList.add('hidden');
            updateUI();
        }

        function resetFilters() {
            state.selectedCategory = 'すべて';
            state.dateFilter = 'all';
            clearSearch();
            setViewFilter('all');
        }

        function toggleBookmark(id, event) {
            if (event) event.stopPropagation();
            if (state.bookmarks.has(id)) {
                state.bookmarks.delete(id);
            } else {
                state.bookmarks.add(id);
            }
            updateUI();
            if (state.activeArticleId === id) updateDetailBookmarkIcon();
        }

        // --- DETAIL SLIDE PANEL & AI FEATURES ---
        function openDetailPanel(id) {
            const art = SAMPLE_ARTICLES.find(a => a.id === id);
            if (!art) return;

            state.activeArticleId = id;
            state.readArticles.add(id);

            document.getElementById('detailTitle').innerText = art.title;
            document.getElementById('detailSource').innerText = art.source;
            document.getElementById('detailDate').innerText = new Date(art.date).toLocaleString('ja-JP');
            document.getElementById('detailDescription').innerText = art.description;
            document.getElementById('detailCategoryBadge').innerText = art.category;
            document.getElementById('detailRegionBadge').innerText = art.region.toUpperCase();
            document.getElementById('detailExternalUrl').href = art.url;

            document.getElementById('myMemoArea').value = state.memos[id] || '';
            document.getElementById('memoSavedBadge').classList.add('opacity-0');

            document.getElementById('aiResultBox').classList.add('hidden');
            state.aiSummaryActive = false;
            state.aiTranslated = false;
            document.getElementById('transLangLabel').innerText = art.region === 'jp' ? '英語に翻訳' : '日本語に翻訳';

            updateDetailBookmarkIcon();

            document.getElementById('detailBackdrop').classList.remove('opacity-0', 'pointer-events-none');
            document.getElementById('detailPanel').classList.remove('translate-x-full');

            updateUI();
        }

        function closeDetailPanel() {
            document.getElementById('detailBackdrop').classList.add('opacity-0', 'pointer-events-none');
            document.getElementById('detailPanel').classList.add('translate-x-full');
            state.activeArticleId = null;
        }

        function updateDetailBookmarkIcon() {
            const btn = document.getElementById('detailBookmarkBtn');
            const isBookmarked = state.bookmarks.has(state.activeArticleId);
            btn.innerHTML = `<i data-lucide="star" class="w-5 h-5 ${isBookmarked ? 'text-amber-400 fill-amber-400' : ''}"></i>`;
            if (window.lucide) lucide.createIcons();
        }

        function toggleBookmarkFromDetail() {
            if (state.activeArticleId) {
                toggleBookmark(state.activeArticleId);
            }
        }

        function generateAiSummary() {
            const art = SAMPLE_ARTICLES.find(a => a.id === state.activeArticleId);
            if (!art) return;

            const box = document.getElementById('aiResultBox');
            const pointsList = document.getElementById('aiSummaryPoints');

            pointsList.innerHTML = `
                <li><strong>要点1:</strong> ${art.category} 分野における最新技術トレンドを反映。</li>
                <li><strong>要点2:</strong> メモリ効率の向上とネイティブ推論環境への最適化が主軸。</li>
                <li><strong>要点3:</strong> プロダクション環境への段階的導入が推奨される構成。</li>
            `;

            box.classList.remove('hidden');
            state.aiSummaryActive = true;
        }

        function toggleAiTranslation() {
            const art = SAMPLE_ARTICLES.find(a => a.id === state.activeArticleId);
            if (!art) return;

            state.aiTranslated = !state.aiTranslated;
            const desc = document.getElementById('detailDescription');
            const label = document.getElementById('transLangLabel');

            if (state.aiTranslated) {
                label.innerText = '原文に戻す';
                desc.innerText = art.region === 'jp'
                    ? `[AI Translation / EN]: ${art.description} (Translated locally via client model).`
                    : `[AIローカル翻訳 / JP]: ${art.description} (端末上の軽量モデルで即時翻訳された概要です)。`;
            } else {
                label.innerText = art.region === 'jp' ? '英語に翻訳' : '日本語に翻訳';
                desc.innerText = art.description;
            }
        }

        function handleMemoInput() {
            if (!state.activeArticleId) return;

            const val = document.getElementById('myMemoArea').value;
            state.memos[state.activeArticleId] = val;

            const badge = document.getElementById('memoSavedBadge');
            badge.classList.remove('opacity-0');

            clearTimeout(memoSaveTimeout);
            memoSaveTimeout = setTimeout(() => {
                badge.classList.add('opacity-0');
            }, 1500);

            saveLocalState();
        }

        // --- AUTH MODAL HANDLERS ---
        function openAuthModal() {
            const modal = document.getElementById('authModal');
            modal.classList.remove('opacity-0', 'pointer-events-none');
            
            if (state.user.isLoggedIn) {
                document.getElementById('logoutSection').classList.remove('hidden');
            } else {
                document.getElementById('logoutSection').classList.add('hidden');
            }
        }

        function closeAuthModal() {
            document.getElementById('authModal').classList.add('opacity-0', 'pointer-events-none');
        }

        function handleAuthSubmit(e) {
            e.preventDefault();
            const id = document.getElementById('authUserId').value.trim();
            if (!id) return;

            state.user.isLoggedIn = true;
            state.user.userId = id;
            updateUI();
            closeAuthModal();
        }

        function handleLogout() {
            state.user.isLoggedIn = false;
            state.user.userId = 'ゲストユーザー';
            updateUI();
            closeAuthModal();
        }

        function toggleDarkMode() {
            document.documentElement.classList.toggle('dark');
        }

        // --- CHART.JS IT TREND ANALYTICS ---
        function initChart() {
            const canvas = document.getElementById('trendChartCanvas');
            if (!canvas) return;
            const ctx = canvas.getContext('2d');
            
            trendChartInstance = new Chart(ctx, {
                type: 'line',
                data: {
                    labels: ['10/17', '10/18', '10/19', '10/20', '10/21', '10/22', '10/23'],
                    datasets: [
                        {
                            label: 'AI / LLM',
                            data: [65, 72, 80, 85, 92, 110, 128],
                            borderColor: '#3b82f6',
                            backgroundColor: 'rgba(59, 130, 246, 0.1)',
                            borderWidth: 3,
                            tension: 0.3,
                            fill: true
                        },
                        {
                            label: 'React',
                            data: [40, 42, 45, 50, 48, 55, 62],
                            borderColor: '#06b6d4',
                            borderWidth: 2,
                            tension: 0.3
                        },
                        {
                            label: 'Rust',
                            data: [20, 25, 28, 35, 42, 50, 58],
                            borderColor: '#f59e0b',
                            borderWidth: 2,
                            tension: 0.3
                        },
                        {
                            label: 'Kubernetes',
                            data: [30, 32, 31, 38, 40, 42, 45],
                            borderColor: '#a855f7',
                            borderWidth: 2,
                            tension: 0.3
                        }
                    ]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: {
                        legend: { display: false }
                    },
                    scales: {
                        x: {
                            grid: { display: false },
                            ticks: { font: { size: 11 } }
                        },
                        y: {
                            grid: { color: 'rgba(150, 150, 150, 0.1)' },
                            ticks: { font: { size: 11 } }
                        }
                    }
                }
            });
        }
    </script>
</body>
</html>
