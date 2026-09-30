<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Global Express Market - Premier African E-Commerce</title>

  <!-- Leaflet CSS (Free Interactive GPS Map) -->
  <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />

  <style>
    :root {
      --primary: #25D366;
      --primary-dark: #1ebc57;
      --dark: #111827;
      --light: #f9fafb;
      --gray: #6b7280;
      --border: #e5e7eb;
      --admin-bg: #eff6ff;
      --admin-border: #bfdbfe;
    }

    * { box-sizing: border-box; margin: 0; padding: 0; font-family: 'Inter', system-ui, -apple-system, sans-serif; }
    body { background-color: #f3f4f6; color: var(--dark); padding-bottom: 40px; }

    header { background: var(--dark); color: white; padding: 15px 20px; display: flex; justify-content: space-between; align-items: center; box-shadow: 0 4px 6px -1px rgba(0,0,0,0.1); margin-bottom: 25px; }
    header h1 { font-size: 1.5rem; }
    .user-nav { display: flex; align-items: center; gap: 15px; }
    .user-nav span { font-size: 0.9rem; color: #9ca3af; }
    .btn-auth { background: #3b82f6; color: white; border: none; padding: 8px 16px; border-radius: 6px; cursor: pointer; font-weight: 600; font-size: 0.85rem; }
    .btn-auth:hover { background: #2563eb; }
    .btn-logout { background: #ef4444; }

    .main-layout { max-width: 1100px; margin: 0 auto; display: grid; grid-template-columns: 1fr; gap: 25px; padding: 0 15px; }
    @media (min-width: 768px) { .main-layout { grid-template-columns: 3fr 2fr; } }

    .card { background: white; padding: 20px; border-radius: 12px; border: 1px solid var(--border); box-shadow: 0 1px 3px rgba(0,0,0,0.05); margin-bottom: 20px; }
    .section-title { font-size: 1.25rem; font-weight: 700; margin-bottom: 15px; color: var(--dark); border-bottom: 2px solid var(--border); padding-bottom: 8px; display: flex; justify-content: space-between; align-items: center; }

    /* Modal / Auth Popups */
    .modal-overlay { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.5); z-index: 1000; justify-content: center; align-items: center; }
    .modal { background: white; padding: 25px; border-radius: 12px; width: 100%; max-width: 400px; box-shadow: 0 10px 25px rgba(0,0,0,0.2); }
    .modal h2 { margin-bottom: 15px; font-size: 1.3rem; }
    .tab-buttons { display: flex; margin-bottom: 15px; border-bottom: 1px solid var(--border); }
    .tab-btn { flex: 1; padding: 10px; border: none; background: none; cursor: pointer; font-weight: 600; color: var(--gray); }
    .tab-btn.active { border-bottom: 2px solid var(--dark); color: var(--dark); }

    /* Admin Panel Styling */
    .admin-card { background: var(--admin-bg); border: 1px solid var(--admin-border); display: none; }
    .admin-form { display: grid; grid-template-columns: 2fr 1fr 1fr; gap: 10px; margin-top: 10px; }
    .btn-admin { background: #2563eb; color: white; border: none; padding: 10px; border-radius: 6px; font-weight: 600; cursor: pointer; }

    /* Products Grid */
    .products-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(180px, 1fr)); gap: 15px; }
    .product-card { border: 1px solid var(--border); border-radius: 8px; padding: 15px; text-align: center; background: #fff; position: relative; }
    .product-card h4 { font-size: 1rem; margin-bottom: 5px; }
    .product-card .price { color: #059669; font-weight: 700; margin-bottom: 10px; }
    .btn-add { background: var(--dark); color: white; border: none; padding: 8px 12px; border-radius: 6px; cursor: pointer; font-size: 0.85rem; width: 100%; }
    .btn-add:hover { background: #374151; }
    .btn-delete { background: #ef4444; color: white; border: none; padding: 2px 6px; border-radius: 4px; cursor: pointer; font-size: 0.75rem; position: absolute; top: 8px; right: 8px; }

    /* Cart Section */
    .cart-list { list-style: none; margin-bottom: 15px; }
    .cart-item { display: flex; justify-content: space-between; align-items: center; padding: 10px 0; border-bottom: 1px dashed var(--border); font-size: 0.9rem; }
    .cart-total { display: flex; justify-content: space-between; font-weight: 700; font-size: 1.1rem; margin-top: 15px; padding-top: 10px; border-top: 2px solid var(--dark); }

    /* Forms & Map */
    .form-group { margin-bottom: 15px; }
    .form-group label { display: block; font-size: 0.875rem; font-weight: 600; margin-bottom: 5px; }
    .form-group input, .form-group textarea { width: 100%; padding: 10px; border: 1px solid var(--border); border-radius: 6px; font-size: 0.9rem; outline: none; }

    #map { height: 220px; width: 100%; border-radius: 8px; border: 1px solid var(--border); margin-top: 8px; }
    .status-msg { font-size: 0.8rem; font-weight: 600; color: #2563eb; margin-top: 5px; }

    .btn-whatsapp { background: var(--primary); color: white; border: none; padding: 14px; width: 100%; font-size: 1rem; font-weight: 700; border-radius: 8px; cursor: pointer; display: flex; align-items: center; justify-content: center; gap: 8px; margin-top: 15px; }
    .btn-whatsapp:hover { background: var(--primary-dark); }
  </style>
</head>
<body>

  <!-- HEADER -->
  <header>
    <h1>Global Express</h1>
    <div class="user-nav">
      <span id="userGreeting">Guest User</span>
      <button id="authBtn" class="btn-auth" onclick="openAuthModal()">Sign In / Sign Up</button>
    </div>
  </header>

  <div class="main-layout">
    
    <!-- LEFT COLUMN -->
    <div>
      <!-- ADMIN PANEL (Hidden until unlocked) -->
      <div id="adminPanel" class="card admin-card">
        <h2 class="section-title" style="color: #1e40af;">⚙️ Admin Panel: Add Product</h2>
        <div class="admin-form">
          <input type="text" id="newProdName" placeholder="Product Name" />
          <input type="number" id="newProdPrice" placeholder="Price (RWF)" />
          <button class="btn-admin" onclick="addNewProduct()">+ Add Item</button>
        </div>
      </div>

      <!-- PRODUCTS CATALOG -->
      <div class="card">
        <h2 class="section-title">
          <span>Available Products</span>
          <button style="background: none; border: none; cursor: pointer; font-size: 0.8rem; color: var(--gray);" onclick="promptAdminLogin()">Admin Mode 🔑</button>
        </h2>
        <div id="productsGrid" class="products-grid"></div>
      </div>
    </div>

    <!-- RIGHT COLUMN -->
    <div>
      <!-- CART -->
      <div class="card">
        <h2 class="section-title">Shopping Cart</h2>
        <ul id="cartList" class="cart-list">
          <li style="color: var(--gray); font-size: 0.9rem;">No items added yet.</li>
        </ul>
        <div class="cart-total">
          <span>Total:</span>
          <span id="cartTotal">0 RWF</span>
        </div>
      </div>

      <!-- DELIVERY FORM -->
      <div class="card">
        <h2 class="section-title">Delivery Information</h2>
        <form id="checkoutForm">
          <div class="form-group">
            <label for="fullName">Full Name</label>
            <input type="text" id="fullName" placeholder="Enter your full name" required />
          </div>

          <div class="form-group">
            <label for="instructions">Delivery Notes / Landmarks</label>
            <textarea id="instructions" rows="2" placeholder="House number, landmark, etc."></textarea>
          </div>

          <div class="form-group">
            <label>Live GPS Location</label>
            <div id="status" class="status-msg">Acquiring GPS location...</div>
            <div id="map"></div>
          </div>

          <button type="button" class="btn-whatsapp" onclick="sendOrderToWhatsApp()">
            Send Order via WhatsApp
          </button>
        </form>
      </div>
    </div>

  </div>

  <!-- AUTHENTICATION MODAL (SIGN UP / LOGIN) -->
  <div id="authModal" class="modal-overlay">
    <div class="modal">
      <div class="tab-buttons">
        <button id="tabLoginBtn" class="tab-btn active" onclick="switchTab('login')">Sign In</button>
        <button id="tabSignupBtn" class="tab-btn" onclick="switchTab('signup')">Sign Up</button>
      </div>

      <!-- LOGIN FORM -->
      <form id="loginForm" onsubmit="handleLogin(event)">
        <div class="form-group">
          <label>Email Address</label>
          <input type="email" id="loginEmail" required placeholder="name@example.com" />
        </div>
        <div class="form-group">
          <label>Password</label>
          <input type="password" id="loginPassword" required placeholder="••••••••" />
        </div>
        <button type="submit" class="btn-auth" style="width: 100%; padding: 12px;">Sign In</button>
      </form>

      <!-- SIGNUP FORM -->
      <form id="signupForm" style="display: none;" onsubmit="handleSignup(event)">
        <div class="form-group">
          <label>Full Name</label>
          <input type="text" id="signupName" required placeholder="John Doe" />
        </div>
        <div class="form-group">
          <label>Email Address</label>
          <input type="email" id="signupEmail" required placeholder="name@example.com" />
        </div>
        <div class="form-group">
          <label>Create Password</label>
          <input type="password" id="signupPassword" required placeholder="••••••••" />
        </div>
        <button type="submit" class="btn-auth" style="width: 100%; padding: 12px; background: #059669;">Create Account</button>
      </form>
      <button onclick="closeAuthModal()" style="margin-top: 15px; background: none; border: none; color: var(--gray); cursor: pointer; width: 100%;">Close</button>
    </div>
  </div>

  <!-- Leaflet JS -->
  <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>

  <script>
    const merchantPhone = "250780073151";
    const adminSecret = "admin123"; // Admin password
    
    // Initial State
    let products = JSON.parse(localStorage.getItem("app_products")) || [
      { id: 1, name: "Fresh Fruit Basket", price: 15000 },
      { id: 2, name: "Organic Coffee Beans", price: 12000 },
      { id: 3, name: "Premium Honey", price: 8000 }
    ];
    let users = JSON.parse(localStorage.getItem("app_users")) || [];
    let currentUser = JSON.parse(localStorage.getItem("app_current_user")) || null;
    let cart = [];
    let map, marker, userLat = null, userLng = null;

    // AUTHENTICATION LOGIC
    function updateAuthUI() {
      const greeting = document.getElementById("userGreeting");
      const authBtn = document.getElementById("authBtn");
      const fullNameInput = document.getElementById("fullName");

      if (currentUser) {
        greeting.innerText = `Welcome, ${currentUser.name}`;
        fullNameInput.value = currentUser.name;
        authBtn.innerText = "Log Out";
        authBtn.classList.add("btn-logout");
        authBtn.onclick = logout;
      } else {
        greeting.innerText = "Guest User";
        authBtn.innerText = "Sign In / Sign Up";
        authBtn.classList.remove("btn-logout");
        authBtn.onclick = openAuthModal;
      }
    }

    function openAuthModal() { document.getElementById("authModal").style.display = "flex"; }
    function closeAuthModal() { document.getElementById("authModal").style.display = "none"; }

    function switchTab(type) {
      if (type === 'login') {
        document.getElementById("loginForm").style.display = "block";
        document.getElementById("signupForm").style.display = "none";
        document.getElementById("tabLoginBtn").classList.add("active");
        document.getElementById("tabSignupBtn").classList.remove("active");
      } else {
        document.getElementById("loginForm").style.display = "none";
        document.getElementById("signupForm").style.display = "block";
        document.getElementById("tabSignupBtn").classList.add("active");
        document.getElementById("tabLoginBtn").classList.remove("active");
      }
    }

    function handleSignup(e) {
      e.preventDefault();
      const name = document.getElementById("signupName").value.trim();
      const email = document.getElementById("signupEmail").value.trim();
      const password = document.getElementById("signupPassword").value.trim();

      if (users.find(u => u.email === email)) {
        alert("An account with this email already exists!");
        return;
      }

      const newUser = { id: Date.now(), name, email, password };
      users.push(newUser);
      localStorage.setItem("app_users", JSON.stringify(users));
      currentUser = newUser;
      localStorage.setItem("app_current_user", JSON.stringify(currentUser));

      alert("Account created successfully!");
      closeAuthModal();
      updateAuthUI();
    }

    function handleLogin(e) {
      e.preventDefault();
      const email = document.getElementById("loginEmail").value.trim();
      const password = document.getElementById("loginPassword").value.trim();

      const user = users.find(u => u.email === email && u.password === password);
      if (!user) {
        alert("Invalid email or password!");
        return;
      }

      currentUser = user;
      localStorage.setItem("app_current_user", JSON.stringify(currentUser));
      closeAuthModal();
      updateAuthUI();
    }

    function logout() {
      currentUser = null;
      localStorage.removeItem("app_current_user");
      document.getElementById("fullName").value = "";
      updateAuthUI();
    }

    // ADMIN MODE
    function promptAdminLogin() {
      const pass = prompt("Enter Admin Password:");
      if (pass === adminSecret) {
        document.getElementById("adminPanel").style.display = "block";
        alert("Admin Mode unlocked!");
      } else if (pass) {
        alert("Incorrect password!");
      }
    }

    function addNewProduct() {
      const name = document.getElementById("newProdName").value.trim();
      const price = parseFloat(document.getElementById("newProdPrice").value);

      if (!name || isNaN(price) || price <= 0) {
        alert("Enter valid details!");
        return;
      }

      products.push({ id: Date.now(), name, price });
      localStorage.setItem("app_products", JSON.stringify(products));
      renderProducts();
      document.getElementById("newProdName").value = "";
      document.getElementById("newProdPrice").value = "";
    }

    function deleteProduct(id) {
      if (confirm("Delete this product?")) {
        products = products.filter(p => p.id !== id);
        localStorage.setItem("app_products", JSON.stringify(products));
        renderProducts();
      }
    }

    function renderProducts() {
      const grid = document.getElementById("productsGrid");
      grid.innerHTML = products.length === 0 ? `<p style="color: var(--gray);">No products available.</p>` : "";
      
      products.forEach(product => {
        grid.innerHTML += `
          <div class="product-card">
            <button class="btn-delete" onclick="deleteProduct(${product.id})">✕</button>
            <h4>${product.name}</h4>
            <p class="price">${product.price.toLocaleString()} RWF</p>
            <button class="btn-add" onclick="addToCart('${product.name}', ${product.price})">+ Add to Cart</button>
          </div>
        `;
      });
    }

    // CART
    function addToCart(name, price) {
      const existing = cart.find(item => item.name === name);
      if (existing) { existing.quantity += 1; }
      else { cart.push({ name, price, quantity: 1 }); }
      renderCart();
    }

    function renderCart() {
      const cartList = document.getElementById("cartList");
      const cartTotal = document.getElementById("cartTotal");
      if (cart.length === 0) {
        cartList.innerHTML = `<li style="color: var(--gray); font-size: 0.9rem;">No items added yet.</li>`;
        cartTotal.innerText = "0 RWF";
        return;
      }
      cartList.innerHTML = "";
      let total = 0;
      cart.forEach(item => {
        const itemTotal = item.price * item.quantity;
        total += itemTotal;
        cartList.innerHTML += `
          <li class="cart-item">
            <div>
              <strong>${item.name}</strong><br>
              <small>${item.quantity} x ${item.price.toLocaleString()} RWF</small>
            </div>
            <span>${itemTotal.toLocaleString()} RWF</span>
          </li>
        `;
      });
      cartTotal.innerText = `${total.toLocaleString()} RWF`;
    }

    // MAP & GPS
    function initMap() {
      const defaultLat = -1.9441, defaultLng = 30.0619;
      map = L.map('map').setView([defaultLat, defaultLng], 14);
      L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png').addTo(map);
      marker = L.marker([defaultLat, defaultLng]).addTo(map);

      if ("geolocation" in navigator) {
        navigator.geolocation.watchPosition(pos => {
          userLat = pos.coords.latitude; userLng = pos.coords.longitude;
          const latLng = new L.LatLng(userLat, userLng);
          marker.setLatLng(latLng); map.setView(latLng, 16);
          document.getElementById("status").innerText = "✓ Live GPS captured!";
          document.getElementById("status").style.color = "#059669";
        }, () => {
          document.getElementById("status").innerText = "⚠ Enable GPS in browser settings.";
          document.getElementById("status").style.color = "#dc2626";
        }, { enableHighAccuracy: true });
      }
    }

    // WHATSAPP ORDER
    function sendOrderToWhatsApp() {
      if (!currentUser) {
        alert("Please Sign In or Sign Up before placing an order!");
        openAuthModal();
        return;
      }

      const name = document.getElementById("fullName").value.trim();
      const notes = document.getElementById("instructions").value.trim();

      if (!name || cart.length === 0) {
        alert("Please fill in your name and add items to your cart.");
        return;
      }

      let itemsSummary = "", totalAmount = 0;
      cart.forEach((item, index) => {
        const cost = item.price * item.quantity;
        totalAmount += cost;
        itemsSummary += `${index + 1}. ${item.name} (${item.quantity}x) - ${cost.toLocaleString()} RWF%0A`;
      });

      const locationLink = userLat && userLng ? `https://maps.google.com/?q=${userLat},${userLng}` : "Location not captured";

      const message = `*NEW ORDER RECEIVED*%0A%0A` +
                      `*Customer:* ${encodeURIComponent(name)} (${encodeURIComponent(currentUser.email)})%0A` +
                      `*Order Details:*%0A${itemsSummary}` +
                      `*Total Amount:* ${totalAmount.toLocaleString()} RWF%0A%0A` +
                      `*Notes:* ${encodeURIComponent(notes || "None")}%0A` +
                      `*Live Location:* ${encodeURIComponent(locationLink)}`;

      window.open(`https://wa.me/${merchantPhone}?text=${message}`, '_blank');
    }

    window.onload = function() {
      renderProducts();
      updateAuthUI();
      initMap();
    };
  </script>
</body>
</html>

