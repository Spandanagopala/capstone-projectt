<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Agrilink Crop Marketplace Flow - Payment Flow Enhanced</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f4f7f6;
        }
        .card-number-group input:focus {
            z-index: 10;
        }
        /* Custom scrollbar for product list */
        .product-list-scroll::-webkit-scrollbar {
            width: 8px;
        }
        .product-list-scroll::-webkit-scrollbar-thumb {
            background-color: #d1d5db; /* gray-300 */
            border-radius: 4px;
        }
        .product-list-scroll::-webkit-scrollbar-thumb:hover {
            background-color: #9ca3af; /* gray-400 */
        }
    </style>
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    colors: {
                        'primary': '#059669',
                        'secondary': '#fbbf24',
                        'background': '#f4f7f6',
                        'card': '#ffffff',
                    }
                }
            }
        }
    </script>
</head>
<body class="min-h-screen flex items-start justify-center p-4">

    <div id="app" class="w-full max-w-xl bg-background shadow-2xl rounded-xl p-6 md:p-8">
        <header class="mb-6 flex justify-between items-center">
            <h1 class="text-3xl font-extrabold text-primary cursor-pointer" onclick="goToScreen('homeScreen')">Agrilink App</h1>
            <div id="header-cart-icon" class="hidden relative cursor-pointer" onclick="goToScreen('cartPage')">
                <svg xmlns="http://www.w3.org/2000/svg" class="h-8 w-8 text-primary" viewBox="0 0 20 20" fill="currentColor">
                    <path d="M3 1a1 1 0 000 2h1.22l.305 1.258A.428.428 0 005.4 5h9.23a.428.428 0 00.418-.344l1.39-5.568A1 1 0 0015.655 0H2.177L2.1 0zM17 17a2 2 0 11-4 0 2 2 0 014 0zm-6 0a2 2 0 11-4 0 2 2 0 014 0z" />
                    <path fill-rule="evenodd" d="M10 5a1 1 0 00-1 1v3H6a1 1 0 100 2h3v3a1 1 0 102 0v-3h3a1 1 0 100-2h-3V6a1 1 0 00-1-1z" clip-rule="evenodd" />
                </svg>
                <span id="cart-count-badge" class="absolute -top-1 -right-2 bg-secondary text-gray-800 text-xs font-bold px-2 rounded-full">0</span>
            </div>
        </header>

        <div id="screen-container" class="bg-card p-6 rounded-lg shadow-inner min-h-[400px]"></div>

        <div id="message-box" class="mt-4 hidden p-3 rounded-lg text-sm transition-all duration-300"></div>

    </div>

    <script>
        const appState = {
            currentScreen: 'homeScreen',
            currentUser: null,
            searchQuery: '',
            mockDB: {
                products: [
                    // --- Using external image URLs for real product representation ---
                    { id: 1, name: 'Organic Tomatoes', emoji: '🍅', price: 80.00, unit: 'kg', farmer: 'Farm A', imageUrl: 'istockphoto-847335116-612x612.jpg' },
                    { id: 2, name: 'Heirloom Carrots', emoji: '🥕', price: 55.00, unit: 'kg', farmer: 'Farm B', imageUrl: 'aa2b7c08-1324-4939-9378-f39fd2640907.jpeg' },
                    { id: 3, name: 'Fresh Strawberries', emoji: '🍓', price: 350.00, unit: 'box', farmer: 'Farm C', imageUrl: 'be2756ea-9986-47fd-90a4-5db7e5234ff9.jpeg' },
                    { id: 4, name: 'Red Onions', emoji: '🧅', price: 40.00, unit: 'kg', farmer: 'Farm A', imageUrl: '2e82f87b-7603-4201-8bc7-00ee3bbd2bff.jpeg' },
                    { id: 5, name: 'Potatoes', emoji: '🥔', price: 30.00, unit: 'kg', farmer: 'Farm D', imageUrl: 'b9ea7b75-a236-487e-a6ab-88650daae588.jpeg' },
                    { id: 6, name: 'Green Bell Pepper', emoji: '🫑', price: 90.00, unit: 'kg', farmer: 'Farm E', imageUrl: '9044d6b5-6816-4d44-89cc-a30653619722.jpeg' },
                ],
            },
            cart: {}
        };

        const container = document.getElementById('screen-container');
        const messageBox = document.getElementById('message-box');
        const cartIconContainer = document.getElementById('header-cart-icon');
        const cartCountBadge = document.getElementById('cart-count-badge');
        
        function updateHeaderCart() {
            const count = getCartCount();
            if (appState.currentUser && appState.currentUser.role === 'customer') {
                cartIconContainer.classList.remove('hidden');
                cartCountBadge.textContent = count;
            } else {
                cartIconContainer.classList.add('hidden');
            }
        }

        function getCartCount() {
            return Object.values(appState.cart).reduce((sum, item) => sum + item.quantity, 0);
        }
        
        function getProductQuantity(productId) {
            return appState.cart[productId] ? appState.cart[productId].quantity : 0;
        }

        function showMessage(text, type = 'success') {
            messageBox.textContent = text;
            messageBox.classList.remove('hidden', 'bg-red-100', 'text-red-800', 'bg-green-100', 'text-green-800');

            if (type === 'success') {
                messageBox.classList.add('bg-green-100', 'text-green-800');
            } else if (type === 'error') {
                messageBox.classList.add('bg-red-100', 'text-red-800');
            }
            setTimeout(() => {
                messageBox.classList.add('hidden');
            }, 3000);
        }

        window.handleSearch = (query) => {
            appState.searchQuery = query;
            renderProductPage();
        };

        function renderHome() {
            appState.searchQuery = ''; 
            
            container.innerHTML = `
                <h2 class="text-2xl font-semibold text-gray-800 mb-4 text-center">Welcome to Agrilink</h2>
                <p class="text-gray-600 mb-8 text-center font-medium">
                    Agrilink is your platform to connect **farmers directly with consumers**, ensuring fair prices and fresh, high-quality produce. Choose your role below to start trading.
                </p>

                <div class="space-y-4">
                    <button onclick="goToScreen('loginScreen', {role: 'customer'})"
                        class="w-full flex items-center justify-center p-4 bg-primary text-white font-bold rounded-lg shadow-md hover:bg-green-700 transition duration-150 transform hover:scale-[1.02]">
                        🛒 Buy Crops (Customer)
                    </button>

                    <button onclick="goToScreen('loginScreen', {role: 'farmer'})"
                        class="w-full flex items-center justify-center p-4 bg-secondary text-gray-800 font-bold rounded-lg shadow-md hover:bg-amber-600 transition duration-150 transform hover:scale-[1.02]">
                        🌾 Sell Crops (Farmer)
                    </button>
                </div>
            `;
            updateHeaderCart();
        }

        function renderLogin(role) {
            const roleName = role.charAt(0).toUpperCase() + role.slice(1);
            container.innerHTML = `
                <h2 class="text-2xl font-semibold text-gray-800 mb-4 text-center">${roleName} Login</h2>
                <p class="text-gray-600 mb-6 text-center">Enter credentials to log in.</p>

                <div class="space-y-4">
                    <div>
                        <label for="username" class="block text-sm font-medium text-gray-700">Username (Mock)</label>
                        <input type="text" id="username" value="${role}user"
                                class="mt-1 block w-full rounded-md border-gray-300 shadow-sm p-2 border focus:border-primary focus:ring-primary"
                                placeholder="Enter username">
                    </div>
                    <div>
                        <label for="password" class="block text-sm font-medium text-gray-700">Password (Mock)</label>
                        <input type="password" id="password" value="password"
                                class="mt-1 block w-full rounded-md border-gray-300 shadow-sm p-2 border focus:border-primary focus:ring-primary"
                                placeholder="Enter password">
                    </div>

                    <button onclick="mockLogin('${role}')"
                        class="w-full p-3 bg-primary text-white font-semibold rounded-lg shadow-md hover:bg-green-700 transition duration-150">
                        Login Success
                    </button>
                    
                    <p class="text-center text-sm">
                        Don't have an account?
                        <button onclick="goToScreen('signupScreen', {role: '${role}'})" class="text-secondary font-medium hover:underline">
                            Sign Up
                        </button>
                    </p>

                    <button onclick="goToScreen('homeScreen')"
                        class="w-full p-3 text-primary border border-primary rounded-lg hover:bg-primary/10 transition duration-150">
                        ← Back to Home
                    </button>
                </div>
            `;
            updateHeaderCart();
        }
        
        function mockLogin(role) {
            appState.currentUser = { role: role, name: document.getElementById('username').value || `${role}User` };
            showMessage(`Login Successful! Logged in as ${appState.currentUser.name} (${role}).`, 'success');

            if (role === 'customer') {
                goToScreen('productPage'); 
            } else if (role === 'farmer') {
                goToScreen('addProductPage'); 
            }
        }
        
        function mockSignup(role) {
            appState.currentUser = { role: role, name: document.getElementById('username').value || `${role}NewUser` };
            showMessage(`Signup Successful! Logged in as ${appState.currentUser.name} (${role}).`, 'success');

            if (role === 'customer') {
                goToScreen('productPage'); 
            } else if (role === 'farmer') {
                goToScreen('addProductPage'); 
            }
        }

        function renderProductPage() {
            if (!appState.currentUser) return goToScreen('homeScreen');

            const query = appState.searchQuery.toLowerCase();
            const totalCartItems = getCartCount();
            
            const filteredProducts = appState.mockDB.products.filter(p => 
                p.name.toLowerCase().includes(query) || 
                p.farmer.toLowerCase().includes(query)
            );

            const productListHTML = filteredProducts.length > 0 ? filteredProducts.map(p => {
                const quantity = getProductQuantity(p.id);
                const isInCart = quantity > 0;
                
                return `
                    <div class="bg-white border border-gray-200 rounded-lg shadow-md p-4 flex flex-col items-center text-center hover:shadow-lg transition duration-200 h-96 justify-between">
                        <div class="flex flex-col items-center w-full">
                            <!-- Image Display Block: Uses external URL with emoji fallback -->
                            <div class="w-full h-32 mb-3 border border-gray-300 rounded-lg overflow-hidden flex items-center justify-center bg-gray-100">
                                ${p.imageUrl ? `
                                    <img src="${p.imageUrl}" 
                                         alt="${p.name}" 
                                         class="w-full h-full object-cover"
                                         onerror="this.onerror=null;this.parentElement.innerHTML='<span class=\\'text-2xl\\'>${p.emoji || '🌱'}</span>';">
                                ` : `
                                    <span class="text-2xl">${p.emoji || '🌱'}</span>
                                `}
                            </div>
                            <!-- Product Details -->
                            <div class="p-1">
                                <p class="font-bold text-gray-900 text-base leading-tight">${p.name}</p>
                                <p class="text-sm text-gray-500 mb-1">${p.emoji} ₹${p.price.toFixed(2)} / ${p.unit}</p>
                                <p class="text-xs text-gray-400">by ${p.farmer}</p>
                            </div>
                        </div>
                        
                        <div class="mt-auto w-full">
                            ${isInCart ? `
                                <div class="flex justify-center items-center space-x-2">
                                    <button onclick="updateCartQuantity(${p.id}, -1, '${p.name}')"
                                        class="p-1 w-8 h-8 bg-red-500 text-white rounded-full font-bold text-base hover:bg-red-600 transition">
                                        −
                                    </button>
                                    <span class="text-lg font-bold text-primary w-6">${quantity}</span>
                                    <button onclick="updateCartQuantity(${p.id}, 1, '${p.name}')"
                                        class="p-1 w-8 h-8 bg-primary text-white rounded-full font-bold text-base hover:bg-green-600 transition">
                                        +
                                    </button>
                                </div>
                            ` : `
                                <button onclick="updateCartQuantity(${p.id}, 1, '${p.name}')"
                                    class="w-full p-2 bg-primary text-white rounded-lg font-semibold hover:bg-green-600 transition">
                                    Add to Cart
                                </button>
                            `}
                        </div>
                    </div>
                `;
            }).join('') : '<p class="text-center text-gray-500 py-10">No crops found matching your search criteria.</p>';

            container.innerHTML = `
                <h2 class="text-2xl font-semibold text-gray-800 mb-4 text-center">🛒 Crop Marketplace</h2>
                <p class="text-gray-600 mb-4 text-center">Find fresh produce directly from local farmers.</p>
                
                <input type="text" id="productSearch" placeholder="Search by crop name or farmer..."
                    class="mb-6 block w-full rounded-md border-gray-300 shadow-sm p-3 border focus:border-primary focus:ring-primary"
                    value="${appState.searchQuery}" onkeyup="handleSearch(this.value)">

                <div class="grid grid-cols-2 gap-4 mb-6 max-h-[400px] overflow-y-auto p-2 product-list-scroll">
                    ${productListHTML}
                </div>

                <div class="space-y-3">
                    <p class="text-center font-medium text-gray-700">Total Items in Cart: <span class="text-primary font-extrabold text-xl">${totalCartItems}</span></p>
                    <button onclick="goToScreen('cartPage')"
                        class="w-full p-3 bg-secondary text-gray-800 font-semibold rounded-lg shadow-md hover:bg-amber-600 transition duration-150">
                        Go to Cart (${totalCartItems} Items)
                    </button>
                </div>
            `;
            
            const searchInput = document.getElementById('productSearch');
            if(searchInput) {
                searchInput.focus();
                const len = searchInput.value.length;
                searchInput.setSelectionRange(len, len);
            }
            
            updateHeaderCart();
        }

        function updateCartQuantity(productId, change, productName) {
            const product = appState.mockDB.products.find(p => p.id === productId);

            if (!product) return;
            
            if (appState.cart[productId]) {
                appState.cart[productId].quantity += change;

                if (appState.cart[productId].quantity <= 0) {
                    delete appState.cart[productId];
                    showMessage(`${productName} removed from cart.`, 'success');
                } else {
                    showMessage(`Updated cart: ${productName} x ${appState.cart[productId].quantity}`, 'success');
                }
            } else if (change > 0) {
                appState.cart[productId] = { ...product, quantity: change };
                showMessage(`${productName} added to cart! (1 unit)`, 'success');
            }
            
            renderProductPage(); 
        }

        function renderCartPage() {
            if (!appState.currentUser) return goToScreen('homeScreen');

            const cartItems = Object.values(appState.cart);
            const total = cartItems.reduce((sum, item) => sum + (item.price * item.quantity), 0);

            const cartListHTML = cartItems.length > 0 ? cartItems.map(item => `
                <div class="flex justify-between items-center p-4 border-b last:border-b-0 bg-gray-50 rounded-md mb-2">
                    <div class="flex items-center space-x-3">
                        <div class="w-10 h-10 bg-gray-100 rounded-md border border-gray-300 flex items-center justify-center text-xs text-gray-500 overflow-hidden">
                            <!-- Small cart image/emoji -->
                            ${item.imageUrl ? `
                                <img src="${item.imageUrl}" 
                                     alt="${item.name}" 
                                     class="w-full h-full object-cover"
                                     onerror="this.onerror=null;this.parentElement.innerHTML='<span class=\\'text-sm font-bold text-gray-700 p-1\\'>${item.name.split(' ').slice(-1)}</span>';">
                            ` : `
                                <span class="text-sm font-bold text-gray-700 p-1">${item.emoji || '🌱'}</span>
                            `}
                        </div>
                        <div>
                            <p class="font-medium text-gray-900">${item.name}</p>
                            <p class="text-sm text-gray-500">${item.emoji} ₹${item.price.toFixed(2)} per ${item.unit}</p>
                        </div>
                    </div>
                    <div class="text-right">
                        <p class="font-semibold text-gray-900">x ${item.quantity}</p>
                        <p class="text-sm text-primary font-bold">₹${(item.price * item.quantity).toFixed(2)}</p>
                    </div>
                </div>
            `).join('') : '<p class="text-center text-gray-500 py-10">Your cart is empty. Go add some crops!</p>';
            
            const totalCartItems = getCartCount();

            container.innerHTML = `
                <h2 class="text-2xl font-semibold text-gray-800 mb-4 text-center">🛒 Your Shopping Cart</h2>
                <p class="text-gray-600 mb-6 text-center">Review your ${totalCartItems} item(s) order.</p>

                <div class="border rounded-lg mb-6 p-4 max-h-60 overflow-y-auto product-list-scroll">
                    ${cartListHTML}
                </div>

                <div class="flex justify-between items-center p-3 border-t-2 border-primary/50 text-xl font-bold mb-6">
                    <span>Final Total:</span>
                    <span>₹${total.toFixed(2)}</span>
                </div>

                <div class="space-y-3">
                    <button onclick="goToScreen('checkoutDetailsPage')"
                        class="w-full p-3 bg-primary text-white font-semibold rounded-lg shadow-md hover:bg-green-700 transition duration-150"
                        ${cartItems.length === 0 ? 'disabled' : ''}>
                        Checkout & Select Payment
                    </button>
                    <button onclick="goToScreen('productPage')"
                        class="w-full p-3 text-primary border border-primary rounded-lg hover:bg-primary/10 transition duration-150">
                        ← Continue Shopping
                    </button>
                </div>
            `;
            updateHeaderCart();
        }
        
        function renderCheckoutDetailsPage() {
            if (!appState.currentUser) return goToScreen('homeScreen');

            const finalTotal = Object.values(appState.cart).reduce((sum, item) => sum + (item.price * item.quantity), 0);

            container.innerHTML = `
                <h2 class="text-2xl font-semibold text-gray-800 mb-4 text-center">Choose Payment Method</h2>
                <p class="text-gray-600 mb-6 text-center">Select how you want to pay for your order.</p>

                <div class="p-4 bg-green-50 border border-green-200 rounded-lg mb-6 flex justify-between items-center">
                    <p class="text-lg font-medium text-gray-700">Order Total:</p>
                    <p class="text-2xl font-extrabold text-primary">₹${finalTotal.toFixed(2)}</p>
                </div>

                <div class="space-y-4">
                    <button onclick="goToScreen('creditCardPage')"
                        class="w-full p-4 border border-gray-300 rounded-lg bg-white shadow-md flex items-center justify-start space-x-4 hover:border-primary transition duration-150">
                        <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6 text-primary" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                            <path stroke-linecap="round" stroke-linejoin="round" d="M3 10h18M7 15h1m4 0h1m-7 4h12a3 3 0 003-3V8a3 3 0 00-3-3H6a3 3 0 00-3 3v8a3 3 0 003 3z" />
                        </svg>
                        <span class="text-lg font-medium text-gray-900">Credit / Debit Card</span>
                    </button>

                    <button onclick="goToScreen('upiPage')"
                        class="w-full p-4 border border-gray-300 rounded-lg bg-white shadow-md flex items-center justify-start space-x-4 hover:border-primary transition duration-150">
                        <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6 text-primary" viewBox="0 0 20 20" fill="currentColor">
                            <path d="M5 4a2 2 0 012-2h6a2 2 0 012 2v1H5V4z" />
                            <path fill-rule="evenodd" d="M2 7v10a2 2 0 002 2h12a2 2 0 002-2V7H2zm8 4a1 1 0 00-1 1v2a1 1 0 102 0v-2a1 1 0 00-1-1z" clip-rule="evenodd" />
                        </svg>
                        <span class="text-lg font-medium text-gray-900">UPI / Net Banking</span>
                    </button>

                    <button onclick="goToScreen('cashOnDeliveryPage')"
                        class="w-full p-4 border border-gray-300 rounded-lg bg-white shadow-md flex items-center justify-start space-x-4 hover:border-primary transition duration-150">
                        <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6 text-primary" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                            <path stroke-linecap="round" stroke-linejoin="round" d="M17 9V7a2 2 0 00-2-2H5a2 2 0 00-2 2v6a2 2 0 002 2h2l-2 2v-2h10a2 2 0 002-2V9z" />
                        </svg>
                        <span class="text-lg font-medium text-gray-900">Cash on Delivery (COD)</span>
                    </button>
                    
                    <button onclick="goToScreen('cartPage')"
                        class="w-full p-3 text-gray-500 border border-gray-300 rounded-lg hover:bg-gray-100 transition duration-150 mt-6">
                        ← Back to Cart
                    </button>
                </div>
            `;
            updateHeaderCart();
        }

        function renderCreditCardPage() {
            if (!appState.currentUser) return goToScreen('homeScreen');
            const finalTotal = Object.values(appState.cart).reduce((sum, item) => sum + (item.price * item.quantity), 0);
            
            container.innerHTML = `
                <h2 class="text-2xl font-semibold text-gray-800 mb-4 text-center">💳 Pay with Card</h2>
                <p class="text-gray-600 mb-6 text-center">Enter your details to complete the payment.</p>
                
                <div class="p-4 bg-green-50 border border-green-200 rounded-lg mb-6 flex justify-between items-center">
                    <p class="text-lg font-medium text-gray-700">Amount Due:</p>
                    <p class="text-2xl font-extrabold text-primary">₹${finalTotal.toFixed(2)}</p>
                </div>
                
                <form onsubmit="handlePaymentSubmit(event, 'Credit Card')" class="space-y-4">
                    <div>
                        <label for="cardNumber" class="block text-sm font-medium text-gray-700">Card Number</label>
                        <input type="tel" id="cardNumber" required pattern="[0-9]{16}" maxlength="16"
                                class="mt-1 block w-full rounded-md border-gray-300 shadow-sm p-2 border focus:border-primary focus:ring-primary"
                                placeholder="XXXX XXXX XXXX XXXX">
                    </div>
                    <div class="flex space-x-4">
                        <div class="w-1/2">
                            <label for="expiryDate" class="block text-sm font-medium text-gray-700">Expiry Date</label>
                            <input type="text" id="expiryDate" required pattern="(0[1-9]|1[0-2])\\/[0-9]{2}" maxlength="5"
                                class="mt-1 block w-full rounded-md border-gray-300 shadow-sm p-2 border focus:border-primary focus:ring-primary"
                                placeholder="MM/YY">
                        </div>
                        <div class="w-1/2">
                            <label for="cvv" class="block text-sm font-medium text-gray-700">CVV</label>
                            <input type="tel" id="cvv" required pattern="[0-9]{3,4}" maxlength="4"
                                class="mt-1 block w-full rounded-md border-gray-300 shadow-sm p-2 border focus:border-primary focus:ring-primary"
                                placeholder="XXX">
                        </div>
                    </div>
                    <div>
                        <label for="cardName" class="block text-sm font-medium text-gray-700">Name on Card</label>
                        <input type="text" id="cardName" required
                                class="mt-1 block w-full rounded-md border-gray-300 shadow-sm p-2 border focus:border-primary focus:ring-primary"
                                placeholder="John Doe">
                    </div>

                    <button type="submit"
                        class="w-full p-3 bg-primary text-white font-semibold rounded-lg shadow-md hover:bg-green-700 transition duration-150">
                        Pay ₹${finalTotal.toFixed(2)} Securely
                    </button>
                    <button type="button" onclick="goToScreen('checkoutDetailsPage')"
                        class="w-full p-3 text-gray-500 border border-gray-300 rounded-lg hover:bg-gray-100 transition duration-150">
                        ← Choose different method
                    </button>
                </form>
            `;
            updateHeaderCart();
        }
        
        function renderUpiPage() {
            if (!appState.currentUser) return goToScreen('homeScreen');
            const finalTotal = Object.values(appState.cart).reduce((sum, item) => sum + (item.price * item.quantity), 0);
            
            container.innerHTML = `
                <h2 class="text-2xl font-semibold text-gray-800 mb-4 text-center">📱 Pay with UPI</h2>
                <p class="text-gray-600 mb-6 text-center">Select your preferred UPI app or enter your VPA.</p>
                
                <div class="p-4 bg-green-50 border border-green-200 rounded-lg mb-6 flex justify-between items-center">
                    <p class="text-lg font-medium text-gray-700">Amount Due:</p>
                    <p class="text-2xl font-extrabold text-primary">₹${finalTotal.toFixed(2)}</p>
                </div>
                
                <div class="space-y-4">
                    <p class="font-semibold text-gray-700">1. Select UPI App (Mock Payment)</p>
                    <div class="grid grid-cols-3 gap-3">
                        ${['PhonePe', 'Google Pay', 'Paytm', 'Amazon Pay', 'BHIM', 'Razorpay'].map(app => `
                            <button onclick="handlePaymentSubmit(event, 'UPI via ${app}')"
                                class="p-3 border rounded-lg bg-white shadow-sm text-sm font-medium hover:bg-green-50 transition">
                                ${app}
                            </button>
                        `).join('')}
                    </div>

                    <p class="font-semibold text-gray-700 pt-4">2. Enter UPI VPA (Virtual Payment Address)</p>
                    <form onsubmit="handlePaymentSubmit(event, 'UPI via VPA')" class="space-y-4">
                        <input type="text" id="upiVpa" required 
                                class="mt-1 block w-full rounded-md border-gray-300 shadow-sm p-2 border focus:border-primary focus:ring-primary"
                                placeholder="name@bankname / mobile@upi">
                        
                        <button type="submit"
                            class="w-full p-3 bg-primary text-white font-semibold rounded-lg shadow-md hover:bg-green-700 transition duration-150">
                            Verify & Pay ₹${finalTotal.toFixed(2)}
                        </button>
                    </form>
                    
                    <button onclick="goToScreen('checkoutDetailsPage')"
                        class="w-full p-3 text-gray-500 border border-gray-300 rounded-lg hover:bg-gray-100 transition duration-150">
                        ← Choose different method
                    </button>
                </div>
            `;
            updateHeaderCart();
        }

        function handlePaymentSubmit(event, method) {
            event.preventDefault();
            
            // Validation logic (kept the same)
            if (method.includes('Credit Card')) {
                const cardNumber = document.getElementById('cardNumber').value;
                if (cardNumber.length !== 16 || isNaN(cardNumber)) {
                     return showMessage('Invalid Card Number.', 'error');
                }
            } else if (method.includes('UPI via VPA')) {
                const upiVpa = document.getElementById('upiVpa').value;
                if (!upiVpa.includes('@')) {
                    return showMessage('Invalid UPI VPA format. Must contain "@".', 'error');
                }
            }
            
            // For Card/UPI, redirect to the specified external payment link
            if (method !== 'Cash on Delivery') {
                // Clear the cart before redirecting to external gateway
                appState.cart = {}; 
                // Redirect to the external link
                window.location.href = "https://rzp.io/rzp/0G2vEczH";
                showMessage(`Redirecting to payment gateway for ${method}...`);
                return; 
            }
            
            goToScreen('orderSummaryPage', { status: 'success', method: method });
        }

        function renderCashOnDeliveryPage() {
            if (!appState.currentUser) return goToScreen('homeScreen');
            const finalTotal = Object.values(appState.cart).reduce((sum, item) => sum + (item.price * item.quantity), 0);

            container.innerHTML = `
                <h2 class="text-2xl font-semibold text-gray-800 mb-4 text-center">💵 Cash on Delivery</h2>
                <p class="text-gray-600 mb-6 text-center">Confirm your order for payment upon delivery.</p>

                <div class="p-4 bg-amber-50 border border-amber-300 rounded-lg mb-6 flex justify-between items-center">
                    <p class="text-lg font-medium text-gray-700">Order Total:</p>
                    <p class="text-2xl font-extrabold text-secondary">₹${finalTotal.toFixed(2)}</p>
                </div>

                <div class="bg-white p-5 rounded-lg shadow-md space-y-4">
                    <p class="text-lg font-medium text-gray-800">Note on COD:</p>
                    <p class="text-gray-600 text-sm">
                        You will pay **₹${finalTotal.toFixed(2)}** in cash or local digital payment upon receiving your fresh produce. 
                        Please ensure you have the exact amount ready for the delivery partner.
                    </p>
                    
                    <button onclick="goToScreen('orderSummaryPage', { status: 'pending', method: 'Cash on Delivery' })"
                        class="w-full p-3 mt-4 bg-primary text-white font-semibold rounded-lg shadow-md hover:bg-green-700 transition duration-150">
                        Place Order (Payment on Delivery)
                    </button>
                    
                    <button onclick="goToScreen('checkoutDetailsPage')"
                        class="w-full p-3 text-gray-500 border border-gray-300 rounded-lg hover:bg-gray-100 transition duration-150">
                        ← Choose different method
                    </button>
                </div>
            `;
            updateHeaderCart();
        }

        function renderOrderSummaryPage(status, method) {
            if (!appState.currentUser) return goToScreen('homeScreen');

            const cartItems = Object.values(appState.cart);
            const finalTotal = cartItems.reduce((sum, item) => sum + (item.price * item.quantity), 0);
            const totalCartItems = getCartCount();
            
            const isSuccess = status === 'success';
            
            const title = isSuccess ? 'Order Placed Successfully!' : 'Order Placed (Payment Pending)';
            const primaryText = isSuccess ? 'Payment Confirmed' : 'Payment Due on Delivery';
            const iconClass = isSuccess ? 'text-primary' : 'text-secondary';
            const iconPath = isSuccess 
                ? `<path fill-rule="evenodd" d="M10 18a8 8 0 100-16 8 8 0 000 16zm3.707-9.293a1 1 0 00-1.414-1.414L9 10.586 7.707 9.293a1 1 0 00-1.414 1.414l2 2a1 1 0 001.414 0l4-4z" clip-rule="evenodd" />`
                : `<path fill-rule="evenodd" d="M18 10a8 8 0 11-16 0 8 8 0 0116 0zm-8-3a1 1 0 00-.894.553L7.382 10l-1.888 3.447A1 1 0 006.5 15h7a1 1 0 00.894-1.447L12.618 10l1.888-3.447A1 1 0 0013.5 7h-3z" clip-rule="evenodd" />`;


            const itemSummaryHTML = cartItems.map(item => `
                <div class="flex justify-between text-sm text-gray-600 border-b border-gray-100 py-1">
                    <span>${item.emoji} ${item.name} (x${item.quantity})</span>
                    <span>₹${(item.price * item.quantity).toFixed(2)}</span>
                </div>
            `).join('');

            container.innerHTML = `
                <div class="flex flex-col items-center text-center p-4">
                    
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-16 w-16 ${iconClass} animate-pulse" viewBox="0 0 20 20" fill="currentColor">
                        ${iconPath}
                    </svg>

                    <h2 class="text-3xl font-extrabold ${iconClass} mt-4 mb-2">${title}</h2>
                    <p class="text-gray-600 mb-6 font-medium">Your fresh produce is being prepared for fulfillment.</p>

                    <div class="w-full bg-green-50 border border-green-200 rounded-xl p-5 shadow-lg mb-6">
                        <p class="text-xl font-bold text-gray-800 mb-3 border-b border-green-200 pb-2">Order Details</p>
                        
                        <div class="space-y-3">
                            <div class="flex justify-between font-medium text-gray-700">
                                <span>Order ID:</span>
                                <span class="text-primary">#AGR${Date.now().toString().slice(-6)}</span>
                            </div>
                            <div class="flex justify-between font-medium text-gray-700">
                                <span>Payment Method:</span>
                                <span class="text-gray-900 font-bold">${method}</span>
                            </div>
                            <div class="flex justify-between font-medium text-gray-700">
                                <span>Payment Status:</span>
                                <span class="${isSuccess ? 'text-green-700' : 'text-amber-700'} font-bold">${primaryText}</span>
                            </div>
                            <div class="flex justify-between font-bold text-2xl text-primary pt-3 border-t border-green-200">
                                <span>Total Paid/Due:</span>
                                <span>₹${finalTotal.toFixed(2)}</span>
                            </div>
                        </div>
                    </div>

                    <div class="w-full bg-white rounded-lg p-4 shadow-md mb-8 border border-gray-100">
                        <p class="font-semibold text-gray-700 mb-2">Items Purchased (${totalCartItems} total):</p>
                        ${itemSummaryHTML}
                    </div>

                    <button onclick="resetAppAndGoHome()"
                        class="w-full p-3 bg-gray-500 text-white font-semibold rounded-lg shadow-md hover:bg-gray-700 transition duration-150">
                        Start New Session (Back to Home)
                    </button>
                </div>
            `;
            updateHeaderCart();
            appState.cart = {};
        }


        function renderAddProductPage() {
            if (!appState.currentUser || appState.currentUser.role !== 'farmer') return goToScreen('homeScreen');

            container.innerHTML = `
                <h2 class="text-2xl font-semibold text-gray-800 mb-4 text-center">🌾 Add Product Page (Farmer View)</h2>
                <p class="text-gray-600 mb-6 text-center">Enter crop details to make them available. (Storing Product in Database)</p>

                <form id="addProductForm" class="space-y-4">
                    <div>
                        <label for="cropName" class="block text-sm font-medium text-gray-700">Crop Name</label>
                        <input type="text" id="cropName" required
                                class="mt-1 block w-full rounded-md border-gray-300 shadow-sm p-2 border focus:border-primary focus:ring-primary">
                    </div>
                    <div>
                        <label for="imageUrl" class="block text-sm font-medium text-gray-700">Image URL (Optional)</label>
                        <input type="url" id="imageUrl" 
                                class="mt-1 block w-full rounded-md border-gray-300 shadow-sm p-2 border focus:border-primary focus:ring-primary"
                                placeholder="Enter public URL of the product image">
                    </div>
                    <div>
                        <label for="emoji" class="block text-sm font-medium text-gray-700">Emoji (e.g., 🍎, 🧅)</label>
                        <input type="text" id="emoji" maxlength="2"
                                class="mt-1 block w-full rounded-md border-gray-300 shadow-sm p-2 border focus:border-primary focus:ring-primary"
                                placeholder="Optional emoji for the product">
                    </div>
                    <div>
                        <label for="price" class="block text-sm font-medium text-gray-700">Price (per unit in ₹)</label>
                        <input type="number" id="price" required min="0.01" step="0.01"
                                class="mt-1 block w-full rounded-md border-gray-300 shadow-sm p-2 border focus:border-primary focus:ring-primary">
                    </div>
                    <div>
                        <label for="unit" class="block text-sm font-medium text-gray-700">Unit (e.g., kg, lb, box)</label>
                        <input type="text" id="unit" required
                                class="mt-1 block w-full rounded-md border-gray-300 shadow-sm p-2 border focus:border-primary focus:ring-primary">
                    </div>

                    <button type="submit"
                        class="w-full p-3 bg-secondary text-gray-800 font-semibold rounded-lg shadow-md hover:bg-amber-600 transition duration-150">
                        Save Product (Store Product)
                    </button>
                    <button type="button" onclick="goToScreen('homeScreen')"
                        class="w-full p-3 text-gray-500 border border-gray-300 rounded-lg hover:bg-gray-100 transition duration-150">
                        ← Logout / Back to Home
                    </button>
                </form>
            `;
            document.getElementById('addProductForm').onsubmit = handleAddProduct;
            updateHeaderCart();
        }

        function handleAddProduct(event) {
            event.preventDefault();
            const name = document.getElementById('cropName').value;
            const imageUrl = document.getElementById('imageUrl').value.trim();
            const emoji = document.getElementById('emoji').value.trim(); 
            const price = parseFloat(document.getElementById('price').value);
            const unit = document.getElementById('unit').value;

            const newProduct = {
                id: Date.now(),
                name: name,
                imageUrl: imageUrl,
                emoji: emoji || '🌱',
                price: price,
                unit: unit,
                farmer: appState.currentUser.name
            };

            appState.mockDB.products.push(newProduct);
            showMessage(`New product '${name}' added successfully! (Stored in Database)`);

            document.getElementById('addProductForm').reset();
        }

        function renderSignup(role) {
            const roleName = role.charAt(0).toUpperCase() + role.slice(1);
            container.innerHTML = `
                <h2 class="text-2xl font-semibold text-gray-800 mb-4 text-center">${roleName} Signup</h2>
                <p class="text-gray-600 mb-6 text-center">Create a new account. (Mock Signup)</p>

                <div class="space-y-4">
                    <div>
                        <label for="username" class="block text-sm font-medium text-gray-700">Username (Mock)</label>
                        <input type="text" id="username" required
                                class="mt-1 block w-full rounded-md border-gray-300 shadow-sm p-2 border focus:border-primary focus:ring-primary"
                                placeholder="Choose a username">
                    </div>
                    <div>
                        <label for="email" class="block text-sm font-medium text-gray-700">Email (Mock)</label>
                        <input type="email" id="email" required
                                class="mt-1 block w-full rounded-md border-gray-300 shadow-sm p-2 border focus:border-primary focus:ring-primary"
                                placeholder="Enter your email">
                    </div>
                    <div>
                        <label for="password" class="block text-sm font-medium text-gray-700">Password (Mock)</label>
                        <input type="password" id="password" required
                                class="mt-1 block w-full rounded-md border-gray-300 shadow-sm p-2 border focus:border-primary focus:ring-primary"
                                placeholder="Choose a strong password">
                    </div>

                    <button onclick="mockSignup('${role}')"
                        class="w-full p-3 bg-primary text-white font-semibold rounded-lg shadow-md hover:bg-green-700 transition duration-150">
                        Sign Up
                    </button>
                    
                    <p class="text-center text-sm">
                        Already have an account?
                        <button onclick="goToScreen('loginScreen', {role: '${role}'})" class="text-secondary font-medium hover:underline">
                            Log In
                        </button>
                    </p>

                    <button onclick="goToScreen('homeScreen')"
                        class="w-full p-3 text-primary border border-primary rounded-lg hover:bg-primary/10 transition duration-150">
                        ← Back to Home
                    </button>
                </div>
            `;
            updateHeaderCart();
        }

        function goToScreen(screenName, params = {}) {
            appState.currentScreen = screenName;
            messageBox.classList.add('hidden');

            switch (screenName) {
                case 'homeScreen':
                    renderHome();
                    break;
                case 'loginScreen':
                    renderLogin(params.role);
                    break;
                case 'signupScreen': 
                    renderSignup(params.role);
                    break;
                case 'productPage':
                    renderProductPage();
                    break;
                case 'cartPage':
                    renderCartPage();
                    break;
                case 'checkoutDetailsPage':
                    renderCheckoutDetailsPage();
                    break;
                case 'creditCardPage':
                    renderCreditCardPage();
                    break;
                case 'upiPage':
                    renderUpiPage();
                    break;
                case 'cashOnDeliveryPage':
                    renderCashOnDeliveryPage();
                    break;
                case 'orderSummaryPage': 
                    renderOrderSummaryPage(params.status, params.method);
                    break;
                case 'addProductPage':
                    renderAddProductPage();
                    break;
                default:
                    renderHome();
            }
            container.parentElement.scrollTo(0, 0);
        }

        function resetAppAndGoHome() {
            appState.currentUser = null;
            appState.cart = {};
            appState.searchQuery = ''; 
            goToScreen('homeScreen');
            showMessage("Session ended. Welcome back!", 'success');
        }

        window.onload = () => {
            goToScreen('homeScreen');
        };

    </script>
</body>
</html>
