<template>
    <div class="page_container" id="photos_container" v-if="dataloaded">
    <div v-for="photo in all_photos" class="col-sm-6" style="    padding-top: 20px;">
    <div class="image_container">
        <a :href="photo.image_url" :data-lightbox="photo.name" :data-title="photo.name">
            <img v-lazy="photo.image_url" :alt="photo.name"/>
        </a>
    </div>
        
    </div>
        
    </div>
</template>
<style>
    .image_container {
        overflow: hidden;
        margin: auto;
        height: 250px;
        position: relative;
    }
    .image_container img {
        width: 100%;
        height: 100%;
    }
</style>
<script>
    define(["Vue", 'vuex', "vue-meta", "lightbox", "vue-lazy-load"], function(Vue, Vuex, Meta, Lightbox, VueLazyload) {
        Vue.use(Meta);
        Vue.use(VueLazyload);
        Vue.use(Lightbox);

        return Vue.component("photos-component", {
            template: template, // the variable template will be injected
            data: function() {
                return {
                    all_photos: null,
                    dataloaded: false
                }
            },
            created() {
                this.$store.dispatch("getData", "repos").then(response => {
                    this.dataloaded = true;
                    this.all_photos = this.findRepoByName('photos').images;
                }, error => {
                    console.error("Could not retrieve data from server. Please check internet connection and try again.");
                });
            },

            computed: {
                ...Vuex.mapGetters([
                    'property',
                    'findRepoByName'
                ])
            },
        });
    });
</script>