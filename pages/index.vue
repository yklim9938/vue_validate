<template>
  <div>
    <Form :onSubmit="formSubmit">
      <div class="mb-4">
        <label class="mb-2">Name</label>
        <input type="text" name="Name" autocomplete="off" v-model="name">
        <ErrorMessage v-model="name" minlength="4"></ErrorMessage>
      </div>
      <div class="mb-4">
        <label class="mb-2">Email</label>
        <input type="email" validemail name="email" class="orca" required minlength="4" autocomplete="off">
      </div>
      <div class="mb-4">
        <label class="mb-2">File</label>
        <input type="file" class="orca" multiple accept="image/*" autocomplete="off" maxsize="1024">
      </div>
      <div class="mb-4">
        <label class="mb-2">Password</label>
        <input type="password" class="orca" autocomplete="off" maxsize="1024" equalto="cPass">
      </div>
      <div class="mb-4">
        <label class="mb-2">Confirm Password</label>
        <input type="password" class="orca" autocomplete="off" maxsize="1024" id="cPass" label="CCPassword">
      </div>
      <div class="mb-4">
        <label class="mb-2">Select</label>
        <select class="orca" required autocomplete="off">
          <option value="">0</option>
          <option value="1">1</option>
          <option value="2">2</option>
        </select>
      </div>
      <div>
        <button type="submit">Submit</button>
      </div>
    </Form>


    <nuxt-link to="/hi">Hi Page</nuxt-link>
  </div>
</template>

<script>
import ErrorMessage from '../components/ErrorMessage.vue'
export default {
  components: { ErrorMessage },
  data() {
    return {
      name: ''
    }
  },
  methods: {
    formSubmit(e) {
      console.log(e)
    }
  }
}
</script>
