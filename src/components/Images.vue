<template>
    <div>
        <div v-if = "error" class = "error">{{error}}</div>
        <image-load
            @getImages = "getImages"
            @deleteSelectedImage = "deleteSelectedImage"
            :selectedImage = "selectedImage"
            ref = "imageLoad"
        ></image-load>

        <div v-if = "images.length">
            <h1>Uploaded Images</h1>
            <div class = "images">
                <div
                        class = "images__image"
                        v-for = "(image, index) in images"
                        :key = "index"
                >
                    <img :src="image.imageData"/>
                    <div class = "image__buttons">
                        <button class = "btn btn-danger" @click = "deleteImage(index)">Delete</button>
                        <button class = "btn btn-success" @click = "chooseImageToEdit(image, index)">Edit</button>
                    </div>
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
                images: [],
                selectedImage: {
                },
                error: ''
            }
        },
        methods: {
            getImages(form) {
                if(this.selectedImage.hasOwnProperty('index')) {
                    this.images[this.selectedImage.index] = Object.assign({}, form);
                    this.selectedImage = {}
                    this.setToLocalStorage();
                } else {
                    this.images.push(form);
                    this.setToLocalStorage();
                }

            },
            setToLocalStorage() {
                localStorage.setItem('images', JSON.stringify(this.images))
            },
            chooseImageToEdit(image, index) {
                this.selectedImage.index = index;
                this.selectedImage.image = image;
                this.$refs.imageLoad.setImageToEdit();
            },
            deleteImage(index) {
                if(index === this.selectedImage.index) {
                    this.error = "You can't delete image which is editing now";
                    setTimeout(()=> this.error = '', 5000);
                } else {
                    this.images.splice(index, 1);
                    this.setToLocalStorage();
                }

            },
            deleteSelectedImage() {
                this.selectedImage = {}
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
        position: relative;
        margin: 5px;
    }
    .images__image img {
        width: 100%;
    }
    .image__buttons {
        position: absolute;
        align-items: center;
        justify-content: center;
        flex-direction: column;
        display: none;
        width: 100%;
        height: 100%;
        top:0;
    }
    .images__image:hover .image__buttons {
        display: flex;
    }
    .image__buttons .btn-success {
        margin-top: 10px;
    }
    .error {
        position: fixed;
        right: 15px;
        top: 15px;
        background: red;
        color: white;
        padding: 10px;
        z-index: 999;
    }
</style>