<template>
  <header class="top-navbar">
    <div class="spacer"></div>
   <div class="title">
    <h1></h1>
   </div>
    <div class="admin-wrapper">
      
      <button class="admin-btn" @click="toggleMenu">
        Admin ▼
      </button>

      <div v-if="showMenu" class="admin-menu">
        <!-- Empty for now -->
      </div>
    </div>
  </header>
</template>


<script>
export default {
  name: "TopNavbar",
  data() {
    return {
      showMenu: false,
    };
  },
  methods: {
    toggleMenu() {
      this.showMenu = !this.showMenu;
    },
  },
};
</script>

<style scoped>
.top-navbar {
  height: 56px;
  background: #ffffff;
  display: flex;
  align-items: center;

  padding: 0;               
  box-shadow: none;          

  position: static;
  top: 0;
  z-index: 100;
}

.spacer {
  flex: 1;
}

.title {
  color: #000;
  font-size: 8px;
  margin-right: 280px; 
}

.admin-wrapper {
  position: relative;
  margin-right: 24px;        
}

.admin-btn {
  background: #f4f6f8;
  border: 1px solid #000;
  padding: 8px 14px;
  border-radius: 20px;
  cursor: pointer;
  font-size: 14px;
  color: #000;
}

.admin-menu {
  position: absolute;
  top: 44px;
  right: 0;
  width: 160px;
  height: 100px;
  background: #fff;
  border-radius: 8px;
  box-shadow: 0 4px 12px rgba(0,0,0,.15);
}

</style>
