<template>
    <div class="orca-error-msg">
        <div class="orca-error" v-if="errorMsg">{{errorMsg}}</div>
    </div>
</template>

<script>
import validation from '@/assets/js/validation'

export default {
    props: {
        value: [String, Number, Object, Array],
        required: Boolean,
        minlength: String,
        maxlength: String,
        min: String,
        max: String,
        equalto: String,
        validemail: Boolean,
        accept: String,
        maxfile: String,
        maxsize: String
    },
    data() {
        return {
            types: ['required', 'minlength', 'maxlength', 'min', 'max', 'equalto', 'validemail', 'accept', 'maxfile', 'maxsize'],
            errorMsg: ''
        }
    },
    methods: {
        validate() {
            if (this.value != undefined && this.value != null) {
                this.types.forEach(t => {
                    if (this[t]) {
                        const error = validation[t](this.value, this[t])
                        if (error.type) {
                            this.errorMsg = error.message
                        }
                    }
                })
            }
        }
    },
    watch: {
        value() {
            this.errorMsg = ''
            this.validate()
        }
    }
}
</script>

<style>

</style>