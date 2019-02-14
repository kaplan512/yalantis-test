<template>
    <div>
        <image-load
            @getImages = "getImages"
        ></image-load>

        <div class = "">
            <h1>Images</h1>
            <div class = "images">
                <div
                        class = "images__image"
                        v-for = "(image, index) in images"
                        :key = "index"
                >
                    <img @click = "chooseImageToEdit(image, index)" :src="image.imageData"/>
                </div>
            </div>
        </div>


    </div>
</template>

<script>
    /* eslint-disable */
    import ImageLoad from './ImageLoad.vue'
    export default {
        name: "images",
        data() {
            return{
                images: []
            }
        },
        methods: {
            getImages(form) {
                this.images.push(form)
                localStorage.setItem('images', JSON.stringify(this.images))
            },
            chooseImageToEdit(image, index) {
                console.log(image, index)
            }
        },
        mounted() {
            if(localStorage.getItem('images')) {
                this.images = JSON.parse(localStorage.getItem('images'));
            } else {
                this.images = [];
            }
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
        cursor: pointer;
    }
    .images__image img {
        width: 100%;
    }
</style>