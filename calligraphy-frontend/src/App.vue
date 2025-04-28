<template>
  <div class="app-container">
    <!-- Promotion Sidebar -->
    <PromotionSidebar />
    <!-- Top navigation bar (JD style) -->
    <div class="top-nav">
      <div class="container">
        <div class="top-nav-left">
          <a href="/">Home</a>
          <a href="#">Location</a>
        </div>
        <div class="top-nav-right">
          <template v-if="!isLoggedIn">
            <a @click="$router.push('/login')">Hello, Sign In</a>
            <a @click="$router.push('/login')">Register</a>
          </template>
          <template v-else>
            <a>Welcome, {{ username }}</a>
            <a @click="logout">Sign Out</a>
          </template>
          <a @click="$router.push('/orders')">My Orders</a>
          <a v-if="isAdmin" @click="$router.push('/admin/products')">Product Management</a>
        </div>
      </div>
    </div>

    <!-- Main header with logo, search and cart (Modern E-commerce style) -->
    <div class="main-header">
      <div class="container">
        <div class="logo" @click="$router.push('/')">
          <span class="logo-text">Calligraphy</span>
          <span class="logo-accent">Art</span>
        </div>
        <div class="search-box">
          <div class="search-categories">
            <select>
              <option>All Categories</option>
              <option>Brushes</option>
              <option>Ink</option>
              <option>Paper</option>
              <option>Calligraphy Works</option>
            </select>
          </div>
          <input type="text" placeholder="Search for calligraphy products..." v-model="searchQuery" @keyup.enter="search" />
          <button @click="search">
            <i class="search-icon">🔍</i>
          </button>
        </div>
        <div class="header-actions">
          <div class="action-item wishlist" @click="$router.push('/wishlist')">
            <i class="action-icon">❤️</i>
            <span>Wishlist</span>
          </div>
          <div class="action-item shopping-cart" @click="$router.push('/cart')">
            <i class="cart-icon">🛒</i>
            <span>Cart</span>
            <div class="cart-count">0</div>
          </div>
        </div>
      </div>
    </div>

    <!-- Category navigation (Modern Mega Menu style) -->
    <div class="category-nav">
      <div class="container">
        <div class="category-menu">
          <div class="all-categories">
            <i class="menu-icon">☰</i>
            <span>All Categories</span>
          </div>
          <ul class="category-list">
            <li><router-link to="/products?category=brushes">Brushes</router-link></li>
            <li><router-link to="/products?category=ink">Ink</router-link></li>
            <li><router-link to="/products?category=paper">Rice Paper</router-link></li>
            <li><router-link to="/products?category=calligraphy works">Calligraphy Works</router-link></li>
            <li><router-link to="/products?category=accessories">Accessories</router-link></li>
            <li><router-link to="/products?category=sets">Calligraphy Sets</router-link></li>
            <li><router-link to="/products">All Products</router-link></li>
          </ul>
        </div>
        <div class="nav-promotions">
          <div class="promo-tag">
            <i class="promo-icon">🎁</i>
            <span>Flash Sale</span>
          </div>
          <div class="promo-tag">
            <i class="promo-icon">💳</i>
            <span>Coupons</span>
          </div>
        </div>
      </div>
    </div>

    <!-- Banner Carousel (New) -->
    <div class="banner-section" v-if="$route.path === '/' || $route.path === '/products'">
      <div class="container">
        <div class="banner-carousel">
          <img src="/images/banner1.jpg" alt="Special Offer" class="banner-image" />
        </div>
      </div>
    </div>

    <!-- Main content -->
    <div class="content-container">
      <router-view></router-view>
    </div>

    <!-- Footer (JD style) -->
    <div class="footer">
      <div class="container">
        <div class="footer-links">
          <div class="footer-section">
            <h4>Shopping Guide</h4>
            <a href="#">Shopping Process</a>
            <a href="#">Membership</a>
            <a href="#">FAQ</a>
          </div>
          <div class="footer-section">
            <h4>Delivery</h4>
            <a href="#">Store Pickup</a>
            <a href="#">Express Delivery</a>
            <a href="#">Delivery Service</a>
          </div>
          <div class="footer-section">
            <h4>Payment</h4>
            <a href="#">Cash on Delivery</a>
            <a href="#">Online Payment</a>
            <a href="#">Installment</a>
          </div>
          <div class="footer-section">
            <h4>After-Sales</h4>
            <a href="#">Return Policy</a>
            <a href="#">Price Protection</a>
            <a href="#">Refund Instructions</a>
          </div>
        </div>
        <div class="footer-bottom">
          <p>Calligraphy Art ©2025 All Rights Reserved</p>
        </div>
      </div>
    </div>
  </div>
