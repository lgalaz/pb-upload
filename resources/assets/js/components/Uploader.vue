<template>
    <div id="dropzone">
        <div id="content" class="flex flex-center flex-columns">
            Arrastra ó
            <div class="btn play-gray">
                Selecciona tus imágenes
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        mounted() {
            console.log('Component mounted.')
        }
    }
</script>

<style lang="scss" scoped>
    #dropzone {
        height: 227px;
        width: 406px;
        border: 1px dashed #CCC;
        padding: 20px;

        #content {
            background-color: #FAFAFA;
            height: 100%;;
        }
    }
</style>