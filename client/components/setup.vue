<template lang="pug">
  v-app.setup
    v-content
      v-container
        v-layout
          v-flex(xs12, lg6, offset-lg3)
            v-card.elevation-20.radius-7.animated.fadeInUp
              v-alert(v-if='isDevMode', tile, dark, color='red darken-3', icon='mdi-alert', prominent)
                .body-2 You are running an unstable, unreleased development version. This code base is #[strong NOT] for production use!
                .body-2.mt-3 Cloning the dev branch directly from GitHub is #[strong NOT] the proper way to install Wiki.js!
                .body-2 Read the #[a(href='https://docs.requarks.io/install', style='color: #FFF;') documentation] on correctly installing the latest stable version.
              .text-center
                img.setup-logo.animated.fadeInUp.wait-p2s(src='/_assets/svg/logo-wikijs-full.svg', alt='Wiki.js Logo')
              v-alert(v-model='error', type='error', icon='mdi-alert', tile, dismissible) {{ errorMessage }}
              v-alert(v-if='!error', tile, color='red lighten-5', :value='true')
                v-icon.mr-3(color='red') mdi-package-variant
                span.red--text You are about to install Wiki.js #[strong {{wikiVersion}}].
              v-card-text
                .overline.pl-3 Administrator Account
                v-container.pa-3.mt-3(grid-list-xl)
                  v-layout(row, wrap)
                    v-flex(xs12)
                      v-text-field(
                        outlined
                        v-model='conf.adminEmail',
                        label='Administrator Email',
                        hint='The email address of the administrator account.',
                        persistent-hint
                        required
                        ref='adminEmailInput'
                      )
                    v-flex(xs6)
                      v-text-field(
                        outlined
                        ref='adminPassword',
                        counter='255'
                        v-model='conf.adminPassword',
                        label='Password',
                        :append-icon="pwdMode ? 'mdi-eye-off' : 'mdi-eye'"
                        @click:append="() => (pwdMode = !pwdMode)"
                        :type="pwdMode ? 'password' : 'text'"
                        hint='At least 8 characters long.',
                        persistent-hint
                      )
                    v-flex(xs6)
                      v-text-field(
                        outlined
                        ref='adminPasswordConfirm',
                        counter='255'
                        v-model='conf.adminPasswordConfirm',
                        label='Confirm Password',
                        :append-icon="pwdConfirmMode ? 'mdi-eye-off' : 'mdi-eye'"
                        @click:append="() => (pwdConfirmMode = !pwdConfirmMode)"
                        :type="pwdConfirmMode ? 'password' : 'text'"
                        hint='Verify your password again.',
                        persistent-hint
                      )
                v-divider.mb-4
                .overline.pl-3.mb-5 Site URL
                v-text-field.mb-4.mx-3(
                  outlined
                  ref='adminSiteUrl',
                  v-model='conf.siteUrl',
                  label='Site URL',
                  hint='Full URL to your wiki, without the trailing slash (e.g. https://wiki.example.com). This should be the public facing URL, not the internal one if using a reverse-proxy.',
                  persistent-hint
                  @keyup.enter='install'
                )
                v-divider.mb-4
                .overline.pl-3.mb-3 Telemetry
                v-switch.ml-3(
                  inset
                  color='primary',
                  v-model='conf.telemetry',
                  label='Allow Telemetry',
                  persistent-hint,
                  hint='Help Wiki.js developers improve this app with anonymized telemetry.'
                )
                a.pl-3(style='font-size: 12px; letter-spacing: initial;', href='https://docs.requarks.io/telemetry', target='_blank') Learn more
              v-divider.mt-2
              v-card-actions
                v-btn(color='primary', @click='install', :disabled='loading', x-large, depressed, block)
                  v-icon(left) mdi-check
                  span Install

    v-dialog(v-model='loading', width='450', persistent)
      v-card(color='primary', dark).radius-7
        v-card-text.text-center.py-5
          .py-3(style='width: 64px; display:inline-block;')
            breeding-rhombus-spinner(
              :animation-duration='2000'
              :size='64'
              color='#FFF'
              )
          template(v-if='!success')
            .subtitle-1.white--text Finalizing your installation...
            .caption Just a moment
          template(v-else)
            .subtitle-1.white--text Installation complete!
            .caption Redirecting...
