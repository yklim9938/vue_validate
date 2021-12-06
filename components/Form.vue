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
            inputAttributes: []
        }
    },
    methods: {
        inputEvent(e) {
            const orcaId = e.target.dataset.orcaId
            // remove error element
            const errorEls = this.getErrorEl(e.target.parentElement)
            errorEls.forEach(e => e.remove())

            this.inputAttributes[orcaId].forEach(a => {
                const errorEls = this.getErrorEl(e.target.parentElement)
                if (errorEls.length > 0) return // already has error, return

                // create error element
                let value = e.target.value
                if (e.target.type == 'file') value = e.target.files
                const error = validation[a.type](value, a.value)
                if (error.type) {
                    const errorEl = document.createElement('div')
                    errorEl.classList.add('orca-error')
                    errorEl.innerHTML = error.message
                    e.target.parentElement.insertBefore(errorEl, e.target.nextSibling)
                }
            })
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
            if (typeof this.onSubmit == 'function') {
                let isValid = true
                this.inputs.forEach(i => {
                    const errorEls = this.getErrorEl(i.parentElement)
                    if (errorEls.length > 0) isValid = false
                })
                e.isValid = isValid
                this.onSubmit(e)
            }
        }
    },
    mounted() {
        this.inputs = this.$refs.form.querySelectorAll('input.orca, select.orca, textarea.orca')

        this.inputs.forEach((i, index) => {
            i.addEventListener(this.getEventType(i), this.inputEvent)
            i.dataset.orcaId = index

            let attributes = []
            for (let a = 0; a < i.attributes.length; a++) {
                if (validation[i.attributes[a].name]) {
                    attributes.push({
                        type: i.attributes[a].name,
                        value: i.attributes[a].value
                    })
                }
            }
            this.inputAttributes.push(attributes)
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