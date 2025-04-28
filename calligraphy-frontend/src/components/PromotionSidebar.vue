<template>
  <div class="promotion-sidebar" :class="{ 'expanded': isExpanded }">
    <div class="toggle-button" @click="toggleSidebar">
      <i class="toggle-icon">{{ isExpanded ? '◀' : '▶' }}</i>
    </div>
    <div class="sidebar-content">
      <div class="sidebar-header">
        <i class="promo-icon">🔥</i>
        <h3 class="sidebar-title">Today's Deals</h3>
      </div>
      <div class="promotion-items">
        <div v-for="(item, index) in promotionItems" :key="index" class="promotion-item" @click="viewProductDetails(item.productId)">
          <div class="item-image">
            <img :src="getCartoonImage(item.imagePath)" :alt="item.productName" />
            <div class="image-price-overlay">
              <div class="overlay-price">
                <div class="overlay-original-price">¥{{ item.originalPrice }}</div>
                <div class="overlay-current-price">¥{{ item.price }}</div>
              </div>
              <div class="overlay-discount">{{ item.discount }}</div>
            </div>
            <div class="item-badge">HOT</div>
          </div>
          <div class="item-info">
            <div class="item-name">{{ item.productName }}</div>
            <div class="item-rating">
              <span class="stars">★★★★☆</span>
              <span class="review-count">({{ 10 + index * 5 }})</span>
            </div>
          </div>
        </div>
      </div>
      <div class="view-more-button">
        <a href="/products">View All Deals</a>
      </div>
    </div>
  </div>
</template>

<script>
import { ref } from 'vue';
import { useRouter } from 'vue-router';
import { getCartoonImage } from '../utils/imageUtils';

export default {
  name: 'PromotionSidebar',
  setup() {
    const router = useRouter();
    const isExpanded = ref(true); // 默认展开

    // 模拟优惠商品数据 - 与实际商品价格对应
    const promotionItems = ref([
      {
        productId: 1,
        productName: 'Calligraphy Brush Set',
        price: 29.99,
        originalPrice: 39.99,
        imagePath: '/images/products/product1.jpg',
        discount: '25% OFF',
        rating: 4.5,
        reviews: 42
      },
      {
        productId: 2,
        productName: 'Premium Ink',
        price: 15.99,
        originalPrice: 19.99,
        imagePath: '/images/products/product2.jpg',
        discount: '20% OFF',
        rating: 4.7,
        reviews: 28
      },
      {
        productId: 3,
        productName: 'Rice Paper',
        price: 12.99,
        originalPrice: 16.99,
        imagePath: '/images/products/product3.jpg',
        discount: '24% OFF',
        rating: 4.3,
        reviews: 15
      }
    ]);

    const toggleSidebar = () => {
      isExpanded.value = !isExpanded.value;
    };

    const viewProductDetails = (productId) => {
      // 强制刷新页面来确保跳转正常
      window.location.href = `/products/${productId}`;
    };

    return {
      isExpanded,
      promotionItems,
      toggleSidebar,
      viewProductDetails,
      getCartoonImage
    };
  }
};
</script>

<style scoped>
.promotion-sidebar {
  position: fixed;
  left: 0;
  top: 180px; /* 调整位置，使其在页面上方 */
  width: 220px; /* 适当宽度 */
  height: auto;
  max-height: 80vh;
  background-color: white;
  box-shadow: 2px 0 8px rgba(0, 0, 0, 0.1);
  border-radius: 0 8px 8px 0;
  transition: all var(--transition-speed);
  z-index: 1000;
  display: flex;
  flex-direction: row;
  border: 1px solid var(--border-color);
  border-left: none;
}

.promotion-sidebar.expanded {
  left: 0;
}

.toggle-button {
  position: absolute;
  right: -28px;
  top: 50%;
  transform: translateY(-50%);
  width: 28px;
  height: 50px;
  background-color: var(--primary-color);
  color: white;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  border-radius: 0 4px 4px 0;
  font-weight: bold;
  font-size: 12px;
  box-shadow: 2px 0 5px rgba(0, 0, 0, 0.1);
  transition: all var(--transition-speed);
}

.toggle-button:hover {
  background-color: var(--hover-color);
}

.toggle-icon {
  font-size: 14px;
}

.sidebar-content {
  width: 100%;
  height: 100%;
  padding: 0;
  overflow-y: auto;
  max-height: 500px; /* 限制最大高度 */
  display: flex;
  flex-direction: column;
}

.sidebar-header {
  display: flex;
  align-items: center;
  padding: 12px 15px;
  background-color: var(--primary-color);
  color: white;
}

.promo-icon {
  margin-right: 8px;
  font-size: 18px;
}

.sidebar-title {
  font-size: 16px;
  font-weight: bold;
  margin: 0;
}

.promotion-items {
  display: flex;
  flex-direction: column;
  padding: 10px;
}

.promotion-item {
  display: flex;
  flex-direction: column;
  margin-bottom: 15px;
  border-radius: var(--border-radius);
  background-color: white;
  cursor: pointer;
  transition: all var(--transition-speed);
  border: 1px solid var(--border-color);
}

.promotion-item:hover {
  transform: translateY(-2px);
  box-shadow: var(--hover-shadow);
}

.item-image {
  width: 100%;
  height: 120px;
  overflow: hidden;
  position: relative;
  border-radius: var(--border-radius) var(--border-radius) 0 0;
}

.item-image img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.item-info {
  padding: 10px;
}

.item-name {
  font-size: 14px;
  font-weight: 500;
  margin-bottom: 5px;
  color: #333;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  overflow: hidden;
  height: 40px;
}

.image-price-overlay {
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  background-color: rgba(0, 0, 0, 0.7);
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 5px 8px;
}

.overlay-price {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
}

.overlay-original-price {
  font-size: 11px;
  color: #ccc;
  text-decoration: line-through;
}

.overlay-current-price {
  font-size: 14px;
  font-weight: bold;
  color: white;
}

.overlay-discount {
  font-size: 12px;
  background-color: var(--accent-color);
  color: white;
  padding: 2px 6px;
  border-radius: 2px;
  font-weight: bold;
}

.item-badge {
  position: absolute;
  top: 10px;
  right: 10px;
  background-color: var(--primary-color);
  color: white;
  font-size: 11px;
  font-weight: bold;
  padding: 3px 6px;
  border-radius: 2px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
}

.item-rating {
  display: flex;
  align-items: center;
  font-size: 12px;
  color: var(--light-text);
}

.stars {
  color: var(--accent-color);
  margin-right: 4px;
}

.view-more-button {
  padding: 10px 15px;
  text-align: center;
  border-top: 1px solid var(--border-color);
  margin-top: auto;
}

.view-more-button a {
  color: var(--primary-color);
  font-size: 14px;
  font-weight: 500;
  text-decoration: none;
}

.view-more-button a:hover {
  text-decoration: underline;
}
</style>
