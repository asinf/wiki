<template lang='pug'>
  v-container(fluid, grid-list-lg)
    v-layout(row wrap)
      v-flex(xs12)
        .admin-header
          img.animated.fadeInUp(src='/_assets/svg/icon-categorize.svg', alt='General', style='width: 80px;')
          .admin-header-title
            .headline.primary--text.animated.fadeInLeft {{ $t('admin:general.title') }}
            .subtitle-1.grey--text.animated.fadeInLeft {{ $t('admin:general.subtitle') }}
          v-spacer
          v-btn.animated.fadeInDown(color='success', depressed, @click='save', large)
            v-icon(left) mdi-check
            span {{$t('common:actions.apply')}}
        v-form.pt-3
          v-layout(row wrap)
            v-flex(lg6 xs12)
              v-form
                v-card.animated.fadeInUp
                  v-toolbar(color='primary', dark, dense, flat)
                    v-toolbar-title.subtitle-1 {{ $t('admin:general.siteInfo') }}
                  .overline.grey--text.pa-4 {{$t('admin:general.general')}}
                  .px-3.pb-3
                    v-text-field(
                      outlined
                      :label='$t(`admin:general.siteUrl`)'
                      required
                      :counter='255'
                      v-model='config.host'
                      prepend-icon='mdi-label-variant-outline'
                      :hint='$t(`admin:general.siteUrlHint`)'
                      persistent-hint
                      )
                    v-text-field.mt-3(
                      outlined
                      :label='$t(`admin:general.siteTitle`)'
                      required
                      :counter='50'
                      v-model='config.title'
                      prepend-icon='mdi-earth'
                      :hint='$t(`admin:general.siteTitleHint`)'
                      persistent-hint
                      )
                  v-divider
                  .overline.grey--text.pa-4 {{$t('admin:general.logo')}}
                  .pt-2.pb-7.pl-10.pr-3
                    .d-flex.align-center
                      v-avatar(size='100', tile)
                        v-img(
                          :src='config.logoUrl'
                          lazy-src='data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAQAAAC1HAwCAAAAC0lEQVR42mNcWQ8AAdcBKrJda2oAAAAASUVORK5CYII='
                          aspect-ratio='1'
                          )
                      .ml-4(style='flex: 1 1 auto;')
                        v-text-field(
                          outlined
                          :label='$t(`admin:general.logoUrl`)'
                          v-model='config.logoUrl'
                          :hint='$t(`admin:general.logoUrlHint`)'
                          persistent-hint
                          append-icon='mdi-folder-image'
                          @click:append='browseLogo'
                          @keyup.enter='refreshLogo'
                        )
                  v-divider
                  .overline.grey--text.pa-4 {{$t('admin:general.footerCopyright')}}
                  .px-3.pb-3
                    v-text-field(
                      outlined
                      :label='$t(`admin:general.companyName`)'
                      v-model='config.company'
                      :counter='255'
                      prepend-icon='mdi-domain'
                      persistent-hint
                      :hint='$t(`admin:general.companyNameHint`)'
                      )
                    v-select.mt-3(
                      outlined
                      :label='$t(`admin:general.contentLicense`)'
                      :items='contentLicenses'
                      v-model='config.contentLicense'
                      prepend-icon='mdi-creative-commons'
                      :return-object='false'
                      :hint='$t(`admin:general.contentLicenseHint`)'
                      persistent-hint
                    )
                    v-text-field.mt-3(
                      outlined
                      :label='$t(`admin:general.footerOverride`)'
                      v-model='config.footerOverride'
                      prepend-icon='mdi-page-layout-footer'
                      append-icon='mdi-language-markdown'
                      persistent-hint
                      :hint='$t(`admin:general.footerOverrideHint`)'
                      )
                  v-divider
                  .overline.grey--text.pa-4 SEO
                  .px-3.pb-3
                    v-text-field(
                      outlined
                      :label='$t(`admin:general.siteDescription`)'
                      :counter='255'
                      v-model='config.description'
                      prepend-icon='mdi-compass'
                      :hint='$t(`admin:general.siteDescriptionHint`)'
                      persistent-hint
                      )
                    v-select.mt-3(
                      outlined
                      :label='$t(`admin:general.metaRobots`)'
                      multiple
                      :items='metaRobots'
                      v-model='config.robots'
                      prepend-icon='mdi-compass'
                      :return-object='false'
                      :hint='$t(`admin:general.metaRobotsHint`)'
                      persistent-hint
                      )

            v-flex(lg6 xs12)
              v-card.animated.fadeInUp.wait-p4s
                v-toolbar(color='deep-purple', dark, dense, flat)
                  v-toolbar-title.subtitle-1 Features
                v-card-text
                  //- v-switch(
                  //-   inset
                  //-   label='Asset Image Optimization'
                  //-   color='deep-purple'
                  //-   v-model='config.featureTinyPNG'
                  //-   persistent-hint
                  //-   hint='Image optimization tool to reduce filesize and bandwidth costs.'
                  //-   disabled
                  //-   )
                  //- v-text-field.mt-3(
                  //-   outlined
                  //-   label='TinyPNG API Key'
                  //-   :counter='255'
                  //-   v-model='config.description'
                  //-   prepend-icon='mdi-subdirectory-arrow-right'
                  //-   hint='Get your API key at https://tinypng.com/developers'
                  //-   persistent-hint
                  //-   disabled
                  //-   )

                  //- v-divider.mt-3
                  //- v-switch(
                  //-   inset
                  //-   label='Page Ratings'
                  //-   color='deep-purple'
                  //-   v-model='config.featurePageRatings'
                  //-   persistent-hint
                  //-   hint='Allow users to rate pages.'
                  //-   disabled
                  //-   )

                  //- v-divider.mt-3
                  v-switch.mt-0(
                    inset
                    label='Comments'
                    color='deep-purple'
                    v-model='config.featurePageComments'
                    persistent-hint
                    hint='Allow users to leave comments on pages.'
                    )

                  //- v-divider.mt-3
                  //- v-switch(
                  //-   inset
                  //-   label='Personal Wikis'
                  //-   color='deep-purple'
                  //-   v-model='config.featurePersonalWikis'
                  //-   persistent-hint
                  //-   hint='Allow users to have their own personal wiki.'
                  //-   disabled
                  //-   )

              v-card.mt-5.animated.fadeInUp.wait-p6s
                v-toolbar(color='primary', dark, dense, flat)
                  v-toolbar-title.subtitle-1 URL Handling
                v-card-text
                  v-text-field(
                    outlined
                    :label='$t(`admin:general.pageExtensions`)'
                    v-model='config.pageExtensions'
                    prepend-icon='mdi-format-text-wrapping-overflow'
                    :hint='$t(`admin:general.pageExtensionsHint`)'
                    persistent-hint
                    )

              v-card.mt-5.animated.fadeInUp.wait-p7s
                v-toolbar(color='primary', dark, dense, flat)
                  v-toolbar-title.subtitle-1 {{$t('admin:general.editShortcuts')}}
                v-card-text
                  v-switch.mt-0(
                    inset
                    :label='$t(`admin:general.editFab`)'
                    color='primary'
                    v-model='config.editFab'
                    persistent-hint
                    :hint='$t(`admin:general.editFabHint`)'
                    )
                v-divider
                .overline.grey--text.pa-4 {{$t('admin:general.editMenuBar')}}
                .px-3.pb-3
                  v-switch.mt-0.ml-1(
                    inset
                    :label='$t(`admin:general.displayEditMenuBar`)'
                    color='primary'
                    v-model='config.editMenuBar'
                    persistent-hint
                    :hint='$t(`admin:general.displayEditMenuBarHint`)'
                    )
                  v-switch.mt-4.ml-1(
                    v-if='config.editMenuBar'
                    inset
                    :label='$t(`admin:general.displayEditMenuBtn`)'
                    color='primary'
                    v-model='config.editMenuBtn'
                    persistent-hint
                    :hint='$t(`admin:general.displayEditMenuBtnHint`)'
                    )
                  v-switch.mt-4.ml-1(
                    v-if='config.editMenuBar'
                    inset
                    :label='$t(`admin:general.displayEditMenuExternalBtn`)'
                    color='primary'
                    v-model='config.editMenuExternalBtn'
                    persistent-hint
                    :hint='$t(`admin:general.displayEditMenuExternalBtnHint`)'
                    )
                template(v-if='config.editMenuBar && config.editMenuExternalBtn')
                  v-divider
                  .overline.grey--text.pa-4 External Edit Button
                  .px-3.pb-3
                    v-text-field(
                      outlined
                      :label='$t(`admin:general.editMenuExternalName`)'
                      v-model='config.editMenuExternalName'
                      prepend-icon='mdi-format-title'
                      :hint='$t(`admin:general.editMenuExternalNameHint`)'
                      persistent-hint
                      )
                    v-text-field.mt-3(
                      outlined
                      :label='$t(`admin:general.editMenuExternalIcon`)'
                      v-model='config.editMenuExternalIcon'
                      prepend-icon='mdi-dice-5'
                      :hint='$t(`admin:general.editMenuExternalIconHint`)'
                      persistent-hint
                      )
                    v-text-field.mt-3(
                      outlined
                      :label='$t(`admin:general.editMenuExternalUrl`)'
                      v-model='config.editMenuExternalUrl'
                      prepend-icon='mdi-near-me'
                      :hint='$t(`admin:general.editMenuExternalUrlHint`)'
                      persistent-hint
                      )

    component(:is='activeModal')

