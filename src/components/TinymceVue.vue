<template>
  <div>
      <textarea :id="id" v-model="content"></textarea>
  </div>
</template>

<script>
   // Import TinyMCE
    import tinymce from 'godream-tinymce/tinymce';

    // A theme is also required
    import 'godream-tinymce/themes/modern/theme';

    // Any plugins you want to use has to be imported
    import 'godream-tinymce/plugins/advlist';
    import 'godream-tinymce/plugins/wordcount';
    import 'godream-tinymce/plugins/autolink';
    import 'godream-tinymce/plugins/autosave';
    import 'godream-tinymce/plugins/charmap';
    import 'godream-tinymce/plugins/codesample';
    import 'godream-tinymce/plugins/contextmenu';
    import 'godream-tinymce/plugins/emoticons';
    import 'godream-tinymce/plugins/fullscreen';
    import 'godream-tinymce/plugins/hr';
    import 'godream-tinymce/plugins/imagetools';
    import 'godream-tinymce/plugins/insertdatetime';
    import 'godream-tinymce/plugins/link';
    import 'godream-tinymce/plugins/media';
    import 'godream-tinymce/plugins/noneditable';
    import 'godream-tinymce/plugins/paste';
    import 'godream-tinymce/plugins/print';
    import 'godream-tinymce/plugins/searchreplace';
    import 'godream-tinymce/plugins/tabfocus';
    import 'godream-tinymce/plugins/template';
    import 'godream-tinymce/plugins/textpattern';
    import 'godream-tinymce/plugins/visualblocks';
    import 'godream-tinymce/plugins/anchor';
    import 'godream-tinymce/plugins/autoresize';
    import 'godream-tinymce/plugins/bbcode';
    import 'godream-tinymce/plugins/code';
    import 'godream-tinymce/plugins/colorpicker';
    import 'godream-tinymce/plugins/directionality';
    import 'godream-tinymce/plugins/fullpage';
    import 'godream-tinymce/plugins/help';
    import 'godream-tinymce/plugins/image';
    import 'godream-tinymce/plugins/importcss';
    import 'godream-tinymce/plugins/legacyoutput';
    import 'godream-tinymce/plugins/lists';
    import 'godream-tinymce/plugins/nonbreaking';
    import 'godream-tinymce/plugins/pagebreak';
    import 'godream-tinymce/plugins/preview';
    import 'godream-tinymce/plugins/save';
    import 'godream-tinymce/plugins/spellchecker';
    import 'godream-tinymce/plugins/table';
    import 'godream-tinymce/plugins/textcolor';
    import 'godream-tinymce/plugins/toc';
    import 'godream-tinymce/plugins/visualchars';
    
    import 'godream-tinymce/skins/lightgray/skin.min.css'
   
    export default {
        name: 'tinymce',
        props: { 
                id : {
                    type : String,
                    required : true
                },
                htmlClass : { default : '', type : String},
                value : { default : '' },
                plugins : { default : function(){ 
                                    return [
                                        'advlist autolink lists link charmap preview hr anchor',
                                        'searchreplace wordcount visualblocks visualchars code fullscreen',
                                        'insertdatetime media nonbreaking save table contextmenu directionality',
                                        'template paste textcolor colorpicker textpattern toc emoticons hr codesample'
                                    ];
                                } , type: Array
                            },
                toolbar1: { default :'sizeselect | fontsizeselect | formatselect | bold italic  strikethrough  forecolor backcolor | link | alignleft aligncenter alignright alignjustify  | numlist bullist outdent indent  | removeformat', type: String},
                toolbar2: { default : '', type: String },
                fontsize_formats: "8pt 10pt 12pt 14pt 18pt 24pt 36pt",
                
                other_options: { default : function() { return {}; }, type: Object},
                readonly: { default: false, type: Boolean }
        },
        data(){
            return {
                content : '',
                editor : null,
                cTinyMce : null,
                checkerTimeout: null,
                isTyping : false
            }; 
        },
        mounted(){
            this.content = this.value;
            this.init();  
        },
        beforeDestroy () {
            this.editor.destroy();
        },
        watch: {
            value : function (newValue){
                if(!this.isTyping){
                    if(this.editor !== null)
                        this.editor.setContent(newValue);
                    else
                        this.content = newValue;
                }
            },
            readonly(value){
                if(value){
                    this.editor.setMode('readonly');
                } else {
                    this.editor.setMode('design');
                }
            }
        },
        methods: {
            init(){
                let options = {
                    selector: '#' + this.id,
                    skin: false,
                    toolbar1: this.toolbar1,
                    toolbar2: this.toolbar2,
                    plugins: this.plugins,
                    init_instance_callback : this.initEditor,
                    
                };
                tinymce.init(this.concatAssciativeArrays(options, this.other_options));
            },
            initEditor(editor) {
                this.editor = editor;
                editor.on('KeyUp', (e) => {
                    this.submitNewContent();
                });
                editor.on('Change', (e) => {
                    if(this.editor.getContent() !== this.value){
                        this.submitNewContent();
                    }
                    this.$emit('editorChange', e);
                });
                editor.on('init', (e) => {
                    editor.setContent(this.content);
                    this.$emit('input', this.content);
                });
                if(this.readonly){
                    this.editor.setMode('readonly');
                } else {
                    this.editor.setMode('design');
                }

                this.$emit('editorInit', editor);
            },
            concatAssciativeArrays(array1, array2){
                if(array2.length === 0) return array1;
                if(array1.length === 0) return array2;
                let dest = [];
                for ( let key in array1) dest[key] = array1[key];
                for ( let key in array2) dest[key] = array2[key];
                return dest;
            },
            submitNewContent(){
                this.isTyping = true;
                if(this.checkerTimeout !== null)
                    clearTimeout(this.checkerTimeout);
                    this.checkerTimeout = setTimeout(()=>{
                        this.isTyping = false;
                    }, 300);

                this.$emit('input', this.editor.getContent());
            }
        }
    }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
