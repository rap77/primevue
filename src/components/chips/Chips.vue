<template>
    <div class="p-chips p-component">
        <ul :class="['p-inputtext', {'p-disabled': disabled, 'p-focus': focused}]" @click="onWrapperClick()">
            <li v-for="(val,i) of value" :key="val" class="p-chips-token p-highlight">
                <slot :value="val">  
                    <span class="p-chips-token-icon pi pi-fw pi-times" @click="removeItem($event, i)"></span>
                    <span class="p-chips-token-label">{{val}}</span>
                </slot>
            </li>
            <li class="p-chips-input-token">
                <input ref="input" type="text" class="p-inputtext p-component" @focus="onFocus($event)" @blur="onBlur($event)" @keydown="onKeyDown($event)" :disabled="disabled || maxedOut">
            </li>
        </ul>
    </div>
</template>

<script>
export default {
    inheritAttrs: false,
    props: {
        value: Array,
        disabled: Boolean,
        max: Number
    },
    data() {
        return {
            focused: false
        };
    },
    methods: {
        onWrapperClick() {
            this.$refs.input.focus();
        },
        onFocus(event) {
            this.focused = true;
            this.$emit('focus', event);
        },
        onBlur() {
            this.focused = false;
            this.$emit('blur', event);
        },
        onKeyDown(event) {
            const inputValue = event.target.value;

            switch(event.which) {
                //backspace
                case 8:
                    if (inputValue.length === 0 && this.value && this.value.length > 0) {
                        this.removeItem(event, this.value.length - 1);
                    }
                break;
            
                //enter
                case 13:
                    if (inputValue && inputValue.trim().length && (!this.max || this.max > this.value.length)) {
                        let values = this.value ? [...this.value]: [];
                        values.push(inputValue);
                        this.$emit('input', values);
                        this.$emit('add', {
                            originalEvent: event,
                            value: values
                        });
                    }

                    this.$refs.input.value = '';
                    event.preventDefault();
                break;
            }
        },
        removeItem(event, index) {
            if (this.disabled) {
                return;
            }
            
            let values = [...this.value];
            const removedItem = values.splice(index, 1);
            this.$emit('input', values);
            this.$emit('remove', {
                originalEvent: event,
                value: removedItem
            });
        }
    },
    computed: {
        maxedOut() {
            return this.max && this.value && this.max === this.value.length;
        }
    }
}
</script>

<style>

</style>
