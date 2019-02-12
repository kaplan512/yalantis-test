<template>
    <div>
        <h1>{{title}}</h1>
        <form @submit.prevent = "submitForm">
            <input
                    id="exampleInput2"
                    type="text"
                    v-model="form.tooltip"
                    required
                    class="form-control"
                    placeholder="Enter tooltip"
            />
            <input
                    @change="previewImage"
                    accept="image/*"
                    ref = "file"
                    type="file"
                    id="file"
                    class="form-control-file"
                    required
            />
            <button type="submit" class = "btn btn-primary">Submit</button>

        </form>

        <h3 v-if = "form.imageData && form.tooltip">Click on the image to set tooltip position</h3>
        <div class="image-preview" v-if="form.imageData.length > 0">
            <img
                    class="preview"
                    :src="form.imageData"
                    @click = "setTooltipPosition"
                    ref = "image"
            >
            <div class = "image-tooltip" v-if = "form.tooltip">
                {{form.tooltip}}
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        name: "image-load",
        data() {
            return {
                form: {
                    tooltip: '',
                    imageData: '',
                    tooltipX: 0,
                    tooltipY: 0
                },
                images: [],
                title: 'Upload image'
            }
        },
        methods: {
            submitForm() {
                if(this.form.tooltipX && this.form.tooltipY) {
                    this.images.push(this.form);
                    localStorage.setItem('images', JSON.stringify(this.images));
                    this.form.tooltip = '';
                    this.form.imageData = '';
                    this.form.tooltipX = 0;
                    this.form.tooltipY = 0;
                } else {
                    console.log('set position')
                }
            },
            previewImage(event) {
                let input = event.target;
                if (input.files && input.files[0]) {
                    let reader = new FileReader();
                    reader.onload = (e) => {
                        this.form.imageData = e.target.result;
                    }
                    reader.readAsDataURL(input.files[0]);
                }
            },
            setTooltipPosition(event) {
                let imageWidth = this.$refs.image.clientWidth;
                console.log(imageWidth)
                this.form.tooltipX = event.offsetX;
                this.form.tooltipY = event.offsetY;
                console.log(this.form)
            }
        },
        mounted() {
            this.images = JSON.parse(localStorage.getItem('images'));

        }
    }
</script>

<style scoped>
    .image-preview {
        width: fit-content;
        margin: 15px auto;
        position: relative;
    }

    .image-tooltip {
        position: absolute;
        background: rgba(0, 0, 0, 0.5);
        color: white;
        padding: 10px;
        border-radius: 10px;
        display: none;
    }
    .image-preview:hover .image-tooltip {
        display: block;
        cursor: pointer;
    }
</style>