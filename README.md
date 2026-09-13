<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Personal AI Chef - AI เชฟส่วนตัว</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Font Awesome -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Google Fonts: Prompt -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Prompt:wght@300;400;500;600;700&display=swap" rel="stylesheet">
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    colors: {
                        brand: {
                            50: '#fffbe6',
                            100: '#fff1b8',
                            500: '#fa8c16',
                            600: '#d46b08',
                            700: '#ad4e00',
                        },
                        chef: {
                            emerald: '#10b981',
                            dark: '#1e293b'
                        }
                    },
                    fontFamily: {
                        sans: ['Prompt', 'sans-serif'],
                    }
                }
            }
        }
    </script>
    <style>
        body {
            font-family: 'Prompt', sans-serif;
            background-color: #f8fafc;
        }
        .drag-active {
            border-color: #fa8c16 !important;
            background-color: #fffbe6 !important;
        }
        .glass-card {
            background: rgba(255, 255, 255, 0.95);
            backdrop-filter: blur(10px);
        }
        /* Custom scrollbar */
        ::-webkit-scrollbar {
            width: 6px;
        }
        ::-webkit-scrollbar-track {
            background: #f1f1f1;
        }
        ::-webkit-scrollbar-thumb {
            background: #cbd5e1;
            border-radius: 4px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: #94a3b8;
        }
    </style>