</template>


<script>
import { computed, ref } from 'vue';
import { useStore } from 'vuex';
import { useRouter } from 'vue-router';
import PromotionSidebar from './components/PromotionSidebar.vue';

export default {
  name: 'App',
  components: {
    PromotionSidebar
  },
  setup() {
    const store = useStore();
    const router = useRouter();
    const searchQuery = ref('');

    const isLoggedIn = computed(() => store.getters.isLoggedIn);
    const isAdmin = computed(() => store.getters.isAdmin);
    const username = computed(() => {
      if (store.state.user) {
        return store.state.user.username || 'User';
      }
      return 'User';
    });

    const logout = () => {
      store.commit('logout');
      router.push('/login');
    };

    const search = () => {
      if (searchQuery.value.trim()) {
        router.push(`/products?search=${encodeURIComponent(searchQuery.value.trim())}`);
      }
    };

    return {
      searchQuery,
      isLoggedIn,
      isAdmin,
      username,
      logout,
      search
    };
  }
};
</script>

<style>
:root {
  --primary-color: #e1251b; /* JD red */
  --secondary-color: #f10215;
  --accent-color: #ff9900;
  --text-color: #333;
  --light-text: #666;
  --light-bg: #f5f5f5;
  --border-color: #e3e4e5;
  --hover-color: #c81623;
  --card-shadow: 0 2px 8px rgba(0, 0, 0, 0.08);
  --hover-shadow: 0 4px 12px rgba(0, 0, 0, 0.12);
  --border-radius: 4px;
  --transition-speed: 0.3s;
  --container-width: 1190px;
  --header-height: 100px;
  --nav-height: 45px;
}

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'Segoe UI', 'Microsoft YaHei', Roboto, Arial, sans-serif;
  background-color: var(--light-bg);
  color: var(--text-color);
  line-height: 1.5;
}

.app-container {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}

.container {
  width: 100%;
  max-width: var(--container-width);
  margin: 0 auto;
  padding: 0 15px;
}

/* Top navigation bar */
.top-nav {
  background-color: #e3e4e5;
  color: #999;
  font-size: 12px;
  height: 30px;
  line-height: 30px;
  border-bottom: 1px solid #ddd;
}

.top-nav .container {
  display: flex;
  justify-content: space-between;
}

.top-nav a {
  color: #999;
  text-decoration: none;
  margin-right: 10px;
  cursor: pointer;
}

.top-nav a:hover {
  color: var(--primary-color);
}

.top-nav-right a {
  margin-left: 10px;
  margin-right: 0;
}

/* Main header */
.main-header {
  background-color: white;
  height: var(--header-height);
  display: flex;
  align-items: center;
  box-shadow: 0 1px 3px rgba(0,0,0,0.08);
  padding: 20px 0;
}

.main-header .container {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 0 10px;
}

.logo {
  display: flex;
  align-items: center;
  cursor: pointer;
}

.logo-text {
  font-size: 28px;
  font-weight: bold;
  color: var(--text-color);
}

.logo-accent {
  font-size: 28px;
  font-weight: bold;
  color: var(--primary-color);
  margin-left: 4px;
}

.search-box {
  display: flex;
  width: 550px;
  height: 40px;
  border-radius: var(--border-radius);
  overflow: hidden;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.05);
}

.search-categories {
  width: 130px;
  background-color: #f5f5f5;
  border-right: 1px solid var(--border-color);
}

