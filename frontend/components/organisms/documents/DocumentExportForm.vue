<template>
  <base-card
    :disabled="!valid"
    title="Export Data"
    agree-text="Export"
    cancel-text="Cancel"
    @agree="download"
    @cancel="cancel"
  >
    <template #content>
      <v-form
        ref="form"
        v-model="valid"
      >
        <h2>{{ $t('dataset.importDataMessage1') }}</h2>
        <v-radio-group
          ref="format"
          v-model="selectedFormat"
          :rules="fileFormatRules($t('rules.fileFormatRules'))"
        >
          <v-radio
            v-for="(format, i) in formats"
            :key="i"
            :label="format.text"
            :value="format"
          />
        </v-radio-group>
        <v-sheet
          v-if="selectedFormat"
          :dark="!$vuetify.theme.dark"
          :light="$vuetify.theme.dark"
          class="mb-5 pa-5"
        >
          <span v-for="(example, index) in selectedFormat.examples" :key="index">
            {{ example }}<br>
          </span>
        </v-sheet>
        <h2>{{ $t('dataset.exportDataMessage2') }}</h2>
        <v-text-field v-model="selectedFileName" placeholder="Name the file" />
      </v-form>
    </template>
  </base-card>
</template>

<script>
import BaseCard from '@/components/molecules/BaseCard'
import { fileFormatRules, uploadFileRules } from '@/rules/index'

export default {
  components: {
    BaseCard
  },
  props: {
    exportDocument: {
      type: Function,
      default: () => {},
      required: true
    },
    formats: {
      type: Array,
      default: () => [],
      required: true
    }
  },
  data() {
    return {
      valid: false,
      file: null,
      selectedFormat: null,
      selectedFileName: 'project_' + this.$route.params.id + '_dataset',
      fileFormatRules,
      uploadFileRules
    }
  },

  computed: {
    acceptType() {
      if (this.selectedFormat) {
        return this.selectedFormat.accept
      } else {
        return '.txt,.csv,.json,.jsonl'
      }
    }
  },

  methods: {
    cancel() {
      this.$emit('close')
    },
    validate() {
      return this.$refs.form.validate()
    },
    reset() {
      this.$refs.format.reset()
    },
    download() {
      if (this.validate()) {
        this.exportDocument({
          projectId: this.$route.params.id,
          fileName: this.selectedFileName,
          format: this.selectedFormat.type,
          suffix: this.selectedFormat.suffix
        })
        this.reset()
        this.cancel()
      }
    }
  }
}
</script>
