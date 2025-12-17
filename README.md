<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>TWD 極速匯率換算 | Currency Exchanger</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <link href="https://fonts.googleapis.com/css2?family=Noto+Sans+TC:wght@300;400;700&family=Roboto+Mono:wght@500&display=swap" rel="stylesheet">
    
    <style>
        /* 基礎設定 */
        body {
            font-family: 'Noto Sans TC', sans-serif;
            overflow-x: hidden;
            background-color: #0f172a;
        }

        /* 背景動態特效 (Floating Orbs) */
        .orb {
            position: absolute;
            border-radius: 50%;
            filter: blur(80px);
            z-index: -1;
            animation: float 10s infinite ease-in-out alternate;
        }
        .orb-1 { width: 300px; height: 300px; background: #4f46e5; top: 10%; left: 10%; animation-delay: 0s; }
        .orb-2 { width: 400px; height: 400px; background: #06b6d4; bottom: 10%; right: 10%; animation-delay: -5s; }
        .orb-3 { width: 200px; height: 200px; background: #ec4899; top: 40%; left: 50%; transform: translate(-50%, -50%); animation-duration: 15s; }

        @keyframes float {
            0% { transform: translate(0, 0) scale(1); }
            100% { transform: translate(20px, 40px) scale(1.1); }
        }

        /* 毛玻璃卡片特效 */
        .glass-card {
            background: rgba(255, 255, 255, 0.05);
            backdrop-filter: blur(16px);
            -webkit-backdrop-filter: blur(16px);
            border: 1px solid rgba(255, 255, 255, 0.1);
            box-shadow: 0 4px 30px rgba(0, 0, 0, 0.5);
        }

        /* 進場動畫 */
        .slide-in {
            animation: slideUp 0.8s cubic-bezier(0.16, 1, 0.3, 1) forwards;
            opacity: 0;
            transform: translateY(30px);
        }

        @keyframes slideUp {
            to { opacity: 1; transform: translateY(0); }
        }

        /* 數字字體 */
        .font-mono { font-family: 'Roboto Mono', monospace; }

        /* 自定義切換按鈕動畫 */
        .swap-btn:active { transform: scale(0.9) rotate(180deg); }
        .swap-btn { transition: all 0.3s ease; }
    </style>
</head>
<body class="text-white min-h-screen flex items-center justify-center p-4 relative">

    <div class="orb orb-1"></div>
    <div class="orb orb-2"></div>
    <div class="orb orb-3"></div>

    <main class="w-full max-w-4xl glass-card rounded-3xl p-6 md:p-10 slide-in grid grid-cols-1 md:grid-cols-2 gap-8 relative overflow-hidden">
        
        <div class="flex flex-col justify-center space-y-6 z-10">
            <header>
                <h1 class="text-2xl md:text-3xl font-bold tracking-wide mb-1 bg-clip-text text-transparent bg-gradient-to-r from-blue-200 to-cyan-200">
                    即時匯率換算
                </h1>
                <p class="text-slate-400 text-sm">以台灣銀行牌告匯率為基準 (模擬)</p>
            </header>

            <div class="space-y-4">
                <div class="relative group">
                    <label class="block text-xs text-slate-400 mb-1 ml-1">持有金額</label>
                    <input type="number" id="amount" value="1000" class="w-full bg-slate-800/50 border border-slate-600 rounded-xl px-4 py-3 text-2xl font-mono focus:outline-none focus:border-cyan-400 focus:ring-1 focus:ring-cyan-400 transition-all text-white placeholder-slate-600" placeholder="0.00">
                </div>

                <div class="flex items-end gap-2 relative">
                    <div class="flex-1">
                        <label class="block text-xs text-slate-400 mb-1 ml-1">持有幣種</label>
                        <select id="from-currency" class="w-full bg-slate-800/50 border border-slate-600 rounded-xl px-3 py-3 text-lg appearance-none cursor-pointer hover:bg-slate-700/50 transition-colors focus:outline-none">
                            <option value="TWD">🇹🇼 TWD 新台幣</option>
                            <option value="USD" selected>🇺🇸 USD 美金</option>
                            <option value="JPY">🇯🇵 JPY 日幣</option>
                            <option value="EUR">🇪🇺 EUR 歐元</option>
                            <option value="CNY">🇨🇳 CNY 人民幣</option>
                            <option value="KRW">🇰🇷 KRW 韓元</option>
                        </select>
                    </div>

                    <button id="swap-btn" class="swap-btn mb-1 p-3 bg-gradient-to-r from-cyan-500 to-blue-600 rounded-full shadow-lg shadow-cyan-500/30 hover:shadow-cyan-500/50 text-white z-20">
                        <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 7h12m0 0l-4-4m4 4l-4 4m0 6H4m0 0l4 4m-4-4l4-4" />
                        </svg>
                    </button>

                    <div class="flex-1">
                        <label class="block text-xs text-slate-400 mb-1 ml-1">兌換目標</label>
                        <select id="to-currency" class="w-full bg-slate-800/50 border border-slate-600 rounded-xl px-3 py-3 text-lg appearance-none cursor-pointer hover:bg-slate-700/50 transition-colors focus:outline-none">
                            <option value="TWD" selected>🇹🇼 TWD 新台幣</option>
                            <option value="USD">🇺🇸 USD 美金</option>
                            <option value="JPY">🇯🇵 JPY 日幣</option>
                            <option value="EUR">🇪🇺 EUR 歐元</option>
                            <option value="CNY">🇨🇳 CNY 人民幣</option>
                            <option value="KRW">🇰🇷 KRW 韓元</option>
                        </select>
                    </div>
                </div>
            </div>

            <div class="bg-gradient-to-br from-slate-800/80 to-slate-900/80 rounded-2xl p-6 border border-slate-700/50 relative overflow-hidden group">
                <div class="absolute top-0 right-0 w-20 h-20 bg-cyan-500 blur-[50px] opacity-20 group-hover:opacity-30 transition-opacity"></div>
                <p class="text-sm text-slate-400 mb-1">預估兌換金額</p>
                <div class="flex items-baseline gap-2">
                    <span id="result" class="text-4xl md:text-5xl font-bold font-mono text-cyan-300 tracking-tight">--</span>
                    <span id="result-unit" class="text-xl text-slate-500 font-bold">TWD</span>
                </div>
                <p class="text-xs text-slate-500 mt-2" id="rate-display">1 USD = 31.5 TWD</p>
            </div>
        </div>

        <div class="flex flex-col z-10 border-t md:border-t-0 md:border-l border-slate-700/50 pt-6 md:pt-0 md:pl-8">
            <div class="flex justify-between items-center mb-4">
                <h3 class="text-lg font-bold text-slate-200 flex items-center gap-2">
                    <span class="w-2 h-6 bg-cyan-500 rounded-full"></span>
                    七日匯率走勢
                </h3>
                <span id="trend-indicator" class="px-2 py-1 bg-green-500/20 text-green-400 text-xs rounded font-bold">
                    +0.25%
                </span>
            </div>
            
            <div class="flex-1 min-h-[250px] relative w-full">
                <canvas id="rateChart"></canvas>
            </div>
            
            <div class="mt-4 grid grid-cols-3 gap-2 text-center text-xs text-slate-400">
                <div class="bg-slate-800/40 p-2 rounded-lg">
                    <div class="block mb-1">最高</div>
                    <div class="font-mono text-white" id="stat-high">--</div>
                </div>
                <div class="bg-slate-800/40 p-2 rounded-lg">
                    <div class="block mb-1">最低</div>
                    <div class="font-mono text-white" id="stat-low">--</div>
                </div>
                <div class="bg-slate-800/40 p-2 rounded-lg">
                    <div class="block mb-1">平均</div>
                    <div class="font-mono text-white" id="stat-avg">--</div>
                </div>
            </div>
        </div>
    </main>

    <script>
        // 1. 模擬數據 (相對於 USD)
        // 在實際專案中，這裡應替換為 API Fetch
        const rates = {
            USD: 1,
            TWD: 31.25,
            JPY: 148.50,
            EUR: 0.92,
            CNY: 7.19,
            KRW: 1330.0
        };

        // 2. 音效處理 (使用 Base64 避免跨域問題或檔案缺失，這是一個清脆的 Pop 聲)
        const popSound = new Audio("data:audio/wav;base64,UklGRl9vT19XQVZFZm10IBAAAAABAAEAQB8AAEAfAAABAAgAZGF0YU"); // 簡化版佔位符
        // 為了更好的體驗，這裡使用一個簡單的合成音效函數
        const audioCtx = new (window.AudioContext || window.webkitAudioContext)();
        function playSound() {
            if (audioCtx.state === 'suspended') audioCtx.resume();
            const oscillator = audioCtx.createOscillator();
            const gainNode = audioCtx.createGain();
            oscillator.type = 'sine';
            oscillator.frequency.setValueAtTime(800, audioCtx.currentTime);
            oscillator.frequency.exponentialRampToValueAtTime(300, audioCtx.currentTime + 0.1);
            gainNode.gain.setValueAtTime(0.3, audioCtx.currentTime);
            gainNode.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.1);
            oscillator.connect(gainNode);
            gainNode.connect(audioCtx.destination);
            oscillator.start();
            oscillator.stop(audioCtx.currentTime + 0.1);
        }

        // 3. DOM 元素
        const amountEl = document.getElementById('amount');
        const fromEl = document.getElementById('from-currency');
        const toEl = document.getElementById('to-currency');
        const resultEl = document.getElementById('result');
        const resultUnitEl = document.getElementById('result-unit');
        const rateDisplayEl = document.getElementById('rate-display');
        const swapBtn = document.getElementById('swap-btn');
        const trendIndicator = document.getElementById('trend-indicator');

        // Chart 實例
        let chartInstance = null;

        // 4. 核心邏輯：換算
        function calculate() {
            const amount = parseFloat(amountEl.value);
            const from = fromEl.value;
            const to = toEl.value;

            if (isNaN(amount)) {
                resultEl.innerText = "0.00";
                return;
            }

            // 公式: (金額 / 來源匯率) * 目標匯率
            // 因為所有匯率都是 base USD
            const rate = (rates[to] / rates[from]);
            const result = amount * rate;

            // 動畫跳動數字
            animateValue(resultEl, parseFloat(resultEl.innerText.replace(/,/g, '') || 0), result, 500);
            
            resultUnitEl.innerText = to;
            rateDisplayEl.innerText = `1 ${from} ≈ ${rate.toFixed(4)} ${to}`;
            
            // 更新圖表
            updateChart(from, to, rate);
        }

        // 數字滾動動畫
        function animateValue(obj, start, end, duration) {
            let startTimestamp = null;
            const step = (timestamp) => {
                if (!startTimestamp) startTimestamp = timestamp;
                const progress = Math.min((timestamp - startTimestamp) / duration, 1);
                // 增加千分位
                obj.innerHTML = (progress * (end - start) + start).toLocaleString('en-US', {
                    minimumFractionDigits: 2,
                    maximumFractionDigits: 2
                });
                if (progress < 1) {
                    window.requestAnimationFrame(step);
                }
            };
            window.requestAnimationFrame(step);
        }

        // 5. 生成模擬歷史數據並更新圖表
        function updateChart(from, to, currentRate) {
            const ctx = document.getElementById('rateChart').getContext('2d');
            
            // 產生過去7天的模擬數據 (在當前匯率上下波動)
            const labels = [];
            const data = [];
            const days = ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'];
            let minVal = currentRate, maxVal = currentRate, sumVal = 0;

            for (let i = 6; i >= 0; i--) {
                labels.push(days[(new Date().getDay() - i + 7) % 7]);
                // 隨機波動 +/- 1%
                const noise = currentRate * (Math.random() * 0.02 - 0.01);
                const val = currentRate + noise;
                data.push(val);
                
                if(val < minVal) minVal = val;
                if(val > maxVal) maxVal = val;
                sumVal += val;
            }
            // 確保最後一點是當前匯率
            data[6] = currentRate;

            // 更新統計數據
            document.getElementById('stat-high').innerText = maxVal.toFixed(3);
            document.getElementById('stat-low').innerText = minVal.toFixed(3);
            document.getElementById('stat-avg').innerText = (sumVal/7).toFixed(3);

            // 更新漲跌幅標籤
            const change = ((data[6] - data[0]) / data[0]) * 100;
            trendIndicator.innerText = (change >= 0 ? '+' : '') + change.toFixed(2) + '%';
            trendIndicator.className = `px-2 py-1 text-xs rounded font-bold ${change >= 0 ? 'bg-green-500/20 text-green-400' : 'bg-red-500/20 text-red-400'}`;

            // Chart.js 設定
            if (chartInstance) {
                chartInstance.destroy();
            }

            const gradient = ctx.createLinearGradient(0, 0, 0, 400);
            gradient.addColorStop(0, 'rgba(6, 182, 212, 0.5)'); // Cyan
            gradient.addColorStop(1, 'rgba(6, 182, 212, 0)');

            chartInstance = new Chart(ctx, {
                type: 'line',
                data: {
                    labels: labels,
                    datasets: [{
                        label: 'Exchange Rate',
                        data: data,
                        borderColor: '#22d3ee', // Cyan-400
                        backgroundColor: gradient,
                        borderWidth: 2,
                        pointBackgroundColor: '#fff',
                        pointBorderColor: '#22d3ee',
                        pointRadius: 4,
                        pointHoverRadius: 6,
                        fill: true,
                        tension: 0.4 // 平滑曲線
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: {
                        legend: { display: false },
                        tooltip: {
                            mode: 'index',
                            intersect: false,
                            backgroundColor: 'rgba(15, 23, 42, 0.9)',
                            titleColor: '#cbd5e1',
                            bodyColor: '#22d3ee',
                            borderColor: 'rgba(255,255,255,0.1)',
                            borderWidth: 1,
                            callbacks: {
                                label: function(context) {
                                    return context.parsed.y.toFixed(4);
                                }
                            }
                        }
                    },
                    scales: {
                        x: {
                            grid: { display: false },
                            ticks: { color: '#64748b' }
                        },
                        y: {
                            display: false, // 隱藏Y軸讓圖表更簡潔
                            min: minVal * 0.99,
                            max: maxVal * 1.01
                        }
                    },
                    interaction: {
                        mode: 'nearest',
                        axis: 'x',
                        intersect: false
                    }
                }
            });
        }

        // 6. 事件監聽
        amountEl.addEventListener('input', calculate);
        fromEl.addEventListener('change', () => { playSound(); calculate(); });
        toEl.addEventListener('change', () => { playSound(); calculate(); });

        swapBtn.addEventListener('click', () => {
            playSound();
            
            // 添加旋轉動畫 class
            swapBtn.style.transform = "rotate(180deg)";
            setTimeout(() => swapBtn.style.transform = "rotate(0deg)", 300);

            // 交換數值
            const temp = fromEl.value;
            fromEl.value = toEl.value;
            toEl.value = temp;
            
            calculate();
        });

        // 初始化
        window.addEventListener('load', () => {
            calculate();
        });

    </script>
</body>
</html>
