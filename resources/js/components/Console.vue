<template>
    <div class="b-console" v-show="isFocus">
        <div class="container">
            <div class="row">
                <div class="col-md-6">
                    <div class="b-console-input">
                        <span>/</span>
                        <span v-for="(char, index) in string" v-html="char">
                        </span>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<style scoped>
    .b-console {
        position: fixed;
        top:0;
        left:0;right:0;
        background-color: #1d2124;
        color: #fff;
        padding: 15px;
        z-index: 999;
    }
    .b-console-input {}
    .b-console-input span {
        display: inline-block;
        margin-right: 10px;
    }
</style>

<script>
    export default {
        data() {
            return {
                isShiftDown:false,
                isFocus:false,
                command: null,
                string: [
                    ""
                ],
                cursorPosition: 0,
                missKeyCodes: [
                    8,27,17,18,32,13
                ],
                commands: {
                    'addpost': {

                    },
                    'viewpost': {

                    }
                }
            }
        },
        methods: {
            checkAvailableCode(code) {
                return (this.missKeyCodes.indexOf(code) === -1);
            },
            addChar(char) {
                let word = this.string[(this.string.length-1)] + char;
                this.$set(this.string, (this.string.length-1), word);
            },
            executeCommand() {
                let command = this.string[0];
                if(this.commands[command] === undefined) {
                    alert("Error: command <"+command+"> not found");
                    return;
                }

            },
            backspace() {
                let curWordIndex = this.string.length-1;
                if(this.string[curWordIndex].length === 0) {
                    if(this.string.length > 1) {
                        this.string.splice(curWordIndex,1);
                        this.backspace();
                        return;
                    }
                }
                let word = this.string[curWordIndex].slice(0,-1);
                this.$set(this.string, curWordIndex, word);
            }
        },
        mounted() {
            $('html').keydown((event)=>{
                if(event.which === 16) {
                    this.isShiftDown = true;
                }
                if(this.isFocus && this.checkAvailableCode(event.which)) {
                    this.addChar(event.key);
                } else if(this.isFocus) {
                    if(event.which === 8) {
                        this.backspace();
                    } else if(event.which === 32) {
                        this.string.push("");
                    } else if(event.which === 13) {
                        //EXECUTE
                        this.executeCommand();
                    }
                }
                if(event.key === '~' && !this.isFocus) {
                    this.isFocus = true;
                }
                console.log(event.which, event.key);
            });
            $('html').keyup((event)=>{
                if(event.which === 16) {
                    this.isShiftDown = false;
                }
                if(event.which === 27) {
                    this.isFocus = false;
                }
                console.log(event.which);
            });
            console.log('Console mounted.')
        }
    }
</script>
