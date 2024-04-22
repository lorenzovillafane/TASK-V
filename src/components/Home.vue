<template>
    <div>
      <component :is="componentToShow" />
    </div>
  </template>
  
  <script>
  import Desktop from '../components/Desktop.vue';
  import Mobile from '../components/Mobile.vue';
  
  export default {
    data() {
      return {
        componentToShow: null
      };
    },
    mounted() {
      this.checkScreenSize();
      window.addEventListener('resize', this.checkScreenSize);
    },
    methods: {
      checkScreenSize() {
        if (window.innerWidth >= 768) {
          this.componentToShow = Desktop;
        } else {
          this.componentToShow = Mobile;
        }
      }
    },
    beforeDestroy() {
      window.removeEventListener('resize', this.checkScreenSize);
    }
  };
  </script>
  