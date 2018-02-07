<template>
    <div id="dropzone">
        <div v-show="$refs.upload && $refs.upload.dropActive" class="drop-active">
            <h3>Suelta la imágen para cargar.</h3>
        </div>
        <div class="content flex flex-center flex-columns" v-show="! files.length && ! edit">
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
  
        <div class="content flex flex-center flex-columns" v-if="files.length && editForm">
            <form class="medium-width">
                <div class="form-group">
                    <label for="description">Agregar Descripción</label>
                    <textarea class="form-control" id="description" aria-describedby="descriptionHelp" placeholder="bla bla bla..."></textarea>
                    <small id="descriptionHelp" class="form-text text-muted">Acerca de la imágen.</small>
                </div>
            </form>
            <div class="flex flex-center">
                <div class="btn play-green" @click="editSave">
                    Aceptar
                </div>
                <div class="btn play-gray" @click="clearDescription">
                    Borrar
                </div>
            </div>       
        </div>     

        <div class="upload-preview" v-show="files.length && edit">
            <div class="uploaded-image" v-if="files.length">
                <img ref="editImage" :src="files[0].url" />
            </div>
            <template v-if="! description">
                <div class="options flex flex-center">
                    <div class="option" @click.prevent="showEditForm">Agregar Descripción</div> |
                    <div class="option" @click.prevent="$refs.upload.clear">Eliminar</div>
                </div>
            </template>
            <template v-else>
                <div class="options flex flex-center flex-columns backdrop">
                    <div class="mb medium-width text-center" v-text="description"></div>
                    <div class="flex">
                        <div class="btn play-green" @click.prevent="showEditForm">Editar</div>
                        <div class="btn alert-red" @click.prevent="$refs.upload.clear">Eliminar</div>
                    </div>
                </div>
            </template>
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
                editForm: false,
                description: ''
            }
        },

        components: {
            VueFileUpload
        },

        methods: {
            showEditForm() {
                this.edit = false;
                this.editForm = true;
            },

            editSave() {
                this.description = document.querySelector('#description').value;
                this.edit = true;
                this.editForm = false;
            },

            clearDescription() {
                document.querySelector('#description').value ="";  
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
    #dropzone {
        border: 1px dashed #CCC;
        padding: 20px;
        height: 227px;
        width: 406px;

        .uploaded-image,
        .uploaded-image img{
            width: 364px;
            height: 187px;
        }

        .content {
            background-color: #FAFAFA;
            height: 100%;
        }

        .btn {
            margin: 0 10px;
        }
        
        .options {
            position: relative;
            bottom: 35px;
            height: 35px;
            background-color: white;
            opacity: .7;
            font-size: 14; 
            border-radius: unset;

            &.backdrop {
                height: 187px;
                bottom: 187px;
            }


            .option {
                cursor: pointer;
                margin: 0 10px;
                padding: 5px;

                &:hover {
                    color: lighten(black, 50%);
                }
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