</head>
<body class="text-slate-800 min-h-screen flex flex-col justify-between">

    <!-- Header Navigation -->
    <header class="bg-white border-b border-slate-200 sticky top-0 z-30 shadow-sm">
        <div class="max-w-5xl mx-auto px-4 py-3 flex items-center justify-between">
            <div class="flex items-center space-x-3">
                <div class="w-10 h-10 rounded-xl bg-gradient-to-tr from-amber-500 to-orange-500 flex items-center justify-center text-white shadow-md shadow-orange-200">
                    <i class="fa-solid font-bold fa-utensils text-lg"></i>
                </div>
                <div>
                    <h1 class="font-bold text-lg leading-tight text-slate-900">Personal AI Chef</h1>
                    <p class="text-xs text-slate-500">เชฟส่วนตัว เนรมิตเมนูอร่อยจากของในตู้เย็น</p>
                </div>
            </div>

            <!-- Settings / Key Button -->
            <button onclick="openApiKeyModal()" class="flex items-center space-x-2 text-xs bg-slate-100 hover:bg-slate-200 text-slate-700 px-3 py-2 rounded-lg transition font-medium">
                <i class="fa-solid fa-key text-amber-500"></i>
                <span id="apiKeyStatusText">ตั้งค่า API Key</span>
            </button>
        </div>
    </header>

    <!-- Main Container -->
    <main class="max-w-5xl mx-auto px-4 py-6 w-full flex-grow grid grid-cols-1 lg:grid-cols-12 gap-6">
        
        <!-- Left Column: Input Form -->
        <div class="lg:col-span-5 space-y-5">
            
            <!-- Input Card -->
            <div class="bg-white p-5 rounded-2xl border border-slate-200 shadow-sm space-y-4">
                
                <!-- Input Mode Tabs -->
                <div class="flex rounded-xl bg-slate-100 p-1">
                    <button id="tabTextBtn" onclick="switchTab('text')" class="flex-1 py-2 rounded-lg text-sm font-medium transition flex items-center justify-center space-x-2 bg-white text-orange-600 shadow-sm">
                        <i class="fa-solid fa-pen"></i>
                        <span>พิมพ์วัตถุดิบ</span>
                    </button>
                    <button id="tabImageBtn" onclick="switchTab('image')" class="flex-1 py-2 rounded-lg text-sm font-medium text-slate-600 hover:text-slate-900 transition flex items-center justify-center space-x-2">
                        <i class="fa-solid fa-camera"></i>
                        <span>ถ่าย/อัปโหลดรูป</span>
                    </button>
                </div>

                <!-- Tab 1: Text Ingredients Input -->
                <div id="textInputSection" class="space-y-3">
                    <label class="block text-xs font-semibold text-slate-700 uppercase tracking-wider">วัตถุดิบที่มีในตู้เย็น</label>
                    <div class="relative">
                        <textarea id="ingredientsText" rows="3" placeholder="เช่น ไก่สด 200 กรัม, ไข่ไก่ 2 ฟอง, หอมใหญ่ half หัว, ซอสเทอริยากิ, มาม่า" class="w-full p-3 border border-slate-200 rounded-xl focus:ring-2 focus:ring-orange-500 focus:border-transparent outline-none text-sm transition"></textarea>
                    </div>

                    <!-- Quick Ingredient Add Chips -->
                    <div>
                        <span class="text-xs text-slate-500 font-medium">กดเพิ่มวัตถุดิบยอดฮิตอย่างไว:</span>
                        <div class="flex flex-wrap gap-1.5 mt-2">
                            <button type="button" onclick="addChip('ไข่ไก่')" class="text-xs bg-slate-100 hover:bg-orange-100 hover:text-orange-700 text-slate-600 px-2.5 py-1 rounded-full transition">+ ไข่ไก่</button>
                            <button type="button" onclick="addChip('อกไก่')" class="text-xs bg-slate-100 hover:bg-orange-100 hover:text-orange-700 text-slate-600 px-2.5 py-1 rounded-full transition">+ อกไก่</button>
                            <button type="button" onclick="addChip('หมูสับ')" class="text-xs bg-slate-100 hover:bg-orange-100 hover:text-orange-700 text-slate-600 px-2.5 py-1 rounded-full transition">+ หมูสับ</button>
                            <button type="button" onclick="addChip('หมูกรอบ')" class="text-xs bg-slate-100 hover:bg-orange-100 hover:text-orange-700 text-slate-600 px-2.5 py-1 rounded-full transition">+ หมูกรอบ</button>
                            <button type="button" onclick="addChip('ปลากระป๋อง')" class="text-xs bg-slate-100 hover:bg-orange-100 hover:text-orange-700 text-slate-600 px-2.5 py-1 rounded-full transition">+ ปลากระป๋อง</button>
                            <button type="button" onclick="addChip('บะหมี่กึ่งสำเร็จรูป')" class="text-xs bg-slate-100 hover:bg-orange-100 hover:text-orange-700 text-slate-600 px-2.5 py-1 rounded-full transition">+ มาม่า</button>
                            <button type="button" onclick="addChip('กระเทียม')" class="text-xs bg-slate-100 hover:bg-orange-100 hover:text-orange-700 text-slate-600 px-2.5 py-1 rounded-full transition">+ กระเทียม</button>
                            <button type="button" onclick="addChip('ผักกาดขาว')" class="text-xs bg-slate-100 hover:bg-orange-100 hover:text-orange-700 text-slate-600 px-2.5 py-1 rounded-full transition">+ ผักกาดขาว</button>
                        </div>
                    </div>
                </div>

                <!-- Tab 2: Image Upload Section -->
                <div id="imageInputSection" class="hidden space-y-3">
                    <label class="block text-xs font-semibold text-slate-700 uppercase tracking-wider">ถ่ายภาพตู้เย็น หรืออัปโหลดรูปวัตถุดิบ</label>
                    <div id="dropZone" class="border-2 border-dashed border-slate-300 hover:border-orange-400 rounded-xl p-6 text-center transition cursor-pointer bg-slate-50 flex flex-col items-center justify-center min-h-[160px]" onclick="document.getElementById('imageInput').click()">
                        
                        <div id="uploadPrompt" class="space-y-2">
                            <div class="w-12 h-12 rounded-full bg-orange-100 text-orange-500 flex items-center justify-center mx-auto text-xl">
                                <i class="fa-solid fa-cloud-arrow-up"></i>
                            </div>
                            <p class="text-sm font-medium text-slate-700">ลากไฟล์รูปมาวางที่นี่ หรือ <span class="text-orange-600 underline">คลิกอัปโหลด</span></p>
                            <p class="text-xs text-slate-400">รองรับไฟล์ JPG, PNG, WEBP (สูงสุด 10MB)</p>
                        </div>

                        <!-- Image Preview Container -->
                        <div id="imagePreviewContainer" class="hidden w-full relative">
                            <img id="imagePreview" class="max-h-48 rounded-lg mx-auto object-contain border border-slate-200 shadow-sm" alt="Ingredient preview">
                            <button type="button" onclick="removeImage(event)" class="absolute top-1 right-1 bg-red-500 text-white rounded-full w-7 h-7 flex items-center justify-center text-xs shadow-md hover:bg-red-600 transition">
                                <i class="fa-solid fa-xmark"></i>
                            </button>
                        </div>
                    </div>
                    <input type="file" id="imageInput" accept="image/*" class="hidden" onchange="handleFileSelect(event)">
                </div>

                <hr class="border-slate-100">

                <!-- Cooking Conditions Section -->
                <div class="space-y-3">
                    <label class="block text-xs font-semibold text-slate-700 uppercase tracking-wider">
                        <i class="fa-solid fa-sliders text-amber-500 mr-1"></i> เงื่อนไขและอุปกรณ์เพิ่มเติม
                    </label>

                    <!-- Equipment Tags -->
                    <div class="space-y-1.5">
                        <span class="text-xs text-slate-500">อุปกรณ์ที่มี:</span>
                        <div class="flex flex-wrap gap-1.5">
                            <label class="inline-flex items-center text-xs bg-slate-100 px-3 py-1.5 rounded-lg cursor-pointer hover:bg-slate-200 transition">
                                <input type="checkbox" name="equipment" value="ไมโครเวฟ" class="rounded text-orange-500 focus:ring-orange-400 mr-1.5"> ไมโครเวฟ
                            </label>
                            <label class="inline-flex items-center text-xs bg-slate-100 px-3 py-1.5 rounded-lg cursor-pointer hover:bg-slate-200 transition">
                                <input type="checkbox" name="equipment" value="หม้อทอดไร้น้ำมัน (Air Fryer)" class="rounded text-orange-500 focus:ring-orange-400 mr-1.5"> หม้อทอดไร้น้ำมัน
                            </label>
                            <label class="inline-flex items-center text-xs bg-slate-100 px-3 py-1.5 rounded-lg cursor-pointer hover:bg-slate-200 transition">
                                <input type="checkbox" name="equipment" value="กระทะ / เตาแก๊ส" checked class="rounded text-orange-500 focus:ring-orange-400 mr-1.5"> กระทะ/เตาแก๊ส
                            </label>
                            <label class="inline-flex items-center text-xs bg-slate-100 px-3 py-1.5 rounded-lg cursor-pointer hover:bg-slate-200 transition">
                                <input type="checkbox" name="equipment" value="หม้อหุงข้าว" class="rounded text-orange-500 focus:ring-orange-400 mr-1.5"> หม้อหุงข้าว
                            </label>
                        </div>
                    </div>

                    <!-- Dietary Restrictions Tags -->
                    <div class="space-y-1.5">
                        <span class="text-xs text-slate-500">รสชาติ / โภชนาการ:</span>
                        <div class="flex flex-wrap gap-1.5">
                            <label class="inline-flex items-center text-xs bg-slate-100 px-3 py-1.5 rounded-lg cursor-pointer hover:bg-slate-200 transition">
                                <input type="checkbox" name="diet" value="ไม่เผ็ด" class="rounded text-orange-500 focus:ring-orange-400 mr-1.5"> ไม่เผ็ด
                            </label>
                            <label class="inline-flex items-center text-xs bg-slate-100 px-3 py-1.5 rounded-lg cursor-pointer hover:bg-slate-200 transition">
                                <input type="checkbox" name="diet" value="ทำง่ายใน 15 นาที" class="rounded text-orange-500 focus:ring-orange-400 mr-1.5"> ทำด่วน < 15 นาที
                            </label>
                            <label class="inline-flex items-center text-xs bg-slate-100 px-3 py-1.5 rounded-lg cursor-pointer hover:bg-slate-200 transition">
                                <input type="checkbox" name="diet" value="แคลอรีต่ำ / คลีน" class="rounded text-orange-500 focus:ring-orange-400 mr-1.5"> เมนูคลีน
                            </label>
                            <label class="inline-flex items-center text-xs bg-slate-100 px-3 py-1.5 rounded-lg cursor-pointer hover:bg-slate-200 transition">
                                <input type="checkbox" name="diet" value="มังสวิรัติ" class="rounded text-orange-500 focus:ring-orange-400 mr-1.5"> มังสวิรัติ
                            </label>
                        </div>
                    </div>

                    <!-- Extra Condition Input -->
                    <input type="text" id="extraCondition" placeholder="เงื่อนไขอื่นๆ เช่น งดน้ำมัน, ขอเมนูคลายหนาว" class="w-full p-2.5 border border-slate-200 rounded-xl focus:ring-2 focus:ring-orange-500 focus:border-transparent outline-none text-xs transition">
                </div>

                <!-- Generate Button -->
                <button id="generateBtn" onclick="generateRecipe()" class="w-full bg-gradient-to-r from-orange-500 to-amber-500 hover:from-orange-600 hover:to-amber-600 text-white font-semibold py-3.5 px-4 rounded-xl shadow-lg shadow-orange-200 hover:shadow-orange-300 transition flex items-center justify-center space-x-2 text-sm">
                    <i class="fa-solid fa-wand-magic-sparkles text-amber-200"></i>
                    <span>เนรมิตเมนูโดย AI เชฟ</span>
                </button>
            </div>

            <!-- Saved Recipes Mini List -->
            <div class="bg-white p-4 rounded-2xl border border-slate-200 shadow-sm">
                <div class="flex items-center justify-between mb-3">
                    <h3 class="text-xs font-semibold text-slate-700 uppercase tracking-wider flex items-center">
                        <i class="fa-solid fa-bookmark text-amber-500 mr-1.5"></i> เมนูบันทึกไว้
                    </h3>
                    <span id="savedCount" class="text-xs bg-slate-100 text-slate-600 px-2 py-0.5 rounded-full font-medium">0</span>
                </div>
                <div id="savedRecipesList" class="space-y-2 max-h-40 overflow-y-auto text-xs text-slate-500">
                    <p class="text-center py-4 text-slate-400">ยังไม่มีเมนูที่บันทึกไว้</p>
                </div>
            </div>

        </div>

        <!-- Right Column: Results & AI Chef Output -->
        <div class="lg:col-span-7 space-y-4">
            
            <!-- Default Empty State -->
            <div id="emptyState" class="bg-white rounded-2xl border border-slate-200 p-8 text-center min-h-[480px] flex flex-col items-center justify-center space-y-4 shadow-sm">
                <div class="w-20 h-20 rounded-full bg-amber-50 border-4 border-amber-100 text-amber-500 flex items-center justify-center text-3xl">
                    <i class="fa-solid fa-hat-cowboy-side"></i>
                </div>
                <div class="max-w-md space-y-1">
                    <h3 class="font-bold text-slate-800 text-lg">พร้อมทำอาหารหรือยังครับ?</h3>
                    <p class="text-xs text-slate-500 leading-relaxed">พิมพ์รายการวัตถุดิบที่คุณมีในตู้เย็น หรือถ่ายภาพตู้เย็น แล้วให้ AI เชฟช่วยคำนวณสูตรอาหารแสนอร่อยที่ทำได้จริงทันที</p>
                </div>
            </div>

            <!-- Loading Skeleton Indicator -->
            <div id="loadingState" class="hidden bg-white rounded-2xl border border-slate-200 p-8 min-h-[480px] flex flex-col items-center justify-center space-y-4 shadow-sm">
                <div class="relative">
                    <div class="w-16 h-16 border-4 border-orange-200 border-t-orange-500 rounded-full animate-spin"></div>
                    <div class="absolute inset-0 flex items-center justify-center text-orange-500 text-lg">
                        <i class="fa-solid fa-utensils"></i>
                    </div>
                </div>
                <div class="text-center space-y-1">
                    <h3 class="font-bold text-slate-800">AI เชฟกำลังวิเคราะห์และคิดสูตร...</h3>
                    <p class="text-xs text-slate-500">กำลังประมวลผลวัตถุดิบและจับคู่เครื่องปรุงที่ดีที่สุด</p>
                </div>
            </div>

            <!-- Recipe Result Card -->
            <div id="recipeResult" class="hidden bg-white rounded-2xl border border-slate-200 overflow-hidden shadow-sm space-y-0">
                
                <!-- Recipe Header Banner -->
                <div class="bg-gradient-to-r from-slate-900 via-slate-800 to-slate-900 text-white p-6 relative">
                    <div class="flex items-start justify-between">
                        <div class="space-y-1 max-w-[80%]">
                            <span class="inline-block bg-orange-500/20 text-orange-300 border border-orange-500/30 text-[11px] px-2.5 py-0.5 rounded-full font-medium mb-1">
                                <i class="fa-solid fa-sparkles mr-1"></i> สูตรอาหารโดย AI เชฟ
                            </span>
                            <h2 id="recipeName" class="text-2xl font-bold tracking-tight text-white leading-snug">ชื่อเมนูอาหาร</h2>
                        </div>

                        <!-- Action Controls -->
                        <div class="flex items-center space-x-2">
                            <button onclick="toggleSpeech()" id="speakBtn" title="อ่านขั้นตอนทำอาหาร" class="w-9 h-9 rounded-xl bg-white/10 hover:bg-white/20 text-white flex items-center justify-center transition">
                                <i class="fa-solid fa-volume-high text-sm"></i>
                            </button>
                            <button onclick="saveCurrentRecipe()" title="บันทึกเมนูนี้" class="w-9 h-9 rounded-xl bg-white/10 hover:bg-white/20 text-amber-300 flex items-center justify-center transition">
                                <i class="fa-solid fa-bookmark text-sm"></i>
                            </button>
                        </div>
                    </div>

                    <!-- Recipe Meta Tags -->
                    <div class="flex flex-wrap gap-4 mt-4 pt-4 border-t border-slate-700/60 text-xs text-slate-300">
                        <div class="flex items-center space-x-1.5">
                            <i class="fa-regular fa-clock text-orange-400"></i>
                            <span id="recipeTime">15-20 นาที</span>
                        </div>
                        <div class="flex items-center space-x-1.5">
                            <i class="fa-solid fa-fire text-orange-400"></i>
                            <span id="recipeDifficulty">ระดับ: ง่าย</span>
                        </div>
                        <div class="flex items-center space-x-1.5">
                            <i class="fa-solid fa-kitchen-set text-orange-400"></i>
                            <span id="recipeEquipment">กระทะ, เตาแก๊ส</span>
                        </div>
                    </div>
                </div>

                <!-- Recipe Details Content -->
                <div class="p-6 space-y-6">
                    
                    <!-- Ingredients List -->
                    <div class="space-y-3">
                        <h3 class="text-sm font-bold text-slate-800 flex items-center">
                            <span class="w-7 h-7 rounded-lg bg-orange-100 text-orange-600 flex items-center justify-center mr-2 text-xs">
                                <i class="fa-solid fa-carrot"></i>
                            </span>
                            วัตถุดิบและสัดส่วนที่ใช้
                        </h3>
                        <div id="recipeIngredients" class="grid grid-cols-1 md:grid-cols-2 gap-2 text-xs text-slate-700 bg-slate-50 p-4 rounded-xl border border-slate-100">
                            <!-- Populated dynamically -->
                        </div>
                    </div>

                    <!-- Step by Step Instructions -->
                    <div class="space-y-3">
                        <h3 class="text-sm font-bold text-slate-800 flex items-center">
                            <span class="w-7 h-7 rounded-lg bg-emerald-100 text-emerald-600 flex items-center justify-center mr-2 text-xs">
                                <i class="fa-solid fa-list-check"></i>
                            </span>
                            วิธีทำ (ทีละขั้นตอน)
                        </h3>
                        <div id="recipeSteps" class="space-y-2.5 text-xs text-slate-700">
                            <!-- Populated dynamically -->
                        </div>
                    </div>

                    <!-- Chef's Pro Tips -->
                    <div class="bg-amber-50 border border-amber-200/70 rounded-xl p-4 space-y-1">
                        <h4 class="text-xs font-bold text-amber-800 flex items-center">
                            <i class="fa-solid fa-lightbulb text-amber-500 mr-1.5"></i> เคล็ดลับความอร่อยจาก AI เชฟ
                        </h4>
                        <p id="recipeTip" class="text-xs text-amber-900/90 leading-relaxed">
                            -
                        </p>
                    </div>

                </div>

            </div>

        </div>

    </main>

    <!-- Footer -->
    <footer class="bg-white border-t border-slate-200 py-4 mt-8 text-center text-xs text-slate-400">
        <p>Personal AI Chef App • Powered by Gemini AI (Google)</p>
    </footer>

    <!-- API Key Modal -->
    <div id="apiKeyModal" class="fixed inset-0 bg-slate-900/50 backdrop-blur-sm flex items-center justify-center z-50 hidden">
        <div class="bg-white rounded-2xl max-w-md w-full p-6 shadow-xl space-y-4 m-4">
            <div class="flex justify-between items-center">
                <h3 class="font-bold text-slate-900 text-base flex items-center">
                    <i class="fa-solid fa-key text-amber-500 mr-2"></i> กำหนด Google Gemini API Key
                </h3>
                <button onclick="closeApiKeyModal()" class="text-slate-400 hover:text-slate-600 text-sm">
                    <i class="fa-solid fa-xmark"></i>
                </button>
            </div>
            <p class="text-xs text-slate-500 leading-relaxed">
                ใส่ GOOGLE_API_KEY เพื่อเชื่อมต่อกับโมเดล Gemini โดยตรง (หากเว้นว่างไว้ ระบบจะเรียกใช้คีย์ในสภาพแวดล้อมอัตโนมัติ)
            </p>
            <div>
                <input type="password" id="userApiKeyInput" placeholder="AIzaSy..." class="w-full p-3 border border-slate-200 rounded-xl focus:ring-2 focus:ring-orange-500 outline-none text-xs">
            </div>
            <div class="flex justify-end space-x-2">
                <button onclick="closeApiKeyModal()" class="px-4 py-2 rounded-xl text-xs font-medium text-slate-600 hover:bg-slate-100 transition">ยกเลิก</button>
                <button onclick="saveApiKey()" class="px-4 py-2 rounded-xl text-xs font-medium bg-orange-500 text-white hover:bg-orange-600 transition shadow-sm">บันทึก Key</button>
            </div>
        </div>
    </div>

    <script>
        // State Management
        let currentTab = 'text'; // 'text' or 'image'
        let selectedBase64Image = null;
        let selectedMimeType = null;
        let currentRecipeData = null;
        let isSpeaking = false;

        // Initialize App
        window.onload = function() {
            checkApiKeyStatus();
            loadSavedRecipes();
            setupDragAndDrop();
        };

        // Tab Switching Logic
        function switchTab(tab) {
            currentTab = tab;
            const textBtn = document.getElementById('tabTextBtn');
            const imageBtn = document.getElementById('tabImageBtn');
            const textSec = document.getElementById('textInputSection');
            const imageSec = document.getElementById('imageInputSection');

            if (tab === 'text') {
                textBtn.className = "flex-1 py-2 rounded-lg text-sm font-medium transition flex items-center justify-center space-x-2 bg-white text-orange-600 shadow-sm";
                imageBtn.className = "flex-1 py-2 rounded-lg text-sm font-medium text-slate-600 hover:text-slate-900 transition flex items-center justify-center space-x-2";
                textSec.classList.remove('hidden');
                imageSec.classList.add('hidden');
            } else {
                imageBtn.className = "flex-1 py-2 rounded-lg text-sm font-medium transition flex items-center justify-center space-x-2 bg-white text-orange-600 shadow-sm";
                textBtn.className = "flex-1 py-2 rounded-lg text-sm font-medium text-slate-600 hover:text-slate-900 transition flex items-center justify-center space-x-2";
                imageSec.classList.remove('hidden');
                textSec.classList.add('hidden');
            }
        }

        // Quick add ingredient chips
        function addChip(item) {
            const textarea = document.getElementById('ingredientsText');
            let current = textarea.value.trim();
            if (current.length > 0 && !current.endsWith(',')) {
                current += ', ';
            }
            textarea.value = current + item;
        }

        // Drag & Drop Handling
        function setupDragAndDrop() {
            const dropZone = document.getElementById('dropZone');
            
            ['dragenter', 'dragover', 'dragleave', 'drop'].forEach(eventName => {
                dropZone.addEventListener(eventName, preventDefaults, false);
            });

            function preventDefaults(e) {
                e.preventDefault();
                e.stopPropagation();
            }

            ['dragenter', 'dragover'].forEach(eventName => {
                dropZone.addEventListener(eventName, () => dropZone.classList.add('drag-active'), false);
            });

            ['dragleave', 'drop'].forEach(eventName => {
                dropZone.addEventListener(eventName, () => dropZone.classList.remove('drag-active'), false);
            });

            dropZone.addEventListener('drop', (e) => {
                const dt = e.dataTransfer;
                const files = dt.files;
                if (files.length > 0) {
                    processFile(files[0]);
                }
            });
        }

        function handleFileSelect(e) {
            const files = e.target.files;
            if (files.length > 0) {
                processFile(files[0]);
            }
        }

        function processFile(file) {
            if (!file.type.startsWith('image/')) {
                alert('โปรดเลือกไฟล์รูปภาพเท่านั้นครับ');
                return;
            }

            selectedMimeType = file.type;
            const reader = new FileReader();
            reader.onload = function(e) {
                const fullBase64 = e.target.result;
                // Extract base64 part
                selectedBase64Image = fullBase64.split(',')[1];

                // Show preview
                document.getElementById('imagePreview').src = fullBase64;
                document.getElementById('uploadPrompt').classList.add('hidden');
                document.getElementById('imagePreviewContainer').classList.remove('hidden');
            };
            reader.readAsDataURL(file);
        }

        function removeImage(e) {
            e.stopPropagation();
            selectedBase64Image = null;
            selectedMimeType = null;
            document.getElementById('imageInput').value = '';
            document.getElementById('imagePreview').src = '';
            document.getElementById('uploadPrompt').classList.remove('hidden');
            document.getElementById('imagePreviewContainer').classList.add('hidden');
        }

        // API Key Modal & Storage
        function openApiKeyModal() {
            const key = localStorage.getItem('gemini_user_api_key') || '';
            document.getElementById('userApiKeyInput').value = key;
            document.getElementById('apiKeyModal').classList.remove('hidden');
        }

        function closeApiKeyModal() {
            document.getElementById('apiKeyModal').classList.add('hidden');
        }

        function saveApiKey() {
            const key = document.getElementById('userApiKeyInput').value.trim();
            if (key) {
                localStorage.setItem('gemini_user_api_key', key);
            } else {
                localStorage.removeItem('gemini_user_api_key');
            }
            checkApiKeyStatus();
            closeApiKeyModal();
        }

        function checkApiKeyStatus() {
            const key = localStorage.getItem('gemini_user_api_key');
            const statusText = document.getElementById('apiKeyStatusText');
            if (key) {
                statusText.textContent = 'เปิดใช้งาน API Key แล้ว';
                statusText.classList.add('text-emerald-600');
            } else {
                statusText.textContent = 'ตั้งค่า API Key';
                statusText.classList.remove('text-emerald-600');
            }
        }

        // Main Recipe Generation with Gemini API
        async function generateRecipe() {
            const ingredientsInput = document.getElementById('ingredientsText').value.trim();
            
            if (currentTab === 'text' && !ingredientsInput) {
                alert('กรุณาพิมพ์ระบุวัตถุดิบที่มีอย่างน้อย 1 อย่างครับ');
                return;
            }
            if (currentTab === 'image' && !selectedBase64Image) {
                alert('กรุณาอัปโหลดรูปภาพตู้เย็นหรือวัตถุดิบก่อนครับ');
                return;
            }

            // Gather Equipment & Conditions
            const equipmentChecked = Array.from(document.querySelectorAll('input[name="equipment"]:checked')).map(el => el.value);
            const dietChecked = Array.from(document.querySelectorAll('input[name="diet"]:checked')).map(el => el.value);
            const extraCond = document.getElementById('extraCondition').value.trim();

            let conditionsCombined = [...equipmentChecked, ...dietChecked];
            if (extraCond) conditionsCombined.push(extraCond);
            const finalConditions = conditionsCombined.length > 0 ? conditionsCombined.join(', ') : 'ไม่มีเงื่อนไขพิเศษ';

            // Show Loading UI
            document.getElementById('emptyState').classList.add('hidden');
            document.getElementById('recipeResult').classList.add('hidden');
            document.getElementById('loadingState').classList.remove('hidden');

            // Construct System Instructions & Prompt
            const systemPrompt = `คุณคือ 'AI เชฟส่วนตัว' ผู้เชี่ยวชาญด้านการประกอบอาหารอย่างสร้างสรรค์จากวัตถุดิบที่มีในบ้าน
คุณต้องวิเคราะห์วัตถุดิบและแนะนำเมนูอาหาร 1 เมนูที่สามารถทำได้จริง อร่อย และสอดคล้องกับเงื่อนไขของผู้ใช้`;

            const userPromptText = `วัตถุดิบที่มี: ${currentTab === 'text' ? ingredientsInput : 'โปรดวิเคราะห์วัตถุดิบจากรูปภาพที่แนบมานี้'}
เงื่อนไขและอุปกรณ์ที่มี: ${finalConditions}

กรุณาส่งออกผลลัพธ์ในรูปแบบโครงสร้าง JSON ดังต่อไปนี้เท่านั้น (ไม่ต้องใส่ markdown backticks หรือข้อความอื่น):
{
  "menuName": "ชื่อเมนูอาหารน่าทาน",
  "prepTime": "เช่น 15-20 นาที",
  "difficulty": "เช่น ง่าย / ปานกลาง",
  "equipment": "เช่น กระทะ, เตาแก๊ส",
  "ingredients": [
    "วัตถุดิบที่ 1 (ปริมาณสัดส่วน)",
    "วัตถุดิบที่ 2 (ปริมาณสัดส่วน)"
  ],
  "steps": [
    "ขั้นตอนที่ 1...",
    "ขั้นตอนที่ 2..."
  ],
  "chefTip": "เคล็ดลับเด็ดในการปรุง"
}`;

            const customApiKey = localStorage.getItem('gemini_user_api_key') || "";
            const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-3-flash-preview:generateContent?key=${customApiKey}`;

            let parts = [{ text: userPromptText }];
            if (currentTab === 'image' && selectedBase64Image) {
                parts.push({
                    inlineData: {
                        mimeType: selectedMimeType || 'image/jpeg',
                        data: selectedBase64Image
                    }
                });
            }

            const payload = {
                contents: [{ parts: parts }],
                systemInstruction: { parts: [{ text: systemPrompt }] },
                generationConfig: {
                    responseMimeType: "application/json"
                }
            };

            try {
                const response = await fetchWithRetry(apiUrl, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(payload)
                });

                const data = await response.json();
                const jsonText = data.candidates?.[0]?.content?.parts?.[0]?.text;

                if (!jsonText) {
                    throw new Error('ไม่ได้รับข้อมูลผลลัพธ์จาก AI');
                }

                const recipeObj = JSON.parse(jsonText);
                currentRecipeData = recipeObj;
                renderRecipe(recipeObj);

            } catch (err) {
                console.error('API Error:', err);
                alert('เกิดข้อผิดพลาดในการประมวลผล: ' + err.message + '\n(หากใช้ Custom API Key โปรดตรวจสอบว่าคีย์ถูกต้อง)');
                document.getElementById('loadingState').classList.add('hidden');
                document.getElementById('emptyState').classList.remove('hidden');
            }
        }

        // Fetch with exponential backoff helper
        async function fetchWithRetry(url, options, retries = 3, backoff = 1000) {
            try {
                const res = await fetch(url, options);
                if (!res.ok) throw new Error(`HTTP Error Status: ${res.status}`);
                return res;
            } catch (err) {
                if (retries > 0) {
                    await new Promise(r => setTimeout(r, backoff));
                    return fetchWithRetry(url, options, retries - 1, backoff * 2);
                }
                throw err;
            }
        }

        // Render Recipe JSON to UI
        function renderRecipe(recipe) {
            document.getElementById('loadingState').classList.add('hidden');
            document.getElementById('recipeResult').classList.remove('hidden');

            document.getElementById('recipeName').textContent = recipe.menuName || 'เมนูจานเด็ด';
            document.getElementById('recipeTime').textContent = recipe.prepTime || '15-20 นาที';
            document.getElementById('recipeDifficulty').textContent = 'ระดับ: ' + (recipe.difficulty || 'ง่าย');
            document.getElementById('recipeEquipment').textContent = recipe.equipment || 'อุปกรณ์ทั่วไป';

            // Ingredients
            const ingContainer = document.getElementById('recipeIngredients');
            ingContainer.innerHTML = '';
            (recipe.ingredients || []).forEach(item => {
                const div = document.createElement('div');
                div.className = 'flex items-center space-x-2 bg-white p-2 rounded-lg border border-slate-100 shadow-2xs';
                div.innerHTML = `<i class="fa-solid fa-circle-check text-orange-500 text-[10px]"></i> <span>${item}</span>`;
                ingContainer.appendChild(div);
            });

            // Steps
            const stepsContainer = document.getElementById('recipeSteps');
            stepsContainer.innerHTML = '';
            (recipe.steps || []).forEach((step, idx) => {
                const stepEl = document.createElement('label');
                stepEl.className = 'flex items-start space-x-3 bg-slate-50 hover:bg-orange-50/50 p-3 rounded-xl border border-slate-200/80 cursor-pointer transition';
                stepEl.innerHTML = `
                    <input type="checkbox" class="mt-0.5 rounded text-orange-500 focus:ring-orange-400">
                    <div class="flex-1">
                        <span class="font-bold text-slate-900 mr-1">ขั้นตอนที่ ${idx + 1}:</span>
                        <span>${step}</span>
                    </div>
                `;
                stepsContainer.appendChild(stepEl);
            });

            // Tip
            document.getElementById('recipeTip').textContent = recipe.chefTip || 'เสิร์ฟร้อนๆ เพื่อความอร่อยกลมกล่อมที่สุด!';
        }

        // Speech Synthesis (Read aloud recipe steps)
        function toggleSpeech() {
            if (!currentRecipeData) return;

            if (isSpeaking) {
                window.speechSynthesis.cancel();
                isSpeaking = false;
                document.getElementById('speakBtn').classList.remove('bg-orange-500');
                return;
            }

            const textToSpeak = `เมนู ${currentRecipeData.menuName} วิธีทำดังนี้ ${currentRecipeData.steps.join(' ต่อจากนั้น ')}`;
            const utterance = new SpeechSynthesisUtterance(textToSpeak);
            utterance.lang = 'th-TH';
            utterance.rate = 0.95;

            utterance.onend = () => {
                isSpeaking = false;
                document.getElementById('speakBtn').classList.remove('bg-orange-500');
            };

            window.speechSynthesis.speak(utterance);
            isSpeaking = true;
            document.getElementById('speakBtn').classList.add('bg-orange-500');
        }

        // Save Recipe to LocalStorage
        function saveCurrentRecipe() {
            if (!currentRecipeData) return;

            let saved = JSON.parse(localStorage.getItem('ai_chef_saved_recipes') || '[]');
            // Check duplicate
            if (saved.some(r => r.menuName === currentRecipeData.menuName)) {
                alert('เมนูนี้อยู่ในรายการบันทึกแล้วครับ');
                return;
            }

            saved.unshift(currentRecipeData);
            localStorage.setItem('ai_chef_saved_recipes', JSON.stringify(saved));
            loadSavedRecipes();
            alert('บันทึกเมนูสำเร็จ!');
        }

        function loadSavedRecipes() {
            const saved = JSON.parse(localStorage.getItem('ai_chef_saved_recipes') || '[]');
            document.getElementById('savedCount').textContent = saved.length;
            const container = document.getElementById('savedRecipesList');

            if (saved.length === 0) {
                container.innerHTML = `<p class="text-center py-4 text-slate-400">ยังไม่มีเมนูที่บันทึกไว้</p>`;
                return;
            }

            container.innerHTML = '';
            saved.forEach((r, idx) => {
                const item = document.createElement('div');
                item.className = 'flex items-center justify-between bg-slate-50 p-2.5 rounded-xl hover:bg-orange-50 transition border border-slate-100 cursor-pointer';
                item.onclick = () => {
                    currentRecipeData = r;
                    renderRecipe(r);
                };

                item.innerHTML = `
                    <div class="truncate font-medium text-slate-700">
                        <i class="fa-solid fa-utensils text-orange-500 mr-1.5"></i> ${r.menuName}
                    </div>
                    <button onclick="deleteSavedRecipe(event, ${idx})" class="text-slate-400 hover:text-red-500 px-1.5 py-0.5">
                        <i class="fa-solid fa-trash-can"></i>
                    </button>
                `;
                container.appendChild(item);
            });
        }

        function deleteSavedRecipe(e, index) {
            e.stopPropagation();
            let saved = JSON.parse(localStorage.getItem('ai_chef_saved_recipes') || '[]');
            saved.splice(index, 1);
            localStorage.setItem('ai_chef_saved_recipes', JSON.stringify(saved));
            loadSavedRecipes();
        }
    </script>
</body>
</html>
