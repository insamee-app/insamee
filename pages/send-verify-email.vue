<template>
  <section class="w-80 mx-auto">
    <h1 v-if="ok" class="text-center text-2xl">
      Un mail vous a été envoyé afin de vérifier votre compte
    </h1>
    <form v-else action="#" @submit.prevent="sendMail">
      <InsameeLabeledInput
        v-model="$v.email.$model"
        :error-message="mailMessage"
        border
        type="email"
        name="email"
        placeholder="exemple@insamee.fr"
        class="w-full"
        label="Adresse électronique"
      />
      <InsameeAppButton
        large
        class="w-full mt-8"
        :disabled="$v.$invalid"
        :loading="loading"
        type="submit"
        >Envoyer le couriel de vérification</InsameeAppButton
      >
      <InsameeAppError :error-message="error" />
    </form>
  </section>
</template>

<script>
import mailMessages from '~/mixins/validationsMail'

export default {
  mixins: [mailMessages],
  layout: 'minimal',
  data() {
    return {
      error: '',
      loading: false,
      email: '',
      ok: false,
    }
  },
  head() {
    return {
      title: 'Vérifier son email',
    }
  },
  methods: {
    async sendMail() {
      this.loading = true
      try {
        await this.$axios.post('/auth/send/verifyEmail', {
          email: this.email,
        })
        this.error = ''
        this.ok = true
      } catch (error) {
        if (error.response.data.errors)
          this.error = error.response.data.errors[0].message
        else this.error = "Une erreur s'est produite"
      }
      this.loading = false
    },
  },
}
</script>
