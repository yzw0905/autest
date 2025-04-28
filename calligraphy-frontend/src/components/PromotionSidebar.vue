<template>
  <div class="promotion-sidebar" :class="{ 'expanded': isExpanded }">
    <div class="toggle-button" @click="toggleSidebar">
      {{ isExpanded ? '<<' : '>>' }}
    </div>
    <div class="sidebar-content">
      <h3 class="sidebar-title">今日特惠</h3>
      <div class="promotion-items">
        <div v-for="(item, index) in promotionItems" :key="index" class="promotion-item" @click="viewProductDetails(item.productId)">
          <div class="item-image">
            <img :src="getCartoonImage(item.imagePath)" :alt="item.productName" />
          </div>
          <div class="item-info">
            <div class="item-name">{{ item.productName }}</div>
            <div class="item-price">
              <span class="original-price">¥{{ item.originalPrice }}</span>
              <span class="current-price">¥{{ item.price }}</span>
            </div>
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
    const isExpanded = ref(false);
    
    // 模拟优惠商品数据
    const promotionItems = ref([
      {
        productId: 1,
        productName: '高级书法毛笔套装',
        price: 19.99,
        originalPrice: 29.99,
        imagePath: '/images/products/product1.jpg',
        discount: '6.7折'
      },
      {
        productId: 2,
        productName: '优质宣纸',
        price: 9.99,
        originalPrice: 15.99,
        imagePath: '/images/products/product2.jpg',
        discount: '6.2折'
      },
      {
        productId: 5,
        productName: '专业墨汁',
        price: 12.99,
        originalPrice: 18.99,
        imagePath: '/images/products/product5.jpg',
        discount: '6.8折'
      }
    ]);
    
    const toggleSidebar = () => {
      isExpanded.value = !isExpanded.value;
    };
    
    const viewProductDetails = (productId) => {
      router.push(`/products/${productId}`);
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
  left: -250px;
  top: 50%;
  transform: translateY(-50%);
  width: 250px;
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
  right: -30px;
  top: 50%;
  transform: translateY(-50%);
  width: 30px;
  height: 60px;
  background-color: var(--primary-color);
  color: white;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  border-radius: 0 4px 4px 0;
  font-weight: bold;
}

.sidebar-content {
  width: 100%;
  height: 100%;
  padding: 15px;
  overflow-y: auto;
}

.sidebar-title {
  color: var(--primary-color);
  font-size: 18px;
  margin-bottom: 15px;
  text-align: center;
  border-bottom: 1px solid #eee;
  padding-bottom: 10px;
}

.promotion-items {
  display: flex;
  flex-direction: column;
  gap: 15px;
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
  width: 60px;
  height: 60px;
  border-radius: 4px;
  overflow: hidden;
  margin-right: 10px;
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

.item-price {
  display: flex;
  align-items: center;
  gap: 5px;
}

.original-price {
  font-size: 12px;
  color: #999;
  text-decoration: line-through;
}

.current-price {
  font-size: 16px;
  font-weight: bold;
  color: var(--primary-color);
}
</style>
