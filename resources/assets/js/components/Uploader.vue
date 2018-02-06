<template>
    <div id="dropzone">
        <div v-show="$refs.upload && $refs.upload.dropActive" class="drop-active">
            <h3>Suelta la imágen para cargar.</h3>
        </div>
        <div id="content" class="flex flex-center flex-columns" v-show="!edit">
            <div class="mb">
                    Arrastra ó
            </div>
 
            <vue-file-upload 
                extensions="gif,jpg,jpeg,png,webp"
                accept="image/png,image/gif,image/jpeg,image/webp"
                name="image"
                post-action="/upload/post"
                :drop="!edit"
                v-model="files"
                @input-filter="inputFilter"
                @input-file="inputFile"
                ref="upload">

                <div class="btn play-gray">
                    Selecciona tu imágen
                </div>

            </vue-file-upload>

        </div>

        <div class="upload-preview" v-show="files.length && edit">
            <div class="uploaded-image" v-if="files.length">
                <img ref="editImage" :src="files[0].url" />
            </div>
            <div class="options flex flex-center">
                <div class="option" @click.prevent="editSave">Agregar Descripción</div> |
                <div class="option" @click.prevent="$refs.upload.clear">Eliminar</div>
            </div>
        </div>
    </div>
</template>

<script>
    import VueFileUpload from 'vue-upload-component';

    export default {
        data() {
            return {
                files: [],
                edit: false,
            }
        },

        components: {
            VueFileUpload
        },

        methods: {
            editSave() {
                this.edit = false;
            },

            alert(message) {
                alert(message);
            },

            inputFile(newFile, oldFile, prevent) {
                if (newFile && !oldFile) {
                    this.$nextTick(function () {
                        this.edit = true;
                    })
                }
                if (!newFile && oldFile) {
                    this.edit = false;
                }
            },

            inputFilter(newFile, oldFile, prevent) {
                if (newFile && !oldFile) {
                    if (!/\.(gif|jpg|jpeg|png|webp)$/i.test(newFile.name)) {
                        this.alert('Your choice is not a picture');
                        return prevent();
                    }
                }

                if (newFile && (!oldFile || newFile.file !== oldFile.file)) {
                    newFile.url = '';
                    let URL = window.URL || window.webkitURL;
                    if (URL && URL.createObjectURL) {
                        newFile.url = URL.createObjectURL(newFile.file);
                    }
                }
            }
        }
    }
</script>

<style lang="scss" scoped>

    #dropzone, 
    .uploaded-image,
    .uploaded-image img{
        height: 227px;
        width: 406px;
    }

    #dropzone {
        border: 1px dashed #CCC;
        padding: 20px;

        #content {
            background-color: #FAFAFA;
            height: 100%;
        }

        .options {
            position: relative;
            bottom: 35px;
            height: 35px;
            background-color: white;
            opacity: .5;
            font-size: 14; 
            border-radius: unset;

            .option {
                cursor: pointer;
                margin: 0 10px;
                padding: 5px;
            }

            .option:hover {
                color: lighten(black, 20%);
            }
        }

        .drop-active {
            top: 0;
            bottom: 0;
            right: 0;
            left: 0;
            position: fixed;
            z-index: 9999;
            opacity: .6;
            text-align: center;
            background: #000;
        }

        .drop-active h3 {
            margin: -.5em 0 0;
            position: absolute;
            top: 50%;
            left: 0;
            right: 0;
            -webkit-transform: translateY(-50%);
            -ms-transform: translateY(-50%);
            transform: translateY(-50%);
            font-size: 40px;
            color: #fff;
            padding: 0;
        }
    }


</style>