</template>

<script>
import _ from 'lodash'
import validate from 'validate.js'
import { BreedingRhombusSpinner } from 'epic-spinners'
import confetti from 'canvas-confetti'

/* global siteConfig */

export default {
  components: {
    BreedingRhombusSpinner
  },
  props: {
    wikiVersion: {
      type: String,
      required: true
    }
  },
  data() {
    return {
      loading: false,
      success: false,
      error: false,
      errorMessage: '',
      conf: {
        adminEmail: '',
        adminPassword: '',
        adminPasswordConfirm: '',
        siteUrl: 'https://wiki.yourdomain.com',
        telemetry: true
      },
      pwdMode: true,
      pwdConfirmMode: true,
      isDevMode: false
    }
  },
  mounted() {
    _.delay(() => {
      this.$refs.adminEmailInput.focus()
    }, 500)
    this.isDevMode = siteConfig.devMode === true
  },
  methods: {
    async install () {
      this.error = false

      const validationResults = validate(this.conf, {
        adminEmail: {
          presence: {
            allowEmpty: false
          },
          email: true
        },
        adminPassword: {
          presence: {
            allowEmpty: false
          },
          length: {
            minimum: 6,
            maximum: 255
          }
        },
        adminPasswordConfirm: {
          equality: 'adminPassword'
        },
        siteUrl: {
          presence: {
            allowEmpty: false
          },
          url: {
            schemes: ['http', 'https'],
            allowLocal: true,
            allowDataUrl: false
          },
          format: {
            pattern: '^(?!.*/$).*$',
            flags: 'i',
            message: 'must not have a trailing slash'
          }
        }
      }, {
        format: 'flat'
      })
      if (validationResults) {
        this.error = true
        this.errorMessage = validationResults[0]
        this.$forceUpdate()
        return
      }

      this.loading = true
      this.success = false
      this.$forceUpdate()

      _.delay(async () => {
        try {
          const resp = await fetch('/finalize', {
            method: 'POST',
            cache: 'no-cache',
            headers: {
              'Content-Type': 'application/json'
            },
            body: JSON.stringify(this.conf)
          }).then(res => res.json())

          if (resp.ok === true) {
            _.delay(() => {
              confetti({
                particleCount: 100,
                spread: 70,
                zIndex: 100000
              })
              this.success = true
              _.delay(() => {
                window.location.assign('/login')
              }, 3000)
            }, 10000)
          } else {
            this.error = true
            this.errorMessage = resp.error
            this.loading = false
          }
        } catch (err) {
          window.alert(err.message)
        }
      }, 1000)
    }
  }
}

</script>

<style lang='scss'>
.setup {
  .v-application--wrap {
    padding-top: 10vh;
    background-color: #111;
    background-image: linear-gradient(45deg, mc('red', '100'), mc('red', '700'), mc('deep-purple', '900'));
    background-blend-mode: exclusion;

    &::before {
      content: '';
      position: absolute;
      left: 0;
      top: 0;
      width: 100%;
      height: 100vh;
      z-index: 0;
      background-color: transparent;
      background-image: url(../static/svg/motif-grid.svg) !important;
      background-size: 100px;
      background-repeat: repeat;
      animation: bg-anim 100s linear infinite;
    }
  }

  @keyframes bg-anim {
    0% {
      background-position: 0 0;
    }
    100% {
      background-position: 100% 100%;
    }
  }

  &-logo {
    width: 400px;
    margin: 2rem 0 2rem 0;
  }
}
</style>
