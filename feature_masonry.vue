<template>
    <div>
        <a href="https://www.facebook.com/TheCentreSK" target="_blank"><i class="fa fa-facebook-official" aria-hidden="true"></i></a>
        <a href="https://www.instagram.com/thecentresk" target="_blank"><i class="fa fa-instagram" aria-hidden="true"></i></a>
        <a href="https://www.twitter.com/TheCentreSK" target="_blank"><i class="fa fa-twitter" aria-hidden="true"></i></a>
         
    </div>
    
</template>
<style>
    .lb-data .lb-number, .lb-nav {
        display:none!important;
    }
</style>
<script>
    define(["Vue", "vuex",  'masonry' , 'vue-masonry-plugin'], function(Vue, Vuex, Lightbox) {
        Vue.use(Lightbox);
        return Vue.component("social-links", {
            template: template, // the variable template will be injected,
        });
    });
</script>