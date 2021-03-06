<template>
    <section class="select" :class="{'open':open, 'seventy':seventy, 'disabled':disabled}">
        <figure class="arrow">
            <i class="fa fa-chevron-down"></i>
        </figure>

        <figure class="selected-option" v-on:click="toggle">
            {{parse(selectedOption)}}
        </figure>

        <section class="options">
            <figure class="option" v-for="item in options" v-on:click="select(item)">
                {{parse(item)}}
            </figure>
        </section>
    </section>
</template>

<script>
    export default {
        data(){ return {
            selectedOption:this.selected || this.placeholder || this.options[0],
            open:false,
        }},
        methods: {
            toggle(){
                if(this.disabled) return false;
                this.open = !this.open
            },
            parse(item){
                if(typeof item === 'string') return item;
                if(this.parser) return this.parser(item);

                let props = this.prop.split(".");
                const lastKey = props.pop();
                return props.reduce((obj,key)=> obj[key], item)[lastKey];
            },
            select(item){
                this.selectedOption = item;
                this.open = false;
                this.$emit('changed', this.selectedOption)
            }
        },
        props:['placeholder', 'options', 'selected', 'prop', 'parser', 'seventy', 'disabled'],
        watch:{
            input(){ this.emit(); },
            text(){ this.input = this.text; },
            disabled(isDisabled){ if(isDisabled) this.open = false; },
            selected(){ this.selectedOption = this.selected; }
        }
    }
</script>

<style lang="scss">
    .select {
        cursor: pointer;
        height:50px;
        position: relative;
        width:100%;
        font-family:'Open Sans',sans-serif;
        font-size:14px;
        border:1px solid #eaeaea;
        border-radius:4px;
        background:#fff;
        transition:background 0.2s ease;

        &.disabled {
            background: #f5f5f5;
        }

        &.seventy {
            width:calc(70% - 5px);
            display:inline-block;
            font-size:14px;
        }

        &:not(:first-child){
            margin-top:10px;
        }

        .arrow {
            position:absolute;
            right:0;
            height:50px;
            line-height:50px;
            padding:0 15px;
            color:#888;
            opacity:0.3;
            transition:opacity 0.2s ease;
            pointer-events: none;
        }

        .selected-option {
            height:50px;
            line-height:50px;
            width:100%;
            padding:0 35px 0 15px;
            overflow: hidden;
        }

        .options {
            position:absolute;
            top:48px;
            background:#fff;
            border:1px solid #eaeaea;
            border-bottom-right-radius:4px;
            border-bottom-left-radius:4px;
            left:-1px; right:-1px;
            box-shadow:0 8px 16px rgba(0,0,0,0);
            visibility:hidden;
            opacity:0;
            max-height:0;
            overflow:auto;
            transition:all 0.2s ease, max-height 0.5s ease;
            transition-property: visibility, box-shadow, opacity, max-height;
            z-index:2;

            .option {
                padding:10px;
                font-size:11px;
                background:transparent;
                transition:background 0.2s ease;

                &:not(:last-child){
                    border-bottom:1px solid #eaeaea;
                }

                &:hover {
                    background:rgba(0,0,0,0.02);
                }
            }
        }

        &.open {
            border-bottom-right-radius:0;
            border-bottom-left-radius:0;
            background:#eee;

            .options {
                box-shadow:0 8px 16px rgba(0,0,0,0.05);
                visibility:visible;
                opacity:1;
                max-height:120px;
            }

            .arrow {
                opacity:1;
            }
        }

        &:hover {

            .arrow {
                opacity:1;
            }
        }
    }
</style>