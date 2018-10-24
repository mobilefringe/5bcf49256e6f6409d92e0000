<template>
	<div v-if="dataloaded">
		<div class="page_header" v-if="pageBanner" v-lazy:background-image="pageBanner.image_url">
			<!--http://via.placeholder.com/1920x300-->
			<div class="site_container">
				<div class="header_content">
					<h1>{{$t("stores_page.shopping")}}</h1>
				</div>
			</div>
		</div>
		<div class="site_container page_content">
			<div class="row bold">
				<div class="col-sm-6 col-md-4">
					<div class="store_search" >
						<search-component :list="allStores" :placeholder="$t('stores_page.find_your_store')" suggestion-attribute="name" v-model="search_result" @select="onOptionSelect" class="text-left">
							<template slot="item" scope="option" class="manual">
								<article class="media">
									<p>
										<strong>{{ option.data.name }}</strong>
									</p>
								</article>
							</template>
						</search-component>
						<img src="//codecloud.cdn.speedyrails.net/sites/5a6a54eb6e6f647da51e0100/image/png/1517497861636/search_icon_2x.png" class="pull-right" id="store_search_img" alt="">
					</div>
				</div>
				<div class="col-sm-6 col-md-4">
					<div class="store_search" >
						<div class="category-select-container">
							<v-select v-model="selectedCat" :options="dropDownCats" :searchable="false" :on-change="filterByCategory" class="category-select" :placeholder="$t('stores_page.sort_by_cats')" id="selectByCat"></v-select>
						</div>
					</div>
				</div>
				<div class="col-md-4 col-sm-12 hidden_phone">
					<div class="store_search" >
						<router-link class="directory_link" to="/map">
							<div class="promotions_header_container directory_btn">{{$t("stores_page.view_map")}}</div>
						</router-link>
					</div>
				</div>
			</div>
			<div class="row">
				<div id="store_list_container">
					<div v-masonry transition-duration="0.3s" item-selector=".stores-grid-item" horizontal-order="true">
                            <transition-group name="custom-classes-transition" enter-active-class="animated fadeIn" leave-active-class="animated fadeOut" tag="div">
                                <div v-masonry-tile  v-for="(store, index) in filteredStores" :key="index" class="stores-grid-item">
                            	    <div class="store_logo_container">
                            	        <router-link :to="'/stores/'+ store.slug">
                                			<!--<img class="store_img" :src="store.image_url" alt="">-->
                                			<div v-if="!store.no_store_logo">
                                			    <img class="transparent_logo" src="//codecloud.cdn.speedyrails.net/sites/5b1550796e6f641cab010000/image/png/1536094421888/default_background.png">
                                			    <img  class="store_img" :src="store.store_front_url_abs" alt="">
                                			</div>
                                			
                                            <div v-else class="no_logo_container">
                                                <img class="transparent_logo" src="//codecloud.cdn.speedyrails.net/sites/5b1550796e6f641cab010000/image/png/1536094421888/default_background.png" alt="">
                                                <div class="no_logo_text">
                                                    <div class="store_text"><h4>{{ store.name }}</h4></div>
                                                </div>
                                            </div>
                                			<div class="store_tag" v-if="store.total_published_promos">
            									<div class="store_tag_text">Promotion</div>
            								</div>
            								<div class="store_tag" v-if="!store.total_published_promos && store.is_coming_soon_store">
            									<div class="store_tag_text">Coming Soon</div>
            								</div>
            								<div class="store_tag" v-if="!store.total_published_promos && !store.is_coming_soon_store && store.is_new_store">
            									<div class="store_tag_text">New Store</div>
            								</div>
            								<div class="store_details">
            								    <div class="store_text"><h4>{{ store.name }}</h4></div>    
            								</div>
                                		</router-link>
                            	    </div>
                                </div>
                            </transition-group>
                        </div>
				</div>
			</div>
		</div>
	</div>
</template>

