<template>
    <div class="page_container" id="thank_you_container">
        <div  v-if="pageBanner" class="page_header">
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
    define(["Vue"], function(Vue) {
        return Vue.component("thank-you-component", {
            template: template, // the variable template will be injected
            data: function() {
                return {
                    title: "title"
                }
            }
        });
    });
</script>
<!--https://thecentre.codecloudapp.com/thank_you-->