.search-categories select {
  width: 100%;
  height: 100%;
  padding: 0 10px;
  border: none;
  background-color: transparent;
  font-size: 13px;
  color: var(--light-text);
  cursor: pointer;
  outline: none;
}

.search-box input {
  flex: 1;
  height: 100%;
  padding: 0 15px;
  border: 1px solid var(--border-color);
  border-left: none;
  border-right: none;
  outline: none;
  font-size: 14px;
}

.search-box button {
  width: 50px;
  height: 100%;
  background-color: var(--primary-color);
  color: white;
  border: none;
  font-size: 18px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.search-box button:hover {
  background-color: var(--hover-color);
  transition: background-color var(--transition-speed);
}

.header-actions {
  display: flex;
  gap: 20px;
}

.action-item {
  display: flex;
  flex-direction: column;
  align-items: center;
  cursor: pointer;
  position: relative;
}

.action-icon, .cart-icon {
  font-size: 20px;
  margin-bottom: 4px;
}

.action-item span {
  font-size: 12px;
  color: var(--light-text);
}

.cart-count {
  position: absolute;
  top: -5px;
  right: -5px;
  background-color: var(--primary-color);
  color: white;
  font-size: 10px;
  width: 16px;
  height: 16px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
}

/* Category navigation */
.category-nav {
  background-color: var(--primary-color);
  height: var(--nav-height);
  line-height: var(--nav-height);
}

.category-nav .container {
  display: flex;
  justify-content: space-between;
  align-items: center;
  height: 100%;
}

.category-menu {
  display: flex;
  height: 100%;
  flex: 1;
}

.all-categories {
  display: flex;
  align-items: center;
  padding: 0 20px;
  background-color: rgba(0, 0, 0, 0.1);
  color: white;
  font-weight: bold;
  height: 100%;
  cursor: pointer;
}

.menu-icon {
  margin-right: 8px;
}

.category-list {
  display: flex;
  list-style: none;
  height: 100%;
}

.category-list li {
  margin-right: 0;
  height: 100%;
}

.category-list a {
  display: flex;
  align-items: center;
  height: 100%;
  padding: 0 20px;
  color: white;
  text-decoration: none;
  font-size: 14px;
  font-weight: 500;
  transition: background-color var(--transition-speed);
}

.category-list a:hover {
  background-color: var(--hover-color);
  color: white;
}

.nav-promotions {
  display: flex;
  gap: 15px;
}

.promo-tag {
  display: flex;
  align-items: center;
  color: white;
  font-size: 13px;
  cursor: pointer;
}

.promo-icon {
  margin-right: 5px;
}

/* Banner Section */
.banner-section {
  padding: 20px 0;
  background-color: white;
}

.banner-carousel {
  width: 100%;
  height: 300px;
  border-radius: 8px;
  overflow: hidden;
  box-shadow: var(--card-shadow);
}

.banner-image {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

/* Content container */
.content-container {
  flex: 1;
  padding: 20px 0;
  background-color: var(--light-bg);
}

/* Footer */
.footer {
  background-color: #eaeaea;
  padding: 30px 0;
  margin-top: 20px;
}

.footer-links {
  display: flex;
  justify-content: space-between;
  margin-bottom: 20px;
}

.footer-section {
  display: flex;
  flex-direction: column;
}

.footer-section h4 {
  margin-bottom: 10px;
  font-size: 14px;
  color: #666;
}

.footer-section a {
  color: #999;
  text-decoration: none;
  font-size: 12px;
  margin-bottom: 5px;
}

.footer-section a:hover {
  color: var(--primary-color);
}

.footer-bottom {
  text-align: center;
  padding-top: 15px;
  border-top: 1px solid #ddd;
  color: #999;
  font-size: 12px;
}

/* Import animations from global.css */
@keyframes fadeIn {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}

@keyframes slideUp {
  from {
    transform: translateY(20px);
    opacity: 0;
  }
  to {
    transform: translateY(0);
    opacity: 1;
  }
}
</style>
