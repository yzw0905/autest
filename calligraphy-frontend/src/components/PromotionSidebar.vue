<template>
  <div class="promotion-sidebar" :class="{ 'expanded': isExpanded }" ref="sidebarRef" :style="sidebarStyle">
    <div class="toggle-button" @click="toggleSidebar">
      <i class="toggle-icon">{{ isExpanded ? '◀' : '▶' }}</i>
    </div>
    <div class="sidebar-content">
      <div class="sidebar-header" @mousedown="startDrag" @touchstart="startDrag">
        <i class="promo-icon">🔥</i>
        <h3 class="sidebar-title">Today's Deals</h3>
        <div class="drag-handle">☰</div>
      </div>
      <div class="promotion-items">
        <transition-group name="slide" tag="div" class="carousel-container">
          <div v-for="(item, index) in visibleItems" :key="item.productId" class="promotion-item" @click="viewProductDetails(item.productId)">
            <div class="item-image">
              <img :src="getCartoonImage(item.imagePath)" :alt="item.productName" />
              <div class="item-hot-badge">HOT</div>
              <div class="item-price-popup">
                <div class="popup-original-price">¥{{ item.originalPrice }}</div>
                <div class="popup-current-price">¥{{ item.price }}</div>
                <div class="popup-discount">{{ item.discount }}</div>
              </div>
            </div>
            <div class="item-info">
              <div class="item-name">{{ item.productName }}</div>
              <div class="item-rating">
                <span class="stars">★★★★☆</span>
                <span class="review-count">({{ 10 + index * 5 }})</span>
              </div>
            </div>
          </div>
        </transition-group>
        <div class="carousel-controls">
          <div class="carousel-dots">
            <span
              v-for="(_, index) in Math.ceil(promotionItems.length / itemsPerPage)"
              :key="index"
              :class="{ 'active': Math.floor(currentIndex / itemsPerPage) === index }"
              @click="goToPage(index)"
              class="carousel-dot"
            ></span>
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
import { ref, computed, onMounted, onUnmounted } from 'vue';
import { useRouter } from 'vue-router';
import { getCartoonImage } from '../utils/imageUtils';

