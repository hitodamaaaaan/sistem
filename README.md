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
        ::-webkit-scrollbar { width: 6px; height: 6px; }
        ::-webkit-scrollbar-track { background: rgba(0, 0, 0, 0.05); }
        .dark ::-webkit-scrollbar-track { background: rgba(255, 255, 255, 0.05); }
        ::-webkit-scrollbar-thumb { background: rgba(0, 0, 0, 0.2); border-radius: 9999px; }
        .dark ::-webkit-scrollbar-thumb { background: rgba(255, 255, 255, 0.2); }
    </style>
</head>
<body class="h-screen overflow-hidden bg-slate-100 dark:bg-slate-950 text-slate-900 dark:text-slate-100 font-sans antialiased selection:bg-brand-500 selection:text-white transition-colors duration-200">

    <div id="app" class="flex h-full w-full overflow-hidden">
        
        <!-- LEFT SIDEBAR -->
        <aside class="w-64 md:w-72 bg-white dark:bg-slate-900 border-r border-slate-200 dark:border-slate-800 flex flex-col shrink-0 z-20 transition-all duration-300">
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

            <div class="flex-1 overflow-y-auto px-3 py-2 space-y-6">
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

                <div>
                    <p class="px-3 text-[11px] font-bold uppercase tracking-wider text-slate-400 dark:text-slate-500 mb-2">カテゴリ</p>
                    <nav class="space-y-1" id="categoryNav"></nav>
                </div>
            </div>

            <div class="p-3 border-t border-slate-200 dark:border-slate-800 bg-slate-50 dark:bg-slate-900/50">
                <div id="userInfoBox" class="flex items-center justify-between">
                    <div class="flex items-center space-x-2.5 min-w-0">
                        <div id="userAvatar" class="w-9 h-9 rounded-full bg-slate-200 dark:bg-slate-700 flex items-center justify-center font-bold text-sm text-slate-700 dark:text-slate-200 shrink-0">U</div>
                        <div class="min-w-0">
                            <p id="userName" class="text-sm font-semibold truncate text-slate-800 dark:text-slate-200">ゲストユーザー</p>
                            <p id="userRole" class="text-[11px] text-slate-500 dark:text-slate-400 truncate">未ログイン</p>
                        </div>
                    </div>
                    <button onclick="openAuthModal()" class="p-2 text-slate-500 hover:text-brand-600 dark:hover:text-brand-400 rounded-lg hover:bg-white dark:hover:bg-slate-800 transition-colors" title="ログイン">
                        <i data-lucide="log-in" class="w-5 h-5"></i>
                    </button>
                </div>
            </div>
        </aside>

        <!-- MAIN CONTENT AREA -->
        <main class="flex-1 flex flex-col min-w-0 h-full overflow-hidden bg-slate-100 dark:bg-slate-950">
            <header class="bg-white dark:bg-slate-900 border-b border-slate-200 dark:border-slate-800 px-6 py-4 shrink-0 shadow-sm">
                <div class="flex flex-col md:flex-row md:items-center md:justify-between gap-4">
                    <div class="relative flex-1 max-w-xl">
                        <i data-lucide="search" class="w-5 h-5 absolute left-3.5 top-1/2 -translate-y-1/2 text-slate-400"></i>
                        <input type="text" id="searchInput" oninput="handleSearch()" placeholder="タイトル・概要・キーワードから検索..." class="w-full pl-10 pr-10 py-2 bg-slate-100 dark:bg-slate-800 text-slate-900 dark:text-slate-100 text-sm rounded-xl border border-transparent focus:border-brand-500 focus:bg-white dark:focus:bg-slate-900 focus:outline-none transition-all placeholder-slate-400">
                        <button id="clearSearchBtn" onclick="clearSearch()" class="hidden absolute right-3 top-1/2 -translate-y-1/2 text-slate-400 hover:text-slate-600">
                            <i data-lucide="x" class="w-4 h-4"></i>
                        </button>
                    </div>

                    <div class="flex items-center space-x-3 overflow-x-auto pb-1 md:pb-0">
                        <div class="flex items-center space-x-1 bg-slate-100 dark:bg-slate-800 p-1 rounded-xl text-xs font-medium">
                            <button onclick="setDateFilter('all')" id="date-all" class="px-2.5 py-1 rounded-lg transition-all bg-white dark:bg-slate-700 shadow-sm text-slate-900 dark:text-white">全期間</button>
                            <button onclick="setDateFilter('24h')" id="date-24h" class="px-2.5 py-1 rounded-lg transition-all text-slate-600 dark:text-slate-400 hover:text-slate-900 dark:hover:text-white">24時間</button>
                            <button onclick="setDateFilter('7d')" id="date-7d" class="px-2.5 py-1 rounded-lg transition-all text-slate-600 dark:text-slate-400 hover:text-slate-900 dark:hover:text-white">7日間</button>
                            <button onclick="setDateFilter('30d')" id="date-30d" class="px-2.5 py-1 rounded-lg transition-all text-slate-600 dark:text-slate-400 hover:text-slate-900 dark:hover:text-white">30日</button>
                        </div>
                        <button onclick="refreshArticlesFromAPI()" id="syncBtn" class="flex items-center space-x-1.5 px-3 py-2 bg-brand-600 hover:bg-brand-700 text-white text-xs font-semibold rounded-xl transition-all shadow-sm">
                            <i data-lucide="refresh-cw" id="syncIcon" class="w-4 h-4"></i>
                            <span class="hidden sm:inline">同期</span>
                        </button>
                    </div>
                </div>
            </header>

            <div class="flex-1 overflow-y-auto p-6" id="mainScrollArea">
                <div class="mb-6 flex items-center justify-between">
                    <div>
                        <h2 id="viewTitle" class="text-xl font-bold text-slate-900 dark:text-white flex items-center space-x-2"><span>すべての記事</span></h2>
                        <p id="viewDescription" class="text-xs text-slate-500 dark:text-slate-400 mt-1">収集された最新ITニュース・技術メタデータ一覧</p>
                    </div>
                    <span id="resultCountBadge" class="text-xs font-semibold px-3 py-1 bg-slate-200 dark:bg-slate-800 text-slate-700 dark:text-slate-300 rounded-full">0 件を表示中</span>
                </div>

                <!-- FEED VIEW CONTAINER -->
                <div id="feedView" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-2 xl:grid-cols-3 gap-5"></div>

                <!-- ANALYTICS VIEW CONTAINER -->
                <div id="analyticsView" class="hidden space-y-6">
                    <div class="bg-white dark:bg-slate-900 p-6 rounded-2xl border border-slate-200 dark:border-slate-800 shadow-sm">
                        <h3 class="font-bold text-lg text-slate-900 dark:text-white mb-4">主要技術スタックの言及数トレンド推移</h3>
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
                    <button onclick="resetFilters()" class="mt-2 px-4 py-2 bg-brand-600 text-white text-xs font-semibold rounded-xl">フィルターをクリア</button>
                </div>
            </div>
        </main>
    </div>

    <!-- SCRIPT SECTION -->
    <script>
        const SAMPLE_ARTICLES = [
            {
                id: "art-jp-001",
                region: "jp",
                category: "AI/LLM",
                title: "ローカルLLM開発フレームワークの新バージョンが公開、軽量化と高速化を実現",
                description: "TauriおよびRustベースのネイティブクライアントで動作可能な軽量AIモデルの最適化ライブラリがリリースされました。",
                source: "TechNews Japan",
                url: "https://example.com/news/art-jp-001",
                date: "2026-10-23T09:30:00Z"
            },
            {
                id: "art-jp-002",
                region: "jp",
                category: "Frontend",
                title: "React 19の新機能とパフォーマンス最適化手法の解説まとめ",
                description: "React 19で正式導入されたServer Actionsおよび自動メモ化コンパイラの仕組みについて解説します。",
                source: "WebDev Weekly JP",
                url: "https://example.com/news/art-jp-002",
                date: "2026-10-22T14:15:00Z"
            }
        ];

        const CATEGORIES = ["すべて", "AI/LLM", "Frontend", "Backend", "Cloud/DevOps", "Security", "Mobile"];

        let state = {
            currentRegion: 'jp',
            currentView: 'all',
            selectedCategory: 'すべて',
            dateFilter: 'all',
            searchQuery: '',
            user: { isLoggedIn: false, userId: 'ゲストユーザー' },
            bookmarks: new Set(),
            readArticles: new Set(),
            memos: {},
            activeArticleId: null
        };

        let trendChartInstance = null;

        function getFilteredArticles() {
            return SAMPLE_ARTICLES.filter(art => {
                if (art.region !== state.currentRegion) return false;
                if (state.selectedCategory !== 'すべて' && art.category !== state.selectedCategory) return false;
                if (state.currentView === 'bookmarks' && !state.bookmarks.has(art.id)) return false;
                if (state.currentView === 'unread' && state.readArticles.has(art.id)) return false;
                return true;
            });
        }

        function renderCategoryNav() {
            const nav = document.getElementById('categoryNav');
            if (!nav) return;
            const regionArticles = SAMPLE_ARTICLES.filter(a => a.region === state.currentRegion);

            nav.innerHTML = CATEGORIES.map(cat => {
                const count = cat === 'すべて' ? regionArticles.length : regionArticles.filter(a => a.category === cat).length;
                const isActive = state.selectedCategory === cat;
                return `
                    <button onclick="setCategory('${cat}')" class="w-full flex items-center justify-between px-3 py-1.5 text-xs font-medium rounded-lg transition-colors ${isActive ? 'bg-slate-200 dark:bg-slate-800 text-brand-600 font-bold' : 'text-slate-600 dark:text-slate-400 hover:bg-slate-100'}">
                        <span>${cat}</span>
                        <span class="text-[10px] px-1.5 py-0.2 rounded-full ${isActive ? 'bg-brand-500 text-white' : 'bg-slate-100 dark:bg-slate-800'}">${count}</span>
                    </button>
                `;
            }).join('');
        }

        function updateUI() {
            const regionArticles = SAMPLE_ARTICLES.filter(a => a.region === state.currentRegion);
            document.getElementById('badge-all-count').innerText = regionArticles.length;
            document.getElementById('badge-bookmark-count').innerText = regionArticles.filter(a => state.bookmarks.has(a.id)).length;
            document.getElementById('badge-unread-count').innerText = regionArticles.filter(a => !state.readArticles.has(a.id)).length;

            const feedView = document.getElementById('feedView');
            const analyticsView = document.getElementById('analyticsView');
            const emptyState = document.getElementById('emptyState');

            if (state.currentView === 'analytics') {
                feedView.classList.add('hidden');
                analyticsView.classList.remove('hidden');
                emptyState.classList.add('hidden');
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
            if (window.lucide) lucide.createIcons();
        }

        function renderArticleCards(articles) {
            const container = document.getElementById('feedView');
            container.innerHTML = articles.map(art => {
                const isBookmarked = state.bookmarks.has(art.id);
                return `
                    <div class="bg-white dark:bg-slate-900 rounded-2xl border border-slate-200 dark:border-slate-800 overflow-hidden shadow-sm flex flex-col justify-between">
                        <div class="p-5 flex-1 flex flex-col justify-between">
                            <div>
                                <span class="text-[10px] font-bold px-2.5 py-1 bg-brand-100 text-brand-700 rounded-lg">${art.category}</span>
                                <h3 class="font-bold text-slate-900 dark:text-white text-base mt-2 mb-2">${art.title}</h3>
                                <p class="text-xs text-slate-600 dark:text-slate-400 line-clamp-2">${art.description}</p>
                            </div>
                            <div class="pt-3 mt-4 border-t border-slate-100 dark:border-slate-800 flex items-center justify-between text-xs text-slate-500">
                                <span>${art.source}</span>
                                <button onclick="toggleBookmark('${art.id}', event)" class="p-1.5 text-slate-400 hover:text-amber-400">
                                    <i data-lucide="star" class="w-4 h-4 ${isBookmarked ? 'text-amber-400 fill-amber-400' : ''}"></i>
                                </button>
                            </div>
                        </div>
                    </div>
                `;
            }).join('');
        }

        function setRegion(r) { state.currentRegion = r; updateUI(); }
        function setCategory(cat) { state.selectedCategory = cat; updateUI(); }
        function setViewFilter(view) { state.currentView = view; updateUI(); }
        function setDateFilter(d) { state.dateFilter = d; updateUI(); }
        function toggleBookmark(id, event) {
            if (event) event.stopPropagation();
            if (state.bookmarks.has(id)) state.bookmarks.delete(id);
            else state.bookmarks.add(id);
            updateUI();
        }
        function toggleDarkMode() { document.documentElement.classList.toggle('dark'); }
        function refreshArticlesFromAPI() { updateUI(); }
        function clearSearch() { document.getElementById('searchInput').value = ''; updateUI(); }
        function handleSearch() { updateUI(); }
        function resetFilters() { state.selectedCategory = 'すべて'; updateUI(); }
        function openAuthModal() {}

        window.onload = function() {
            renderCategoryNav();
            updateUI();
        };
    </script>
</body>
</html>
