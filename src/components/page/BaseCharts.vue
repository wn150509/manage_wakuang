<template>
<div>
    <!--<p>{{location.cnty}}  {{location.admin_area}}  {{location.location}}</p>-->
</div>
</template>

<script>
    export default {
        data () {
            return {
                location:''
            }
        },
        created(){
            // var that=this;
            // this.$axios
            //     .get("https://free-api.heweather.com/s6/weather?location=auto_ip&key=a46fd5c4f1b54fda9ee71ba6711f09cd")
            //     .then(function (res) {
            //        that.location=res.data.HeWeather6[0].basic;
            //         console.log(res.data.HeWeather6[0].basic)
            //     });
        }
    }
</script>

<style>

</style>
