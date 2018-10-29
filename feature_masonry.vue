<template>
    <div v-masonry transition-duration="0.3s" item-selector=".grid-item" >
        <div v-masonry-tile class="item feature_item_container" >
            <div v-for="feature in feature_items" :class="'grid-item ' + feature.masonry_class ">
            	<div  :class="{ 'gallery-image' : feature.no_hover_class}"> 
            	<!--class="ih-item circle effect19"-->
            		<router-link v-if="feature.url" :to="feature.url">
            			<img :src="feature.image_url" alt="name">
            			<!--<div class="info">-->
            				<div class="figcaption">
            					<h3 v-if="locale=='en-ca'"> {{feature.name}} </h3>
            					<h3 v-else> {{feature.name_2}} </h3>
            				</div>
            			<!--</div>-->
            		</router-link>
            		<div v-else>
            			<img :src="feature.image_url" alt="name">
            			<!--<div class="info">-->
            				<div class="figcaption">
            					<h3 v-if="locale=='en-ca'"> {{feature.name}} </h3>
            					<h3 v-else> {{feature.name_2}} </h3>
            				</div>
            			<!--</div>-->
            		</div>
            	</div>
            </div>
        </div>
    </div>
    
</template>
<style>
    .lb-data .lb-number, .lb-nav {
        display:none!important;
    }
</style>
<script>
    define(["Vue", "vuex",  'masonry' , 'vue-masonry-plugin'], function(Vue, Vuex, masonry, VueMasonryPlugin) {
        Vue.use(VueMasonryPlugin.default);
        return Vue.component("feature-masonry", {
            template: template, // the variable template will be injected,
            props:['feature_items']
        });
    });
</script>