<template>
    <div>
        <image-load/>

        <div class = "">
            <h1>Images</h1>
            <div class = "images">
                <div class = "images__image" v-for = "(image, index) in images" :key = "index">
                    <img :src="image.imageData"/>
                </div>
            </div>
        </div>


    </div>
</template>

<script>
    import ImageLoad from './ImageLoad.vue'
    export default {
        name: "images",
        data() {
            return{
                images: []
            }
        },
        methods: {
            getImages() {
                this.images = JSON.parse(localStorage.getItem('images'));
            }
        },
        mounted() {
            this.getImages();
        },
        components: {
            ImageLoad
        }
    }
</script>

<style scoped>
    .images {
        display: flex;
        align-items: flex-start;
        justify-content: space-around;
        flex-wrap: wrap;
    }
    .images__image {
        max-width: 200px;
        height: auto;
        overflow: hidden;
    }
    .images__image img {
        width: 100%;
    }
</style>