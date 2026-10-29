
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>TISS - 인생 은행</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800;900&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Inter', -apple-system, BlinkMacSystemFont, "Apple SD Gothic Neo", "Pretendard", sans-serif;
            background-color: #e5e7eb;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            margin: 0;
        }
        
        #app-container {
            width: 100%;
            max-width: 400px;
            height: 100vh;
            background-color: #f3f4f6;
            position: relative;
            overflow-x: hidden;
            overflow-y: auto;
            display: flex;
            flex-direction: column;
        }

        /* PC 화면에서는 모바일 앱처럼 둥글게 표시 */
        @media (min-width: 480px) {
            #app-container {
                height: 90vh;
                max-height: 850px;
                border-radius: 2.5rem;
                box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.25);
            }
        }

        /* 스크롤바 숨김 */
        #app-container::-webkit-scrollbar { display: none; }
        #app-container { -ms-overflow-style: none; scrollbar-width: none; }

        /* 바텀 시트 애니메이션 */
        .modal-enter { animation: slideUp 0.3s cubic-bezier(0.16, 1, 0.3, 1) forwards; }
        .modal-exit { animation: slideDown 0.3s cubic-bezier(0.16, 1, 0.3, 1) forwards; }

        @keyframes slideUp {
            from { transform: translateY(100%); opacity: 0; }
            to { transform: translateY(0); opacity: 1; }
        }
        @keyframes slideDown {
            from { transform: translateY(0); opacity: 1; }
            to { transform: translateY(100%); opacity: 0; }
        }

        /* 숫자가 깜빡이거나 흔들리지 않도록 고정폭 폰트 적용 */
        .tabular-nums { font-variant-numeric: tabular-nums; }
    </style>