<script>
    define(["Vue", "vuex", "vue-select", "jquery", "vue!search-component", "vue-lazy-load", 'json!site.json',"masonry","vue-masonry-plugin"], function(Vue, Vuex, VueSelect, $, SearchComponent,VueLazyload, Site, masonry, VueMasonryPlugin) {
        Vue.use(VueLazyload);
        Vue.use(VueMasonryPlugin.default);
        return Vue.component("stores-component", {
            template: template, // the variable template will be injected
            data: function() {
                return {
                    listMode: "alphabetical",
                    selectedCat: null,
                    selectedAlpha: "All",
                    alphabet: ["All", "A", "B", "C", "D", "E", "F", "G", "H", "I", "J", "K", "L", "M", "N", "O", "P", "Q", "R", "S", "T", "U", "V", "W", "X", "Y", "Z"],
                    filteredStores: null,
                    dataloaded: false,
                    mobile_store: false,
                    windowWidth: 0,
                    pageBanner : null,
                    search_result : null,
                }
            },
            // created() {
            //     // window.Raphael = Raphael; // our mapSvg plugin is stupid and outdated. need this hack to tie Raphael to window object (global variable)
            //     this.$store.dispatch("getData", "categories").then(response => {
            //         this.dataloaded = true;
            //         this.filteredStores = this.allStores;
            //     }, error => {
            //         console.error("Could not retrieve data from server. Please check internet connection and try again.");
            //     });
            // },
            created (){
                this.loadData().then(response => {
                    this.dataloaded = true;
                    this.filteredStores = this.allStores;
                    
                    // this.storeBanner = this.findRepoByName('Stores Banner').images[0];
                    var temp_repo = this.findRepoByName('Directory Banner');
                    if(temp_repo) {
                        this.pageBanner = temp_repo.images[0];
                    }
                    else {
                        this.pageBanner = {};
                        this.pageBanner.image_url = "";
                    }
                    
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
                    'processedCategories',
                    'storesByAlphaIndex',
                    'storesByCategoryName',
                    'findCategoryById',
                    'findCategoryByName',
                    'findRepoByName'

                ]),
                allStores() {
                    var stores = this.processedStores;
                   stores.map(store => {
                        if (_.includes(store.image_url, 'missing')) {
                           store.no_store_logo = true;
                        } else {
                          store.no_store_logo = false;
                        }
                    });
                    return this.processedStores;
                },
                allCatergories() {
                    return this.processedCategories;
                },
                dropDownCats() {
                    var cats = _.map(this.processedCategories, 'name');
                    cats.unshift('All');
                    return cats;
                },
                getPNGurl() {
                    return "https://www.mallmaverick.com" + this.property.map_url;
                },
                svgMapRef() {
                    return _.filter(this.$children, function(o) {
                        return (o.$el.className == "svg-map")
                    })[0];
                },
                filterStores() {
                    letter = this.selectedAlpha;
                    if (letter == "All") {
                        this.filteredStores = this.allStores;
                    } else {
                        var filtered = _.filter(this.allStores, function(o, i) {
                            return _.lowerCase(o.name)[0] == _.lowerCase(letter);
                        });
                        this.filteredStores = filtered;
                    }
                },
                filterByCategory() {
                    category_id = this.selectedCat;
                    if (category_id == "All" || category_id == null || category_id == undefined) {
                        category_id = "All";
                    } else {
                        category_id = this.findCategoryByName(category_id).id;
                    }

                    if (category_id == "All") {
                        this.filteredStores = this.allStores;
                    } else {

                        var find = this.findCategoryById;
                        var filtered = _.filter(this.allStores, function(o) {
                            return _.indexOf(o.categories, _.toNumber(category_id)) > -1;
                        });
                    
                        this.filteredStores = filtered;
                    }
                    var el = document.getElementById("selectByCat");
                    if(el) {
                        el.classList.remove("open");
                        console.log(el.classList);
                    }
                    
                },
                
            },
            beforeDestroy: function() {
                window.removeEventListener('resize', this.getWindowWidth);
            },
        });
    });
</script>