</template>

<script>
import _ from 'lodash'
import { sync } from 'vuex-pathify'
import gql from 'graphql-tag'

import editorStore from '../../store/editor'

/* global WIKI */

const titleRegex = /[<>"]/i

WIKI.$store.registerModule('editor', editorStore)

export default {
  i18nOptions: { namespaces: 'editor' },
  components: {
    editorModalMedia: () => import(/* webpackChunkName: "editor", webpackMode: "lazy" */ '../editor/editor-modal-media.vue')
  },
  data() {
    return {
      config: {
        host: '',
        title: '',
        description: '',
        robots: [],
        analyticsService: '',
        analyticsId: '',
        company: '',
        contentLicense: '',
        footerOverride: '',
        logoUrl: '',
        featureAnalytics: false,
        featurePageRatings: false,
        featurePageComments: false,
        featurePersonalWikis: false,
        featureTinyPNG: false,
        pageExtensions: '',
        editFab: false,
        editMenuBar: false,
        editMenuBtn: false,
        editMenuExternalBtn: false,
        editMenuExternalName: '',
        editMenuExternalIcon: '',
        editMenuExternalUrl: ''
      },
      metaRobots: [
        { text: 'Index', value: 'index' },
        { text: 'Follow', value: 'follow' },
        { text: 'No Index', value: 'noindex' },
        { text: 'No Follow', value: 'nofollow' }
      ]
    }
  },
  computed: {
    siteTitle: sync('site/title'),
    logoUrl: sync('site/logoUrl'),
    company: sync('site/company'),
    contentLicense: sync('site/contentLicense'),
    footerOverride: sync('site/footerOverride'),
    activeModal: sync('editor/activeModal'),
    contentLicenses () {
      return [
        { value: '', text: this.$t('common:license.none') },
        { value: 'alr', text: this.$t('common:license.alr') },
        { value: 'cc0', text: this.$t('common:license.cc0') },
        { value: 'ccby', text: this.$t('common:license.ccby') },
        { value: 'ccbysa', text: this.$t('common:license.ccbysa') },
        { value: 'ccbynd', text: this.$t('common:license.ccbynd') },
        { value: 'ccbync', text: this.$t('common:license.ccbync') },
        { value: 'ccbyncsa', text: this.$t('common:license.ccbyncsa') },
        { value: 'ccbyncnd', text: this.$t('common:license.ccbyncnd') }
      ]
    }
  },
  methods: {
    async save () {
      const title = _.get(this.config, 'title', '')
      if (titleRegex.test(title)) {
        this.$store.commit('showNotification', {
          style: 'error',
          message: this.$t('admin:general.siteTitleInvalidChars'),
          icon: 'alert'
        })
        return
      }
      try {
        await this.$apollo.mutate({
          mutation: gql`
            mutation (
              $host: String
              $title: String
              $description: String
              $robots: [String]
              $analyticsService: String
              $analyticsId: String
              $company: String
              $contentLicense: String
              $footerOverride: String
              $logoUrl: String
              $pageExtensions: String
              $featurePageRatings: Boolean
              $featurePageComments: Boolean
              $featurePersonalWikis: Boolean
              $editFab: Boolean
              $editMenuBar: Boolean
              $editMenuBtn: Boolean
              $editMenuExternalBtn: Boolean
              $editMenuExternalName: String
              $editMenuExternalIcon: String
              $editMenuExternalUrl: String
            ) {
              site {
                updateConfig(
                  host: $host
                  title: $title
                  description: $description
                  robots: $robots
                  analyticsService: $analyticsService
                  analyticsId: $analyticsId
                  company: $company
                  contentLicense: $contentLicense
                  footerOverride: $footerOverride
                  logoUrl: $logoUrl
                  pageExtensions: $pageExtensions
                  featurePageRatings: $featurePageRatings
                  featurePageComments: $featurePageComments
                  featurePersonalWikis: $featurePersonalWikis
                  editFab: $editFab
                  editMenuBar: $editMenuBar
                  editMenuBtn: $editMenuBtn
                  editMenuExternalBtn: $editMenuExternalBtn
                  editMenuExternalName: $editMenuExternalName
                  editMenuExternalIcon: $editMenuExternalIcon
                  editMenuExternalUrl: $editMenuExternalUrl
                ) {
                  responseResult {
                    succeeded
                    errorCode
                    slug
                    message
                  }
                }
              }
            }
          `,
          variables: {
            host: _.get(this.config, 'host', ''),
            title: _.get(this.config, 'title', ''),
            description: _.get(this.config, 'description', ''),
            robots: _.get(this.config, 'robots', []),
            analyticsService: _.get(this.config, 'analyticsService', ''),
            analyticsId: _.get(this.config, 'analyticsId', ''),
            company: _.get(this.config, 'company', ''),
            contentLicense: _.get(this.config, 'contentLicense', ''),
            footerOverride: _.get(this.config, 'footerOverride', ''),
            logoUrl: _.get(this.config, 'logoUrl', ''),
            pageExtensions: _.get(this.config, 'pageExtensions', ''),
            featurePageRatings: _.get(this.config, 'featurePageRatings', false),
            featurePageComments: _.get(this.config, 'featurePageComments', false),
            featurePersonalWikis: _.get(this.config, 'featurePersonalWikis', false),
            editFab: _.get(this.config, 'editFab', false),
            editMenuBar: _.get(this.config, 'editMenuBar', false),
            editMenuBtn: _.get(this.config, 'editMenuBtn', false),
            editMenuExternalBtn: _.get(this.config, 'editMenuExternalBtn', false),
            editMenuExternalName: _.get(this.config, 'editMenuExternalName', ''),
            editMenuExternalIcon: _.get(this.config, 'editMenuExternalIcon', ''),
            editMenuExternalUrl: _.get(this.config, 'editMenuExternalUrl', '')
          },
          watchLoading (isLoading) {
            this.$store.commit(`loading${isLoading ? 'Start' : 'Stop'}`, 'admin-site-update')
          }
        })
        this.$store.commit('showNotification', {
          style: 'success',
          message: this.$t('admin:general.saveSuccess'),
          icon: 'check'
        })
        this.siteTitle = this.config.title
        this.company = this.config.company
        this.contentLicense = this.config.contentLicense
        this.footerOverride = this.config.footerOverride
        this.logoUrl = this.config.logoUrl
      } catch (err) {
        this.$store.commit('pushGraphError', err)
      }
    },
    browseLogo () {
      this.$store.set('editor/editorKey', 'common')
      this.activeModal = 'editorModalMedia'
    },
    refreshLogo () {
      this.$forceUpdate()
    }
  },
  mounted () {
    this.$root.$on('editorInsert', opts => {
      this.config.logoUrl = opts.path
    })
  },
  beforeDestroy() {
    this.$root.$off('editorInsert')
  },
  apollo: {
    config: {
      query: gql`
        {
          site {
            config {
              host
              title
              description
              robots
              analyticsService
              analyticsId
              company
              contentLicense
              footerOverride
              logoUrl
              pageExtensions
              featurePageRatings
              featurePageComments
              featurePersonalWikis
              editFab
              editMenuBar
              editMenuBtn
              editMenuExternalBtn
              editMenuExternalName
              editMenuExternalIcon
              editMenuExternalUrl
            }
          }
        }
      `,
      fetchPolicy: 'network-only',
      update: (data) => _.cloneDeep(data.site.config),
      watchLoading (isLoading) {
        this.$store.commit(`loading${isLoading ? 'Start' : 'Stop'}`, 'admin-site-refresh')
      }
    }
  }
}
</script>

<style lang='scss'>

</style>
