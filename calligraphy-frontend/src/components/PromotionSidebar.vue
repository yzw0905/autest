<template>
  <div class="promotion-sidebar" :class="{ 'expanded': isExpanded }">
    <div class="toggle-button" @click="toggleSidebar">
      {{ isExpanded ? '<<' : '>>' }}
    </div>
    <div class="sidebar-content">
      <h3 class="sidebar-title">今日特惠商品</h3>
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
          </div>
          <div class="item-info">
            <div class="item-name">{{ item.productName }}</div>
          </div>
        </div>
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
        discount: '25% OFF'
      },
      {
        productId: 2,
        productName: 'Premium Ink',
        price: 15.99,
        originalPrice: 19.99,
        imagePath: '/images/products/product2.jpg',
        discount: '20% OFF'
      },
      {
        productId: 3,
        productName: 'Rice Paper',
        price: 12.99,
        originalPrice: 16.99,
        imagePath: '/images/products/product3.jpg',
        discount: '24% OFF'
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
  top: 200px; /* 调整位置，使其在页面上方 */
  width: 200px; /* 减小宽度 */
  height: auto;
  max-height: 80vh;
  background-color: white;
  box-shadow: 2px 0 8px rgba(0, 0, 0, 0.1);
  border-radius: 0 8px 8px 0;
  transition: left 0.3s ease;
  z-index: 1000;
  display: flex;
  flex-direction: row;
}

.promotion-sidebar.expanded {
  left: 0;
}

.toggle-button {
  position: absolute;
  right: -25px;
  top: 10px; /* 调整到顶部 */
  width: 25px;
  height: 40px;
  background-color: var(--primary-color);
  color: white;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  border-radius: 0 4px 4px 0;
  font-weight: bold;
  font-size: 12px;
}

.sidebar-content {
  width: 100%;
  height: 100%;
  padding: 10px;
  overflow-y: auto;
  max-height: 500px; /* 限制最大高度 */
}

.sidebar-title {
  color: var(--primary-color);
  font-size: 16px;
  margin-bottom: 10px;
  text-align: center;
  border-bottom: 1px solid #eee;
  padding-bottom: 8px;
}

.promotion-items {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.promotion-item {
  display: flex;
  align-items: center;
  padding: 10px;
  border-radius: 4px;
  background-color: #f9f9f9;
  cursor: pointer;
  transition: transform 0.2s ease, box-shadow 0.2s ease;
}

.promotion-item:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

.item-image {
  width: 50px;
  height: 50px;
  border-radius: 4px;
  overflow: hidden;
  margin-right: 8px;
  position: relative;
}

.item-image img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.item-info {
  flex: 1;
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
}

.image-price-overlay {
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  background-color: rgba(0, 0, 0, 0.6);
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 2px 4px;
}

.overlay-price {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
}

.overlay-original-price {
  font-size: 10px;
  color: #ccc;
  text-decoration: line-through;
}

.overlay-current-price {
  font-size: 12px;
  font-weight: bold;
  color: white;
}

.overlay-discount {
  font-size: 10px;
  background-color: var(--primary-color);
  color: white;
  padding: 1px 3px;
  border-radius: 2px;
}
</style>
