<template>
	<div id="find_us_container">
	    <div  v-if="pageBanner" class="page_header" v-bind:style="{ backgroundImage: 'url(' + pageBanner.image_url + ')', marginBottom:'0px'}">
			<div class="site_container">
				<div class="header_content">
					<h1>Find Us</h1>
				</div>
			</div>
		</div>  
		<!-- for some reason if you do not put an outer container div this component template will not render -->
		<div>
			<div class="row text-left">
				<div style="height: 300px;margin-bottom:-30px;">
	                <google-map :property="property"></google-map>
	            </div>
                <div class="text-left site_container padding_tb_30 inside_page_content" v-if="currentPage" v-html="currentPage.body"></div>
					
			</div>
			<div class="padding_top_40"></div>
		</div>
	</div>
</template>

<script>
    define(["Vue", "vuex", "moment", "moment-timezone", "vue-moment", "vue-meta", 'vee-validate', "vue!google-map"], function(Vue, Vuex, moment, tz, VueMoment, Meta, VeeValidate, GoogleMapAPI) {
        Vue.use(Meta);
        Vue.use(VeeValidate);
        return Vue.component("find-us-component", {
            template: template, // the variable template will be injected,
            data: function() {
                return {
                    success_subscribe: false,
                    currentPage: null,
                    pageBanner: null
                }
            },
            created(){
                this.loadData().then(response => {
                    this.currentPage = response[0].data;
                    var temp_repo = this.findRepoByName('Contact Us Banner');
                    if(temp_repo) {
                        this.pageBanner = temp_repo.images[0];
                    }
                    else {
                        this.pageBanner = {};
                        this.pageBanner.image_url = ""l
                    }
                    // this.pageBanner = this.findRepoByName('Contact Us Banner').images[0];
                   console.log(this.pageBanner); 
                });
            },
            computed: {
                ...Vuex.mapGetters([
                    'property',
                    'timezone',
                    'findRepoByName'
                ]),
                full_address() {
                    var address = this.property.address1 +','+this.property.city +','+ this.property.country +','+this.property.province_state +','+this.property.province_state;
                    return address.replace(/ /g,"+");
            
                }
            },
            methods: {
                loadData: async function() {
                    try {
                        // avoid making LOAD_META_DATA call for now as it will cause the entire Promise.all to fail since no meta data is set up.
                        let results = await Promise.all([this.$store.dispatch('LOAD_PAGE_DATA', {url: this.property.mm_host + "	/pages/thecentre-find-us.json"}),this.$store.dispatch("getData", "repos")]);
                        return results;
                    } catch (e) {
                        console.log("Error loading data: " + e.message);
                    }
                },
            }
        });
    });
</script>