</head>
<body class="text-gray-900 antialiased">

    <div id="app-container">
        <!-- 헤더 -->
        <header class="flex justify-between items-center px-6 py-5 bg-transparent sticky top-0 z-10 backdrop-blur-sm bg-f3f4f6/70">
            <div class="text-2xl font-black tracking-tighter text-blue-600">TISS</div>
            <button onclick="openModal('settings-modal', 'settings-content')" class="p-2 rounded-full hover:bg-gray-200 transition-colors" aria-label="설정">
                <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6 text-gray-600" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                    <path stroke-linecap="round" stroke-linejoin="round" d="M10.325 4.317c.426-1.756 2.924-1.756 3.35 0a1.724 1.724 0 002.573 1.066c1.543-.94 3.31.826 2.37 2.37a1.724 1.724 0 001.065 2.572c1.756.426 1.756 2.924 0 3.35a1.724 1.724 0 00-1.066 2.573c.94 1.543-.826 3.31-2.37 2.37a1.724 1.724 0 00-2.572 1.065c-.426 1.756-2.924 1.756-3.35 0a1.724 1.724 0 00-2.573-1.066c-1.543.94-3.31-.826-2.37-2.37a1.724 1.724 0 00-1.065-2.572c-1.756-.426-1.756-2.924 0-3.35a1.724 1.724 0 001.066-2.573c-.94-1.543.826-3.31 2.37-2.37.996.608 2.296.07 2.572-1.065z" />
                    <path stroke-linecap="round" stroke-linejoin="round" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z" />
                </svg>
            </button>
        </header>

        <!-- 메인 콘텐츠 -->
        <main class="flex-1 px-4 pb-8 space-y-4">
            
            <!-- 1. 시간 카드 (파란색 테마) -->
            <div class="bg-white rounded-[2rem] p-6 shadow-sm relative overflow-hidden">
                <div class="relative z-10">
                    <div class="flex items-center gap-2 mb-2">
                        <div class="w-8 h-8 rounded-full bg-blue-50 flex items-center justify-center">
                            <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 text-blue-500" viewBox="0 0 20 20" fill="currentColor">
                              <path fill-rule="evenodd" d="M10 18a8 8 0 100-16 8 8 0 000 16zm1-12a1 1 0 10-2 0v4a1 1 0 00.293.707l2.828 2.829a1 1 0 101.415-1.415L11 9.586V6z" clip-rule="evenodd" />
                            </svg>
                        </div>
                        <span class="text-sm font-semibold text-gray-600">남은 시간</span>
                    </div>

                    <div class="mt-4 mb-6">
                        <div class="text-3xl sm:text-4xl font-extrabold tracking-tight text-gray-900 tabular-nums flex items-baseline gap-1 break-words flex-wrap">
                            <span id="val-hours">0</span><span class="text-lg sm:text-xl font-bold text-gray-700">시간</span>
                            <span id="val-minutes" class="ml-1">00</span><span class="text-lg sm:text-xl font-bold text-gray-700">분</span>
                            <span id="val-seconds" class="ml-1 text-blue-500">00</span><span class="text-base sm:text-lg font-bold text-blue-500/80">초</span>
                        </div>
                    </div>

                    <div class="flex gap-3">
                        <button onclick="openModal('transfer-time-modal', 'transfer-time-content')" class="flex-1 bg-blue-50 hover:bg-blue-100 text-blue-600 font-bold py-3.5 px-4 rounded-[1rem] transition-colors shadow-sm text-base flex items-center justify-center gap-2">
                            시간송금
                        </button>
                        <button onclick="openModal('exchange-time-modal', 'exchange-time-content')" class="flex-1 bg-blue-500 hover:bg-blue-600 text-white font-bold py-3.5 px-4 rounded-[1rem] transition-colors shadow-sm text-base flex items-center justify-center gap-2">
                            시간환전
                        </button>
                    </div>
                </div>
            </div>

            <!-- 2. 자산 카드 (초록색 테마) -->
            <div class="bg-white rounded-[2rem] p-6 shadow-sm relative overflow-hidden">
                <div class="relative z-10">
                    <div class="flex items-center gap-2 mb-2">
                        <div class="w-8 h-8 rounded-full bg-green-50 flex items-center justify-center">
                            <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 text-green-500" viewBox="0 0 20 20" fill="currentColor">
                              <path d="M8.433 7.418c.155-.103.346-.196.567-.267v1.698a2.305 2.305 0 01-.567-.267C8.07 8.34 8 8.114 8 8c0-.114.07-.34.433-.582zM11 12.849v-1.698c.22.071.412.164.567.267.364.243.433.468.433.582 0 .114-.07.34-.433.582a2.305 2.305 0 01-.567.267z" />
                              <path fill-rule="evenodd" d="M10 18a8 8 0 100-16 8 8 0 000 16zm1-13a1 1 0 10-2 0v.092a4.535 4.535 0 00-1.676.662C6.602 6.234 6 7.009 6 8c0 .99.602 1.765 1.324 2.246.48.32 1.054.545 1.676.662v1.941c-.391-.127-.68-.317-.843-.504a1 1 0 10-1.51 1.31c.562.649 1.413 1.076 2.353 1.253V15a1 1 0 102 0v-.092a4.535 4.535 0 001.676-.662C13.398 13.766 14 12.991 14 12c0-.99-.602-1.765-1.324-2.246A4.535 4.535 0 0011 9.092V7.151c.391.127.68.317.843.504a1 1 0 101.511-1.31c-.563-.649-1.413-1.076-2.354-1.253V5z" clip-rule="evenodd" />
                            </svg>
                        </div>
                        <span class="text-sm font-semibold text-gray-600">보유 자산</span>
                    </div>

                    <div class="mt-4 mb-6">
                        <div class="text-3xl sm:text-4xl font-extrabold tracking-tight text-gray-900 tabular-nums flex items-baseline gap-1 break-words flex-wrap">
                            <span id="val-money">0</span><span class="text-lg sm:text-xl font-bold text-gray-700">원</span>
                        </div>
                    </div>

                    <div class="flex gap-3">
                        <button onclick="openModal('transfer-money-modal', 'transfer-money-content')" class="flex-1 bg-green-50 hover:bg-green-100 text-green-700 font-bold py-3.5 px-4 rounded-[1rem] transition-colors shadow-sm text-base flex items-center justify-center gap-2">
                            돈송금
                        </button>
                        <button onclick="openModal('exchange-money-modal', 'exchange-money-content')" class="flex-1 bg-green-500 hover:bg-green-600 text-white font-bold py-3.5 px-4 rounded-[1rem] transition-colors shadow-sm text-base flex items-center justify-center gap-2">
                            돈환전
                        </button>
                    </div>
                </div>
            </div>

        </main>

        <!-- 설정 모달 (초기 상태 설정) -->
        <div id="settings-modal" class="absolute inset-0 z-50 hidden flex-col justify-end">
            <div class="absolute inset-0 bg-black/40 transition-opacity" onclick="closeModal('settings-modal', 'settings-content')"></div>
            <div id="settings-content" class="bg-white w-full rounded-t-[2rem] p-6 relative z-10 transform translate-y-full">
                <div class="w-12 h-1.5 bg-gray-300 rounded-full mx-auto mb-6"></div>
                <h2 class="text-2xl font-bold text-gray-800 mb-2">자산 설정</h2>
                <p class="text-gray-500 text-sm mb-6">시작 시간과 보유 금액을 새롭게 설정합니다.<br>0을 입력하면 초기화됩니다.</p>
                
                <div class="space-y-4">
                    <div class="flex gap-4">
                        <div class="flex-1 relative">
                            <input type="number" id="set-hours" placeholder="0" min="0" class="w-full bg-gray-50 border border-gray-200 text-gray-900 rounded-xl px-4 py-3.5 focus:outline-none focus:ring-2 focus:ring-gray-400 text-right pr-12 font-bold text-lg">
                            <span class="absolute right-4 top-1/2 -translate-y-1/2 text-gray-500 font-medium">시간</span>
                        </div>
                        <div class="flex-1 relative">
                            <input type="number" id="set-minutes" placeholder="0" min="0" max="59" class="w-full bg-gray-50 border border-gray-200 text-gray-900 rounded-xl px-4 py-3.5 focus:outline-none focus:ring-2 focus:ring-gray-400 text-right pr-10 font-bold text-lg">
                            <span class="absolute right-4 top-1/2 -translate-y-1/2 text-gray-500 font-medium">분</span>
                        </div>
                    </div>
                    <div class="relative">
                        <input type="number" id="set-money" placeholder="0" min="0" class="w-full bg-gray-50 border border-gray-200 text-gray-900 rounded-xl px-4 py-3.5 focus:outline-none focus:ring-2 focus:ring-gray-400 text-right pr-12 font-bold text-lg">
                        <span class="absolute right-4 top-1/2 -translate-y-1/2 text-gray-500 font-medium">원</span>
                    </div>
                </div>
                
                <div class="mt-8">
                    <button onclick="setInitialAssets()" id="btn-set" class="w-full bg-gray-800 hover:bg-gray-900 text-white font-bold py-4 rounded-xl transition-colors text-lg">
                        새로 설정하기
                    </button>
                </div>
            </div>
        </div>

        <!-- 시간 송금 (마이너스) 모달 -->
        <div id="transfer-time-modal" class="absolute inset-0 z-50 hidden flex-col justify-end">
            <div class="absolute inset-0 bg-black/40 transition-opacity" onclick="closeModal('transfer-time-modal', 'transfer-time-content')"></div>
            <div id="transfer-time-content" class="bg-white w-full rounded-t-[2rem] p-6 relative z-10 transform translate-y-full">
                <div class="w-12 h-1.5 bg-gray-300 rounded-full mx-auto mb-6"></div>
                <h2 class="text-2xl font-bold text-gray-800 mb-2">시간 송금</h2>
                <p class="text-gray-500 text-sm mb-6">내 잔여 시간에서 차감할 시간을 입력하세요.</p>
                
                <div class="space-y-4">
                    <div class="flex gap-4">
                        <div class="flex-1 relative">
                            <input type="number" id="transfer-hours" placeholder="0" min="0" class="w-full bg-gray-50 border border-gray-200 text-gray-900 rounded-xl px-4 py-3.5 focus:outline-none focus:ring-2 focus:ring-blue-500 text-right pr-12 font-bold text-lg">
                            <span class="absolute right-4 top-1/2 -translate-y-1/2 text-gray-500 font-medium">시간</span>
                        </div>
                        <div class="flex-1 relative">
                            <input type="number" id="transfer-minutes" placeholder="0" min="0" max="59" class="w-full bg-gray-50 border border-gray-200 text-gray-900 rounded-xl px-4 py-3.5 focus:outline-none focus:ring-2 focus:ring-blue-500 text-right pr-10 font-bold text-lg">
                            <span class="absolute right-4 top-1/2 -translate-y-1/2 text-gray-500 font-medium">분</span>
                        </div>
                    </div>
                </div>
                
                <div class="mt-8">
                    <button onclick="executeTimeTransfer()" id="btn-transfer-time" class="w-full bg-blue-500 hover:bg-blue-600 text-white font-bold py-4 rounded-xl transition-colors text-lg">
                        시간 보내기
                    </button>
                </div>
            </div>
        </div>

        <!-- 금액 송금 (마이너스) 모달 -->
        <div id="transfer-money-modal" class="absolute inset-0 z-50 hidden flex-col justify-end">
            <div class="absolute inset-0 bg-black/40 transition-opacity" onclick="closeModal('transfer-money-modal', 'transfer-money-content')"></div>
            <div id="transfer-money-content" class="bg-white w-full rounded-t-[2rem] p-6 relative z-10 transform translate-y-full">
                <div class="w-12 h-1.5 bg-gray-300 rounded-full mx-auto mb-6"></div>
                <h2 class="text-2xl font-bold text-gray-800 mb-2">금액 송금</h2>
                <p class="text-gray-500 text-sm mb-6">내 자산에서 차감할 금액을 입력하세요.</p>
                
                <div class="space-y-4">
                    <div class="relative">
                        <input type="number" id="transfer-amount" placeholder="0" min="0" class="w-full bg-gray-50 border border-gray-200 text-gray-900 rounded-xl px-4 py-3.5 focus:outline-none focus:ring-2 focus:ring-green-500 text-right pr-12 font-bold text-lg">
                        <span class="absolute right-4 top-1/2 -translate-y-1/2 text-gray-500 font-medium">원</span>
                    </div>
                </div>
                
                <div class="mt-8">
                    <button onclick="executeMoneyTransfer()" id="btn-transfer-money" class="w-full bg-green-500 hover:bg-green-600 text-white font-bold py-4 rounded-xl transition-colors text-lg">
                        돈 보내기
                    </button>
                </div>
            </div>
        </div>

        <!-- 시간 -> 돈 환전 모달 -->
        <div id="exchange-time-modal" class="absolute inset-0 z-50 hidden flex-col justify-end">
            <div class="absolute inset-0 bg-black/40 transition-opacity" onclick="closeModal('exchange-time-modal', 'exchange-time-content')"></div>
            <div id="exchange-time-content" class="bg-white w-full rounded-t-[2rem] p-6 relative z-10 transform translate-y-full">
                <div class="w-12 h-1.5 bg-gray-300 rounded-full mx-auto mb-6"></div>
                <h2 class="text-2xl font-bold text-gray-800 mb-2">시간 환전</h2>
                <p class="text-gray-500 text-sm mb-6">6분 차감 시 자산에 1,000원이 추가됩니다.<br>(6분 단위 입력)</p>
                
                <div class="space-y-4">
                    <div class="flex gap-4">
                        <div class="flex-1 relative">
                            <input type="number" id="exchange-hours" placeholder="0" min="0" class="w-full bg-gray-50 border border-gray-200 text-gray-900 rounded-xl px-4 py-3.5 focus:outline-none focus:ring-2 focus:ring-blue-500 text-right pr-12 font-bold text-lg">
                            <span class="absolute right-4 top-1/2 -translate-y-1/2 text-gray-500 font-medium">시간</span>
                        </div>
                        <div class="flex-1 relative">
                            <input type="number" id="exchange-minutes" placeholder="0" min="0" max="59" step="6" class="w-full bg-gray-50 border border-gray-200 text-gray-900 rounded-xl px-4 py-3.5 focus:outline-none focus:ring-2 focus:ring-blue-500 text-right pr-10 font-bold text-lg">
                            <span class="absolute right-4 top-1/2 -translate-y-1/2 text-gray-500 font-medium">분</span>
                        </div>
                    </div>
                </div>
                
                <div class="mt-8">
                    <button onclick="executeTimeExchange()" id="btn-exchange-time" class="w-full bg-blue-500 hover:bg-blue-600 text-white font-bold py-4 rounded-xl transition-colors text-lg">
                        돈으로 바꾸기
                    </button>
                </div>
            </div>
        </div>

        <!-- 돈 -> 시간 환전 모달 -->
        <div id="exchange-money-modal" class="absolute inset-0 z-50 hidden flex-col justify-end">
            <div class="absolute inset-0 bg-black/40 transition-opacity" onclick="closeModal('exchange-money-modal', 'exchange-money-content')"></div>
            <div id="exchange-money-content" class="bg-white w-full rounded-t-[2rem] p-6 relative z-10 transform translate-y-full">
                <div class="w-12 h-1.5 bg-gray-300 rounded-full mx-auto mb-6"></div>
                <h2 class="text-2xl font-bold text-gray-800 mb-2">돈 환전</h2>
                <p class="text-gray-500 text-sm mb-6">1,000원 차감 시 남은 시간에 6분이 추가됩니다.<br>(1,000원 단위 입력)</p>
                
                <div class="space-y-4">
                    <div class="relative">
                        <input type="number" id="exchange-amount" placeholder="0" min="0" step="1000" class="w-full bg-gray-50 border border-gray-200 text-gray-900 rounded-xl px-4 py-3.5 focus:outline-none focus:ring-2 focus:ring-green-500 text-right pr-12 font-bold text-lg">
                        <span class="absolute right-4 top-1/2 -translate-y-1/2 text-gray-500 font-medium">원</span>
                    </div>
                </div>
                
                <div class="mt-8">
                    <button onclick="executeMoneyExchange()" id="btn-exchange-money" class="w-full bg-green-500 hover:bg-green-600 text-white font-bold py-4 rounded-xl transition-colors text-lg">
                        시간으로 바꾸기
                    </button>
                </div>
            </div>
        </div>

    </div>

    <script>
        let targetEndTime = 0;
        let currentMoney = 0;
        let timerInterval;

        // 앱 초기화 함수
        function init() {
            // LocalStorage에서 남은 시간 불러오기
            const savedTime = localStorage.getItem('tiss_endTime');
            if (savedTime) {
                targetEndTime = parseInt(savedTime, 10);
                if (targetEndTime < Date.now()) {
                    targetEndTime = 0;
                    localStorage.removeItem('tiss_endTime');
                }
            }
            
            // LocalStorage에서 자산 불러오기
            const savedMoney = localStorage.getItem('tiss_money');
            if (savedMoney) {
                currentMoney = parseInt(savedMoney, 10);
            }

            updateTimer();
            updateMoneyDisplay();
            
            // 1초마다 화면 갱신
            if(timerInterval) clearInterval(timerInterval);
            timerInterval = setInterval(updateTimer, 1000);
        }
        
        // 타이머 계산 및 렌더링 호출
        function updateTimer() {
            if (targetEndTime === 0) {
                renderTime(0, 0, 0);
                return;
            }
            const now = Date.now();
            const diff = targetEndTime - now;
            
            if (diff <= 0) {
                targetEndTime = 0;
                localStorage.removeItem('tiss_endTime');
                renderTime(0, 0, 0);
                return;
            }
            const totalHours = Math.floor(diff / (1000 * 60 * 60));
            const minutes = Math.floor((diff / (1000 * 60)) % 60);
            const seconds = Math.floor((diff / 1000) % 60);
            renderTime(totalHours, minutes, seconds);
        }

        // HTML에 시간 표시
        function renderTime(h, m, s) {
            document.getElementById('val-hours').innerText = h;
            document.getElementById('val-minutes').innerText = m.toString().padStart(2, '0');
            document.getElementById('val-seconds').innerText = s.toString().padStart(2, '0');
        }

        // HTML에 자산(돈) 표시
        function updateMoneyDisplay() {
            document.getElementById('val-money').innerText = currentMoney.toLocaleString('ko-KR');
        }

        // 모달 열기
        function openModal(modalId, contentId) {
            const modal = document.getElementById(modalId);
            const content = document.getElementById(contentId);
            modal.classList.remove('hidden');
            modal.classList.add('flex');
            void content.offsetWidth; // Trigger reflow for animation
            content.classList.remove('modal-exit');
            content.classList.add('modal-enter');
        }

        // 모달 닫기
        function closeModal(modalId, contentId) {
            const modal = document.getElementById(modalId);
            const content = document.getElementById(contentId);
            content.classList.remove('modal-enter');
            content.classList.add('modal-exit');
            setTimeout(() => {
                modal.classList.add('hidden');
                modal.classList.remove('flex');
            }, 300);
        }

        // 에러 발생 시 버튼 색상/텍스트 임시 변경
        function showTempButtonMessage(btnId, msg, originalColorClass, originalHoverClass, errorColorClass, errorHoverClass) {
            const btn = document.getElementById(btnId);
            const oldText = btn.innerText;
            btn.innerText = msg;
            
            // 색상 교체
            const currentClasses = btn.className;
            if(currentClasses.includes(originalColorClass)) {
                btn.classList.replace(originalColorClass, errorColorClass);
                btn.classList.replace(originalHoverClass, errorHoverClass);
            } else {
                // 대체 클래스가 이미 적용된 경우 (연속 클릭 방지)
                return;
            }

            setTimeout(() => {
                btn.innerText = oldText;
                btn.classList.replace(errorColorClass, originalColorClass);
                btn.classList.replace(errorHoverClass, originalHoverClass);
            }, 1500);
        }

        // 설정 모달: 초기 자산 설정
        function setInitialAssets() {
            const hStr = document.getElementById('set-hours').value;
            const mStr = document.getElementById('set-minutes').value;
            const moneyStr = document.getElementById('set-money').value;

            if (hStr === '' && mStr === '' && moneyStr === '') {
                showTempButtonMessage('btn-set', '값을 입력해주세요', 'bg-gray-800', 'hover:bg-gray-900', 'bg-red-500', 'hover:bg-red-600');
                return;
            }

            // 1. 시간 셋팅
            if (hStr !== '' || mStr !== '') {
                const h = parseInt(hStr) || 0;
                const m = parseInt(mStr) || 0;
                
                if (h === 0 && m === 0) {
                    targetEndTime = 0;
                    localStorage.removeItem('tiss_endTime');
                } else {
                    targetEndTime = Date.now() + (h * 60 * 60 * 1000) + (m * 60 * 1000);
                    localStorage.setItem('tiss_endTime', targetEndTime);
                }
            }
            
            // 2. 돈 셋팅
            if (moneyStr !== '') {
                currentMoney = parseInt(moneyStr) || 0;
                localStorage.setItem('tiss_money', currentMoney);
            }
            
            updateTimer();
            updateMoneyDisplay();
            closeModal('settings-modal', 'settings-content');
            
            // 입력 폼 비우기
            document.getElementById('set-hours').value = '';
            document.getElementById('set-minutes').value = '';
            document.getElementById('set-money').value = '';
        }

        // 시간 송금 (차감) 로직
        function executeTimeTransfer() {
            const h = parseInt(document.getElementById('transfer-hours').value) || 0;
            const m = parseInt(document.getElementById('transfer-minutes').value) || 0;
            const transferMs = (h * 60 * 60 * 1000) + (m * 60 * 1000);

            if (transferMs === 0) {
                showTempButtonMessage('btn-transfer-time', '보낼 시간을 입력하세요', 'bg-blue-500', 'hover:bg-blue-600', 'bg-red-500', 'hover:bg-red-600');
                return;
            }
            if (targetEndTime === 0 || (targetEndTime - Date.now() <= 0)) {
                showTempButtonMessage('btn-transfer-time', '잔여 시간이 없습니다', 'bg-blue-500', 'hover:bg-blue-600', 'bg-red-500', 'hover:bg-red-600');
                return;
            }
            if (targetEndTime - Date.now() < transferMs) {
                showTempButtonMessage('btn-transfer-time', '남은 시간이 부족합니다', 'bg-blue-500', 'hover:bg-blue-600', 'bg-red-500', 'hover:bg-red-600');
                return;
            }

            targetEndTime -= transferMs;
            if (targetEndTime < Date.now()) targetEndTime = 0;

            localStorage.setItem('tiss_endTime', targetEndTime);
            updateTimer();
            closeModal('transfer-time-modal', 'transfer-time-content');
            
            document.getElementById('transfer-hours').value = '';
            document.getElementById('transfer-minutes').value = '';
        }

        // 금액 송금 (차감) 로직
        function executeMoneyTransfer() {
            const amount = parseInt(document.getElementById('transfer-amount').value) || 0;

            if (amount <= 0) {
                showTempButtonMessage('btn-transfer-money', '보낼 금액을 입력하세요', 'bg-green-500', 'hover:bg-green-600', 'bg-red-500', 'hover:bg-red-600');
                return;
            }
            if (currentMoney === 0) {
                showTempButtonMessage('btn-transfer-money', '잔액이 없습니다', 'bg-green-500', 'hover:bg-green-600', 'bg-red-500', 'hover:bg-red-600');
                return;
            }
            if (currentMoney < amount) {
                showTempButtonMessage('btn-transfer-money', '잔액이 부족합니다', 'bg-green-500', 'hover:bg-green-600', 'bg-red-500', 'hover:bg-red-600');
                return;
            }

            currentMoney -= amount;
            localStorage.setItem('tiss_money', currentMoney);
            updateMoneyDisplay();
            closeModal('transfer-money-modal', 'transfer-money-content');
            
            document.getElementById('transfer-amount').value = '';
        }

        // 환전: 시간 -> 돈 (6분당 1,000원)
        function executeTimeExchange() {
            const h = parseInt(document.getElementById('exchange-hours').value) || 0;
            const m = parseInt(document.getElementById('exchange-minutes').value) || 0;
            const totalMinutes = (h * 60) + m;
            
            if (totalMinutes <= 0) {
                showTempButtonMessage('btn-exchange-time', '환전할 시간을 입력하세요', 'bg-blue-500', 'hover:bg-blue-600', 'bg-red-500', 'hover:bg-red-600');
                return;
            }
            if (totalMinutes % 6 !== 0) {
                showTempButtonMessage('btn-exchange-time', '6분 단위로 입력하세요', 'bg-blue-500', 'hover:bg-blue-600', 'bg-red-500', 'hover:bg-red-600');
                return;
            }
            
            const transferMs = totalMinutes * 60 * 1000;
            
            if (targetEndTime === 0 || (targetEndTime - Date.now() < transferMs)) {
                showTempButtonMessage('btn-exchange-time', '남은 시간이 부족합니다', 'bg-blue-500', 'hover:bg-blue-600', 'bg-red-500', 'hover:bg-red-600');
                return;
            }

            // 시간 차감
            targetEndTime -= transferMs;
            if (targetEndTime < Date.now()) targetEndTime = 0;
            localStorage.setItem('tiss_endTime', targetEndTime);
            
            // 금액 추가
            const earnedMoney = (totalMinutes / 6) * 1000;
            currentMoney += earnedMoney;
            localStorage.setItem('tiss_money', currentMoney);

            updateTimer();
            updateMoneyDisplay();
            closeModal('exchange-time-modal', 'exchange-time-content');
            
            document.getElementById('exchange-hours').value = '';
            document.getElementById('exchange-minutes').value = '';
        }

        // 환전: 돈 -> 시간 (1,000원당 6분)
        function executeMoneyExchange() {
            const amount = parseInt(document.getElementById('exchange-amount').value) || 0;
            
            if (amount <= 0) {
                showTempButtonMessage('btn-exchange-money', '환전할 금액을 입력하세요', 'bg-green-500', 'hover:bg-green-600', 'bg-red-500', 'hover:bg-red-600');
                return;
            }
            if (amount % 1000 !== 0) {
                showTempButtonMessage('btn-exchange-money', '1,000원 단위로 입력하세요', 'bg-green-500', 'hover:bg-green-600', 'bg-red-500', 'hover:bg-red-600');
                return;
            }
            if (currentMoney < amount) {
                showTempButtonMessage('btn-exchange-money', '잔액이 부족합니다', 'bg-green-500', 'hover:bg-green-600', 'bg-red-500', 'hover:bg-red-600');
                return;
            }

            // 금액 차감
            currentMoney -= amount;
            localStorage.setItem('tiss_money', currentMoney);
            
            // 시간 추가
            const earnedMinutes = (amount / 1000) * 6;
            const addMs = earnedMinutes * 60 * 1000;
            
            if (targetEndTime === 0 || targetEndTime < Date.now()) {
                targetEndTime = Date.now() + addMs;
            } else {
                targetEndTime += addMs;
            }
            localStorage.setItem('tiss_endTime', targetEndTime);

            updateTimer();
            updateMoneyDisplay();
            closeModal('exchange-money-modal', 'exchange-money-content');
            
            document.getElementById('exchange-amount').value = '';
        }

        // 화면 렌더링 시 초기화 함수 실행
        window.onload = init;
    </script>
</body>
</html>