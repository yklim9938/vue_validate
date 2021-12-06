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
        validateInput(input, index) {
            let value = input.value
            if (input.type == 'file') value = input.files
            if (!input.required && typeof value == 'string' && value.length < 1) return

            const orcaId = input.dataset.orcaId
            this.inputAttributes[orcaId].forEach(a => {
                const errorEls = this.getErrorEl(input.parentElement)
                if (errorEls.length > 0) return // already has error, return

                // create error element
                const error = validation[a.type](value, a.value)
                if (error.type) {
                    const errorEl = document.createElement('div')
                    errorEl.classList.add('orca-error')
                    errorEl.innerHTML = error.message
                    input.parentElement.insertBefore(errorEl, input.nextSibling)
                }
            })
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