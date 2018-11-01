<template>
    <div class="page_container" id="thank_you_container">
        <div  v-if="pageBanner" class="page_header" v-bind:style="{ backgroundImage: 'url(' + pageBanner.image_url + ')' }">
			<div class="site_container">
				<div class="header_content">
					<h1>{{$t("newsletter_page.newsletter")}}</h1>
				</div>
			</div>
		</div>  
        <div class="row">
            Your subscription has been confirmed. You've been added to our list and will hear from us soon.
        </div>
    </div>
</template>
<style>
    #thank_you_container div{
        padding: 20px;
    }
</style>
<script>
    define(["Vue", "vuex"], function(Vue, Vuex) {
        return Vue.component("thank-you-component", {
            template: template, // the variable template will be injected
            data: function() {
                return {
                    title: "title",
                    pageBanner: null
                }
            },
            created () {
                this.loadData().then(response => {
                    var temp_repo = this.findRepoByName('Newsletter Banner');
                    if(temp_repo) {
                        this.pageBanner = temp_repo.images[0];
                    }
                    else {
                        this.pageBanner = {};
                        this.pageBanner.image_url="";
                    }
                });    
            },
            mounted () {
                this.form_data.email = this.$route.query.email;
                $("#newsletter_email").val(this.form_data.email);
            },
            watch : {
                $route () {
                    this.form_data.email = this.$route.query.email;
                    $("#newsletter_email").val(this.form_data.email);
                }
            },
            computed: {
                ...Vuex.mapGetters([
                    'property',
                    'timezone',
                    'findRepoByName'
                ])
            }
        });
    });
</script>
<!--https://thecentre.codecloudapp.com/thank_you-->