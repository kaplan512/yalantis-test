<template>
    <div>
        <h1>
            <template  v-if="selectedImage.hasOwnProperty('index')">Edit Image</template>
            <template  v-else>Upload Image</template>
        </h1>
        <form @submit.prevent = "submitForm">
            <label class = "label" for = "tooltip" v-show = "!form.tooltip">Set tooltip</label>
            <input
                    id="tooltip"
                    type="text"
                    v-model="form.tooltip"
                    class="form-control"
                    placeholder="Enter tooltip"
            />
            <label class = "label" for = "file" v-show = "!form.imageData">Choose image</label>
            <input
                    @change="previewImage"
                    accept="image/*"
                    ref = "file"
                    type="file"
                    id="file"
                    class="form-control-file"
            />

            <div class = "label" v-show = "form.imageData.length > 0">Click on the image to set tooltip position</div>
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
            <div v-if = "form.imgName">{{form.imgName}}</div>

            <button type="submit" class = "btn btn-primary" :disabled="!isFormFilled">Submit</button>
            <button @click = "cancelForm" type="reset" v-if = "isFormNotEmpty" class = "btn btn-danger cancel">Cancel</button>

        </form>


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
                    imgName: '',
                    tooltipX: 0,
                    tooltipY: 0
                },
            }
        },
        props: {
            selectedImage: {
                type: Object,
                default() {
                    return {};
                }
            }
        },
        methods: {
            submitForm() {
                this.$emit('getImages', Object.assign({}, this.form));
                this.cancelForm();

            },
            setImageToEdit() {
                this.form = Object.assign({}, this.selectedImage.image);
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
            },
            cancelForm() {
                this.form.tooltip = '';
                this.form.imageData = '';
                this.form.tooltipX = 0;
                this.form.tooltipY = 0;
                this.form.imgName = '';
                this.$refs.file.value = '';
                this.$emit('deleteSelectedImage');
            }
        },
        computed: {
            tooltipPosition() {
                return {
                    left: this.form.tooltipX + '%',
                    top: this.form.tooltipY + '%',
                }
            },
            isFormNotEmpty() {
                for(let i in Object.keys(this.form)) {
                    if(this.form[Object.keys(this.form)[i]]) {
                        return true;
                    }
                }
                return false;
            },
            isFormFilled() {
                for(let i in Object.keys(this.form)) {
                    if(this.form[Object.keys(this.form)[i]]) {
                        continue;
                    } else {
                        return false;
                    }
                }
                return true;
            },

        },

    }
</script>

<style scoped>
    form {
        max-width: 80%;
        margin: 0 auto;
    }
    .image-preview {
        width: fit-content;
        margin: 15px auto;
        position: relative;
        max-width: 100%;
    }
    .image-preview img {
        width: 100%;
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
    .image-preview img:hover {
        cursor: pointer;
    }
    .image-preview:hover .image-tooltip {
        display: block;
    }
    .label {
        font-size: 20px;
        color: blue;
        margin-top: 15px;
    }
    input[type=file] {
        cursor: pointer;
        width: 180px;
        height: 34px;
        overflow: hidden;
        margin: 15px auto;
    }
    input[type=file]:before {
        width: 100%;
        height: 32px;
        font-size: 16px;
        line-height: 32px;
        content: 'Select your file';
        display: inline-block;
        background: white;
        border: 1px solid #000;
        padding: 0 10px;
        text-align: center;
    }

    input[type=file]::-webkit-file-upload-button {
        visibility: hidden;
    }
    .cancel {
        margin-left: 10px;
    }
</style>