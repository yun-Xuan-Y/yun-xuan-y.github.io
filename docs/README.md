<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>家庭点餐小程序</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://cdn.jsdelivr.net/npm/font-awesome@4.7.0/css/font-awesome.min.css" rel="stylesheet">
    
    <!-- 配置Tailwind自定义颜色和字体 -->
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    colors: {
                        primary: '#FF7A00', // 温暖的橙色作为主色调
                        secondary: '#4CAF50', // 绿色作为辅助色，用于确认按钮
                        neutral: '#F5F5F5', // 浅灰色作为背景
                        dark: '#333333', // 深灰色用于文字
                    },
                    fontFamily: {
                        sans: ['Inter', 'system-ui', 'sans-serif'],
                    },
                }
            }
        }
    </script>
    
    <style type="text/tailwindcss">
        @layer utilities {
            .content-auto {
                content-visibility: auto;
            }
            .scrollbar-hide {
                -ms-overflow-style: none;
                scrollbar-width: none;
            }
            .scrollbar-hide::-webkit-scrollbar {
                display: none;
            }
            .card-shadow {
                box-shadow: 0 4px 12px rgba(0, 0, 0, 0.08);
            }
        }
    </style>
</head>
<body class="bg-neutral min-h-screen font-sans text-dark">
    <!-- 顶部导航栏 -->
    <header class="sticky top-0 z-50 bg-white shadow-md">
        <div class="container mx-auto px-4 py-3 flex justify-between items-center">
            <h1 class="text-[clamp(1.2rem,5vw,1.8rem)] font-bold text-primary flex items-center">
                <i class="fa fa-cutlery mr-2"></i>家庭点餐
            </h1>
            <div class="relative">
                <button id="cartBtn" class="text-dark hover:text-primary transition-colors duration-300 relative">
                    <i class="fa fa-shopping-cart text-xl"></i>
                    <span id="cartCount" class="absolute -top-2 -right-2 bg-secondary text-white text-xs rounded-full h-5 w-5 flex items-center justify-center">0</span>
                </button>
            </div>
        </div>
    </header>

    <main class="container mx-auto px-4 py-6">
        <!-- 搜索栏 -->
        <div class="mb-6 relative">
            <input type="text" id="searchInput" placeholder="搜索菜品..." 
                class="w-full py-3 px-5 pr-10 rounded-full bg-white shadow-sm focus:outline-none focus:ring-2 focus:ring-primary/50 transition-all">
            <i class="fa fa-search absolute right-4 top-1/2 -translate-y-1/2 text-gray-400"></i>
        </div>

        <!-- 分类标签 -->
        <div class="mb-6 overflow-x-auto scrollbar-hide">
            <div class="flex space-x-2 pb-2 min-w-max">
                <button class="category-btn bg-primary text-white px-4 py-2 rounded-full text-sm font-medium transition-all">全部</button>
                <button class="category-btn bg-white text-dark px-4 py-2 rounded-full text-sm font-medium hover:bg-primary/10 transition-all">家常菜</button>
                <button class="category-btn bg-white text-dark px-4 py-2 rounded-full text-sm font-medium hover:bg-primary/10 transition-all">肉类</button>
                <button class="category-btn bg-white text-dark px-4 py-2 rounded-full text-sm font-medium hover:bg-primary/10 transition-all">蔬菜</button>
                <button class="category-btn bg-white text-dark px-4 py-2 rounded-full text-sm font-medium hover:bg-primary/10 transition-all">汤品</button>
                <button class="category-btn bg-white text-dark px-4 py-2 rounded-full text-sm font-medium hover:bg-primary/10 transition-all">主食</button>
                <button class="category-btn bg-white text-dark px-4 py-2 rounded-full text-sm font-medium hover:bg-primary/10 transition-all">小吃</button>
            </div>
        </div>

        <!-- 菜品列表 -->
        <h2 class="text-xl font-bold mb-4">今日菜单</h2>
        <div id="dishesContainer" class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-4 mb-8">
            <!-- 菜品卡片会通过JavaScript动态生成 -->
        </div>
    </main>

    <!-- 购物车侧边栏 -->
    <div id="cartSidebar" class="fixed inset-y-0 right-0 w-full sm:w-96 bg-white shadow-2xl transform translate-x-full transition-transform duration-300 z-50 flex flex-col">
        <div class="p-4 border-b flex justify-between items-center">
            <h2 class="text-xl font-bold">我的订单</h2>
            <button id="closeCartBtn" class="text-gray-500 hover:text-dark">
                <i class="fa fa-times text-xl"></i>
            </button>
        </div>
        
        <div id="cartItems" class="flex-1 overflow-y-auto p-4 space-y-4">
            <!-- 购物车项目会通过JavaScript动态生成 -->
            <div class="text-center text-gray-500 py-10">
                <i class="fa fa-shopping-cart text-4xl mb-2 opacity-30"></i>
                <p>购物车还是空的</p>
                <p class="text-sm">快去添加美味菜品吧！</p>
            </div>
        </div>
        
        <div class="p-4 border-t">
            <div class="flex justify-between mb-4">
                <span class="font-medium">总计:</span>
                <span id="cartTotal" class="font-bold text-xl">¥0.00</span>
            </div>
            <button id="submitOrderBtn" class="w-full bg-secondary hover:bg-secondary/90 text-white py-3 rounded-lg font-medium transition-colors flex items-center justify-center">
                <i class="fa fa-check mr-2"></i>提交订单
            </button>
        </div>
    </div>

    <!-- 订单成功弹窗 -->
    <div id="orderSuccessModal" class="fixed inset-0 bg-black/50 z-50 flex items-center justify-center hidden">
        <div class="bg-white rounded-xl p-6 max-w-md w-full mx-4 transform transition-all scale-95 opacity-0" id="modalContent">
            <div class="text-center">
                <div class="w-16 h-16 bg-green-100 rounded-full flex items-center justify-center mx-auto mb-4">
                    <i class="fa fa-check text-2xl text-secondary"></i>
                </div>
                <h3 class="text-xl font-bold mb-2">订单提交成功！</h3>
                <p class="text-gray-600 mb-6">厨房已经收到您的订单，正在准备中...</p>
                <button id="closeModalBtn" class="bg-primary hover:bg-primary/90 text-white py-2 px-6 rounded-lg font-medium transition-colors">
                    确定
                </button>
            </div>
        </div>
    </div>

    <script>
        // 菜品数据
        const dishes = [
            {
                id: 1,
                name: "宫保鸡丁",
                image: "https://picsum.photos/seed/dish1/300/200",
                price: 38,
                category: "家常菜",
                description: "经典川菜，鸡肉鲜嫩，花生酥脆，微辣可口"
            },
            {
                id: 2,
                name: "鱼香肉丝",
                image: "https://picsum.photos/seed/dish2/300/200",
                price: 32,
                category: "家常菜",
                description: "酸甜可口，肉丝鲜嫩，下饭神器"
            },
            {
                id: 3,
                name: "红烧肉",
                image: "https://picsum.photos/seed/dish3/300/200",
                price: 48,
                category: "肉类",
                description: "肥而不腻，入口即化，酱香浓郁"
            },
            {
                id: 4,
                name: "清炒时蔬",
                image: "https://picsum.photos/seed/dish4/300/200",
                price: 22,
                category: "蔬菜",
                description: "新鲜蔬菜，清淡爽口，营养健康"
            },
            {
                id: 5,
                name: "西红柿鸡蛋汤",
                image: "https://picsum.photos/seed/dish5/300/200",
                price: 18,
                category: "汤品",
                description: "家常汤品，酸甜可口，老少皆宜"
            },
            {
                id: 6,
                name: "扬州炒饭",
                image: "https://picsum.photos/seed/dish6/300/200",
                price: 26,
                category: "主食",
                description: "米饭松软，配料丰富，香气扑鼻"
            },
            {
                id: 7,
                name: "麻婆豆腐",
                image: "https://picsum.photos/seed/dish7/300/200",
                price: 28,
                category: "家常菜",
                description: "麻辣鲜香，豆腐嫩滑，下饭神器"
            },
            {
                id: 8,
                name: "炸薯条",
                image: "https://picsum.photos/seed/dish8/300/200",
                price: 16,
                category: "小吃",
                description: "外酥里嫩，金黄诱人，蘸番茄酱更佳"
            },
            {
                id: 9,
                name: "水煮鱼",
                image: "https://picsum.photos/seed/dish9/300/200",
                price: 58,
                category: "肉类",
                description: "麻辣鲜香，鱼肉嫩滑，回味无穷"
            }
        ];

        // 购物车数据
        let cart = [];

        // DOM元素
        const dishesContainer = document.getElementById('dishesContainer');
        const cartBtn = document.getElementById('cartBtn');
        const closeCartBtn = document.getElementById('closeCartBtn');
        const cartSidebar = document.getElementById('cartSidebar');
        const cartItems = document.getElementById('cartItems');
        const cartCount = document.getElementById('cartCount');
        const cartTotal = document.getElementById('cartTotal');
        const submitOrderBtn = document.getElementById('submitOrderBtn');
        const orderSuccessModal = document.getElementById('orderSuccessModal');
        const modalContent = document.getElementById('modalContent');
        const closeModalBtn = document.getElementById('closeModalBtn');
        const searchInput = document.getElementById('searchInput');
        const categoryBtns = document.querySelectorAll('.category-btn');

        // 初始化页面
        function init() {
            renderDishes(dishes);
            setupEventListeners();
        }

        // 渲染菜品列表
        function renderDishes(dishesToRender) {
            dishesContainer.innerHTML = '';
            
            if (dishesToRender.length === 0) {
                dishesContainer.innerHTML = `
                    <div class="col-span-full text-center py-10">
                        <i class="fa fa-search text-4xl mb-2 text-gray-300"></i>
                        <p class="text-gray-500">没有找到匹配的菜品</p>
                    </div>
                `;
                return;
            }
            
            dishesToRender.forEach(dish => {
                const dishCard = document.createElement('div');
                dishCard.className = 'bg-white rounded-xl overflow-hidden card-shadow hover:shadow-lg transition-shadow duration-300';
                dishCard.innerHTML = `
                    <div class="relative">
                        <img src="${dish.image}" alt="${dish.name}" class="w-full h-48 object-cover">
                        <span class="absolute top-3 right-3 bg-primary text-white text-sm font-bold px-2 py-1 rounded">¥${dish.price}</span>
                    </div>
                    <div class="p-4">
                        <h3 class="font-bold text-lg mb-1">${dish.name}</h3>
                        <p class="text-gray-500 text-sm mb-3 line-clamp-2">${dish.description}</p>
                        <button class="add-to-cart-btn w-full bg-primary/10 hover:bg-primary/20 text-primary py-2 rounded-lg font-medium transition-colors" 
                                data-id="${dish.id}">
                            <i class="fa fa-plus mr-1"></i> 加入订单
                        </button>
                    </div>
                `;
                dishesContainer.appendChild(dishCard);
            });

            // 为添加到购物车按钮添加事件监听
            document.querySelectorAll('.add-to-cart-btn').forEach(btn => {
                btn.addEventListener('click', function() {
                    const dishId = parseInt(this.getAttribute('data-id'));
                    addToCart(dishId);
                    
                    // 添加按钮动画效果
                    this.classList.add('bg-primary', 'text-white');
                    this.classList.remove('bg-primary/10', 'text-primary');
                    this.innerHTML = '<i class="fa fa-check mr-1"></i> 已添加';
                    
                    setTimeout(() => {
                        this.classList.remove('bg-primary', 'text-white');
                        this.classList.add('bg-primary/10', 'text-primary');
                        this.innerHTML = '<i class="fa fa-plus mr-1"></i> 加入订单';
                    }, 1000);
                });
            });
        }

        // 添加菜品到购物车
        function addToCart(dishId) {
            const dish = dishes.find(d => d.id === dishId);
            if (!dish) return;
            
            const existingItem = cart.find(item => item.id === dishId);
            
            if (existingItem) {
                existingItem.quantity++;
            } else {
                cart.push({
                    ...dish,
                    quantity: 1
                });
            }
            
            updateCart();
        }

        // 从购物车移除菜品
        function removeFromCart(dishId) {
            const index = cart.findIndex(item => item.id === dishId);
            if (index !== -1) {
                cart.splice(index, 1);
                updateCart();
            }
        }

        // 更新购物车中菜品数量
        function updateQuantity(dishId, newQuantity) {
            if (newQuantity < 1) return;
            
            const item = cart.find(item => item.id === dishId);
            if (item) {
                item.quantity = newQuantity;
                updateCart();
            }
        }

        // 更新购物车显示
        function updateCart() {
            // 更新购物车数量标记
            const totalItems = cart.reduce((sum, item) => sum + item.quantity, 0);
            cartCount.textContent = totalItems;
            
            // 更新购物车内容
            if (cart.length === 0) {
                cartItems.innerHTML = `
                    <div class="text-center text-gray-500 py-10">
                        <i class="fa fa-shopping-cart text-4xl mb-2 opacity-30"></i>
                        <p>购物车还是空的</p>
                        <p class="text-sm">快去添加美味菜品吧！</p>
                    </div>
                `;
                cartTotal.textContent = '¥0.00';
                return;
            }
            
            cartItems.innerHTML = '';
            let totalPrice = 0;
            
            cart.forEach(item => {
                const itemTotal = item.price * item.quantity;
                totalPrice += itemTotal;
                
                const cartItem = document.createElement('div');
                cartItem.className = 'flex items-center justify-between p-3 bg-neutral rounded-lg';
                cartItem.innerHTML = `
                    <div class="flex items-center">
                        <img src="${item.image}" alt="${item.name}" class="w-16 h-16 object-cover rounded-md mr-3">
                        <div>
                            <h4 class="font-medium">${item.name}</h4>
                            <p class="text-primary font-bold">¥${item.price}</p>
                        </div>
                    </div>
                    <div class="flex items-center">
                        <button class="quantity-btn minus bg-white w-7 h-7 rounded-full flex items-center justify-center shadow-sm" data-id="${item.id}">
                            <i class="fa fa-minus text-sm"></i>
                        </button>
                        <span class="mx-3 w-6 text-center">${item.quantity}</span>
                        <button class="quantity-btn plus bg-white w-7 h-7 rounded-full flex items-center justify-center shadow-sm" data-id="${item.id}">
                            <i class="fa fa-plus text-sm"></i>
                        </button>
                        <button class="ml-2 text-gray-400 hover:text-red-500" data-id="${item.id}">
                            <i class="fa fa-trash"></i>
                        </button>
                    </div>
                `;
                cartItems.appendChild(cartItem);
            });
            
            // 更新总价
            cartTotal.textContent = `¥${totalPrice.toFixed(2)}`;
            
            // 为购物车项目添加事件监听
            document.querySelectorAll('.quantity-btn.plus').forEach(btn => {
                btn.addEventListener('click', function() {
                    const dishId = parseInt(this.getAttribute('data-id'));
                    const item = cart.find(item => item.id === dishId);
                    updateQuantity(dishId, item.quantity + 1);
                });
            });
            
            document.querySelectorAll('.quantity-btn.minus').forEach(btn => {
                btn.addEventListener('click', function() {
                    const dishId = parseInt(this.getAttribute('data-id'));
                    const item = cart.find(item => item.id === dishId);
                    if (item.quantity > 1) {
                        updateQuantity(dishId, item.quantity - 1);
                    } else {
                        removeFromCart(dishId);
                    }
                });
            });
            
            document.querySelectorAll('.fa-trash').forEach(icon => {
                icon.parentElement.addEventListener('click', function() {
                    const dishId = parseInt(this.getAttribute('data-id'));
                    removeFromCart(dishId);
                });
            });
        }

        // 提交订单
        function submitOrder() {
            if (cart.length === 0) return;
            
            // 显示订单成功弹窗
            orderSuccessModal.classList.remove('hidden');
            // 触发动画
            setTimeout(() => {
                modalContent.classList.remove('scale-95', 'opacity-0');
                modalContent.classList.add('scale-100', 'opacity-100', 'transition-all', 'duration-300');
            }, 10);
            
            // 清空购物车
            cart = [];
            updateCart();
        }

        // 关闭订单成功弹窗
        function closeModal() {
            modalContent.classList.remove('scale-100', 'opacity-100');
            modalContent.classList.add('scale-95', 'opacity-0', 'transition-all', 'duration-300');
            
            setTimeout(() => {
                orderSuccessModal.classList.add('hidden');
            }, 300);
        }

        // 筛选菜品
        function filterDishes(category) {
            let filteredDishes = dishes;
            
            if (category !== '全部') {
                filteredDishes = dishes.filter(dish => dish.category === category);
            }
            
            // 同时应用搜索筛选
            const searchTerm = searchInput.value.toLowerCase().trim();
            if (searchTerm) {
                filteredDishes = filteredDishes.filter(dish => 
                    dish.name.toLowerCase().includes(searchTerm) || 
                    dish.description.toLowerCase().includes(searchTerm)
                );
            }
            
            renderDishes(filteredDishes);
        }

        // 设置事件监听器
        function setupEventListeners() {
            // 购物车侧边栏开关
            cartBtn.addEventListener('click', () => {
                cartSidebar.classList.remove('translate-x-full');
                document.body.style.overflow = 'hidden';
            });
            
            closeCartBtn.addEventListener('click', () => {
                cartSidebar.classList.add('translate-x-full');
                document.body.style.overflow = '';
            });
            
            // 提交订单按钮
            submitOrderBtn.addEventListener('click', submitOrder);
            
            // 关闭订单弹窗
            closeModalBtn.addEventListener('click', closeModal);
            
            // 点击弹窗外部关闭
            orderSuccessModal.addEventListener('click', (e) => {
                if (e.target === orderSuccessModal) {
                    closeModal();
                }
            });
            
            // 分类筛选
            categoryBtns.forEach(btn => {
                btn.addEventListener('click', () => {
                    // 更新按钮样式
                    categoryBtns.forEach(b => {
                        b.classList.remove('bg-primary', 'text-white');
                        b.classList.add('bg-white', 'text-dark', 'hover:bg-primary/10');
                    });
                    
                    btn.classList.remove('bg-white', 'text-dark', 'hover:bg-primary/10');
                    btn.classList.add('bg-primary', 'text-white');
                    
                    // 筛选菜品
                    filterDishes(btn.textContent);
                });
            });
            
            // 搜索功能
            searchInput.addEventListener('input', () => {
                // 找到当前选中的分类
                let activeCategory = '全部';
                categoryBtns.forEach(btn => {
                    if (btn.classList.contains('bg-primary')) {
                        activeCategory = btn.textContent;
                    }
                });
                
                filterDishes(activeCategory);
            });
        }

        // 初始化应用
        document.addEventListener('DOMContentLoaded', init);
    </script>
</body>
</html>
