<template>
	<div v-if="dataloaded">
		<div class="page_header" v-if="pageBanner" v-lazy:background-image="pageBanner.image_url">
			<!--http://via.placeholder.com/1920x300-->
			<div class="site_container">
				<div class="header_content">
					<h1>{{$t("stores_page.new_stores")}}</h1>
				</div>
			</div>
		</div>
		<div class="site_container page_content">
			<div class="row">
				<div id="store_list_container">
					<store-masonry :filteredStores="filteredStores"></store-masonry>
				</div>
			</div>
		</div>
	</div>
</template>

<script>
    define(["Vue", "vuex", "vue-select", "jquery", "vue!search-component", "vue-lazy-load", 'json!site.json', "vue!storelist_masonry.vue"], function(Vue, Vuex, VueSelect, $, SearchComponent,VueLazyload, Site, StoreMasonry) {
        Vue.use(VueLazyload);
        return Vue.component("whats-new-component", {
            template: template, // the variable template will be injected
            props: ['new_store'],
            data: function() {
                return {
                    listMode: "alphabetical",
                    selectedCat: null,
                    filteredStores: null,
                    dataloaded: false,
                    mobile_store: false,
                    windowWidth: 0,
                    pageBanner : null,
                    search_result : null,
                }
            },
            created (){
                this.loadData().then(response => {
                    this.dataloaded = true;
                    // this.filteredStores = this.allStores;
                    
                    // this.storeBanner = this.findRepoByName('Stores Banner').images[0];
                    var temp_repo = this.findRepoByName('Directory Banner');
                    if(temp_repo) {
                        this.pageBanner = temp_repo.images[0];
                    }
                    else {
                        this.pageBanner = {};
                        this.pageBanner.image_url = "";
                    }
                    
                    
                    // if(this.$route.params.new_store == "new_store"){
                    //   this.filteredStores = this.new_coming_soon_stores;
                    //   console.log("this.filteredStores", this.filteredStores)
                      
                    // } else {
                    this.filteredStores = this.allStores;
                    // }
                });
            },
            watch: {
                windowWidth: function() {
                    if (this.windowWidth <= 768) {
                        this.mobile_store = true;
                    } else {
                        this.mobile_store = false;
                    }
                },
                // filteredStores () {
                //     console.log("this.filteredStores", this.filteredStores)
                // },
                // selectedAlpha() {
                //      console.log("this.selectedAlpha", this.selectedAlpha)
                // }
            },
            mounted() {
                // this.filteredStores = this.allStores;
                this.$nextTick(function() {
                    window.addEventListener('resize', this.getWindowWidth);
                    //Init
                    this.getWindowWidth();
                });
            },
            methods: {
                loadData: async function() {
                    try {
                        // avoid making LOAD_META_DATA call for now as it will cause the entire Promise.all to fail since no meta data is set up.
                        let results = await Promise.all([this.$store.dispatch("getData", "categories"), this.$store.dispatch("getData", "repos")]);
                    } catch (e) {
                        console.log("Error loading data: " + e.message);
                    }
                },
                changeMode(mode) {
                    this.listMode = mode;
                },
                updateSVGMap(map) {
                    this.map = map;
                },
                addLandmark(store) {
                    this.svgMapRef.addMarker(store);
                },
                getWindowWidth(event) {
                    this.windowWidth = window.innerWidth;
                },
                onOptionSelect(option) {
                    this.search_result = "";
                    this.$router.push("/stores/"+option.slug);
                },
            },
            computed: {
                ...Vuex.mapGetters([
                    'property',
                    'timezone',
                    'processedStores',
                    '
                    'findRepoByName'

                ]),
                allStores() {
                    var stores  = _.filter(this.processedStores, function(o, i) {
                        return o.is_new_store || o.is_coming_soon_store;
                    });
                    stores.map(store => {
                        if (_.includes(store.image_url, 'missing')) {
                           store.no_store_logo = true;
                        } else {
                          store.no_store_logo = false;
                        }
                    });
                    return stores;
                }
            }
        });
    });
</script>