export default {
  name: 'PromotionSidebar',
  setup() {
    const router = useRouter();
    const isExpanded = ref(true); // 默认展开
    const sidebarRef = ref(null);
    const isDragging = ref(false);
    const dragOffset = ref({ x: 0, y: 0 });
    const position = ref({ x: 0, y: 180 }); // 初始位置
    const currentIndex = ref(0);
    const itemsPerPage = 1; // 每页显示的商品数量
    const autoRotateInterval = ref(null);

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
      },
      {
        productId: 4,
        productName: 'Calligraphy Practice Book',
        price: 18.99,
        originalPrice: 24.99,
        imagePath: '/images/products/product4.jpg',
        discount: '24% OFF',
        rating: 4.6,
        reviews: 37
      }
    ]);

    // 计算当前可见的商品
    const visibleItems = computed(() => {
      const startIndex = currentIndex.value;
      return promotionItems.value.slice(startIndex, startIndex + itemsPerPage);
    });

    // 计算侧边栏样式
    const sidebarStyle = computed(() => {
      return {
        left: `${position.value.x}px`,
        top: `${position.value.y}px`
      };
    });

    // 开始拖动
    const startDrag = (event) => {
      if (event.target.closest('.drag-handle')) {
        isDragging.value = true;

        // 记录鼠标位置和当前元素位置的偏移量
        const clientX = event.clientX || (event.touches && event.touches[0].clientX);
        const clientY = event.clientY || (event.touches && event.touches[0].clientY);

        dragOffset.value = {
          x: clientX - position.value.x,
          y: clientY - position.value.y
        };

        // 添加事件监听器
        document.addEventListener('mousemove', onDrag);
        document.addEventListener('touchmove', onDrag);
        document.addEventListener('mouseup', stopDrag);
        document.addEventListener('touchend', stopDrag);

        // 防止默认行为
        event.preventDefault();
      }
    };

    // 拖动中
    const onDrag = (event) => {
      if (isDragging.value) {
        const clientX = event.clientX || (event.touches && event.touches[0].clientX);
        const clientY = event.clientY || (event.touches && event.touches[0].clientY);

        position.value = {
          x: clientX - dragOffset.value.x,
          y: clientY - dragOffset.value.y
        };
      }
    };

    // 停止拖动
    const stopDrag = () => {
      isDragging.value = false;
      document.removeEventListener('mousemove', onDrag);
      document.removeEventListener('touchmove', onDrag);
      document.removeEventListener('mouseup', stopDrag);
      document.removeEventListener('touchend', stopDrag);
    };

    // 切换到下一页
    const nextItem = () => {
      currentIndex.value = (currentIndex.value + 1) % promotionItems.value.length;
    };

    // 切换到上一页
    const prevItem = () => {
      currentIndex.value = (currentIndex.value - 1 + promotionItems.value.length) % promotionItems.value.length;
    };

    // 切换到指定页
    const goToPage = (pageIndex) => {
      currentIndex.value = pageIndex * itemsPerPage;
    };

    // 开始自动轮播
    const startAutoRotate = () => {
      stopAutoRotate(); // 先停止之前的轮播
      autoRotateInterval.value = setInterval(() => {
        nextItem();
      }, 3000); // 每3秒切换一次
    };

    // 停止自动轮播
    const stopAutoRotate = () => {
      if (autoRotateInterval.value) {
        clearInterval(autoRotateInterval.value);
        autoRotateInterval.value = null;
      }
    };

    const toggleSidebar = () => {
      isExpanded.value = !isExpanded.value;
    };

    const viewProductDetails = (productId) => {
      // 强制刷新页面来确保跳转正常
      window.location.href = `/products/${productId}`;
    };

    // 组件挂载时启动自动轮播
    onMounted(() => {
      startAutoRotate();
    });

    // 组件卸载时清除定时器
    onUnmounted(() => {
      stopAutoRotate();
    });

    return {
      sidebarRef,
      isExpanded,
      promotionItems,
      visibleItems,
      currentIndex,
      sidebarStyle,
      toggleSidebar,
      startDrag,
      viewProductDetails,
      getCartoonImage,
      nextItem,
      prevItem,
      goToPage,
      itemsPerPage
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
  border-radius: 8px;
  transition: all var(--transition-speed);
  z-index: 1000;
  display: flex;
  flex-direction: row;
  border: 1px solid var(--border-color);
  user-select: none; /* 防止拖动时选中文本 */
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
  cursor: move;
  border-radius: 8px 8px 0 0;
  justify-content: space-between;
}

.drag-handle {
  font-size: 16px;
  cursor: move;
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
  position: relative;
}

.carousel-container {
  position: relative;
  overflow: hidden;
}

.carousel-controls {
  display: flex;
  justify-content: center;
  margin-top: 10px;
}

.carousel-dots {
  display: flex;
  gap: 5px;
}

.carousel-dot {
  width: 8px;
  height: 8px;
  border-radius: 50%;
  background-color: #ccc;
  cursor: pointer;
  transition: all 0.3s ease;
}

.carousel-dot.active {
  background-color: var(--primary-color);
  transform: scale(1.2);
}

/* 轮播动画 */
.slide-enter-active,
.slide-leave-active {
  transition: all 0.5s ease;
}

.slide-enter-from {
  opacity: 0;
  transform: translateX(30px);
}

.slide-leave-to {
  opacity: 0;
  transform: translateX(-30px);
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

.item-price-popup {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background-color: rgba(255, 255, 255, 0.9);
  border: 2px solid var(--primary-color);
  border-radius: 8px;
  padding: 8px 12px;
  display: flex;
  flex-direction: column;
  align-items: center;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.2);
  opacity: 0;
  transition: opacity 0.3s ease;
  pointer-events: none;
  min-width: 100px;
}

.item-image:hover .item-price-popup {
  opacity: 1;
}

.popup-original-price {
  font-size: 12px;
  color: var(--light-text);
  text-decoration: line-through;
  margin-bottom: 2px;
}

.popup-current-price {
  font-size: 18px;
  font-weight: bold;
  color: var(--primary-color);
  margin-bottom: 2px;
}

.popup-discount {
  font-size: 12px;
  background-color: var(--accent-color);
  color: white;
  padding: 2px 8px;
  border-radius: 12px;
  font-weight: bold;
}

.item-hot-badge {
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
  z-index: 2;
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
