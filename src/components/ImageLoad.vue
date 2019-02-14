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
            <div class = "image-tooltip" v-if = "form.tooltip" :style = "tooltipPosition">
                {{form.tooltip}}
            </div>
        </div>
    </div>
</template>

<script>
    /* eslint-disable */
    export default {
        name: "image-load",
        data() {
            return {
                form: {
                    tooltip: '',
                    imageData: '',
                    imgName: '',
                    tooltipX: 0,
                    tooltipY: 0
                },

                title: 'Upload image'
            }
        },
        methods: {
            submitForm() {
                if(this.form.tooltipX && this.form.tooltipY) {
                    this.$emit('getImages', Object.assign({}, this.form));
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
                        this.form.imgName = input.files[0].name;
                        this.form.imageData = e.target.result;
                    }
                    reader.readAsDataURL(input.files[0]);
                }
            },
            setTooltipPosition(event) {
                let imageWidth = this.$refs.image.clientWidth;
                let imageHeight = this.$refs.image.clientHeight;
                this.form.tooltipX = event.offsetX/(imageWidth/100);
                this.form.tooltipY = event.offsetY/(imageHeight/100);
            }
        },
        computed: {
            tooltipPosition() {
                return {
                    left: this.form.tooltipX + '%',
                    top: this.form.tooltipY + '%',
                }
            }
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
        transform: translate(-50%, -50%);
    }
    /*.image-tooltip::after {*/
        /*content: '';*/
        /*position: absolute;*/
        /*left: 50%;*/
        /*transform: translateX(-50%);*/
        /*top: -30px;*/
        /*border: 10px solid transparent;*/
        /*border-bottom: 20px solid rgba(0, 0, 0, 0.5);*/
    /*}*/
    .image-preview img:hover {
        cursor: pointer;
    }
    .image-preview:hover .image-tooltip {
        display: block;
    }
</style>