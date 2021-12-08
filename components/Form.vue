<template>
  <form ref="form" @submit.prevent="submitHandler">
    <slot></slot>
  </form>
</template>

<script>
import validation from '@/assets/js/validation'
export default {
    props: ['onSubmit'],
    data() {
        return {
            inputs: [],
        }
    },
    methods: {
        validateInput(input) {
            let value = input.value
            if (input.type == 'file') value = input.files
            if (!input.required && typeof value == 'string' && value.length < 1) return

            for (let a = 0; a < input.attributes.length; a++) {
                const errorEls = this.getErrorEl(input.parentElement)
                if (errorEls.length > 0) break // already has error, exit loop
                if (!validation[input.attributes[a].name]) continue // not validation attribute, skip

               // create error element
                const error = validation[input.attributes[a].name](value, input.attributes[a].value)
                if (error.type) {
                    const errorEl = document.createElement('div')
                    errorEl.classList.add('orca-error')
                    errorEl.innerHTML = error.message
                    input.parentElement.insertBefore(errorEl, input.nextSibling)
                }
            }
        },
        inputEvent(e) {
            // remove error element
            const errorEls = this.getErrorEl(e.target.parentElement)
            errorEls.forEach(e => e.remove())
            this.validateInput(e.target)
        },
        getEventType(input) {
            let eventType = 'input'
            if (input.tagName.toLowerCase() == 'select') eventType = 'change'
            return eventType
        },
        getErrorEl(wrapper) {
           return wrapper.querySelectorAll('.orca-error')
        },
        submitHandler(e) {
            this.inputs.forEach(i => {
                this.validateInput(i)
            })

            this.$children.forEach(c => {
                c.validate()
            })

            if (typeof this.onSubmit == 'function') {
                setTimeout(() => {
                    let isValid = true
                    const errorEls = this.getErrorEl(this.$refs.form)

                    if (errorEls.length > 0) isValid = false
                    e.isValid = isValid
                    this.onSubmit(e)
                }, 50)
            }
        }
    },
    mounted() {
        this.inputs = this.$refs.form.querySelectorAll('input.orca, select.orca, textarea.orca')

        this.inputs.forEach((i, index) => {
            i.addEventListener(this.getEventType(i), this.inputEvent)
        })
    },
    beforeDestroy() {
        this.inputs.forEach(i => {
            i.removeEventListener(this.getEventType(i), this.inputEvent)
        })
    }
};
</script>

<style>
.orca-error {
    color: red;
    font-size: 0.875rem;
}
</style>