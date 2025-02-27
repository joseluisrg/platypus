<template>
  <div class="code-editor">
    <CodeArea class="code-editor__text-area" :code="internalCode" @codeChanged="codeChanged" />
    <CodeEditorTools
      class="code-editor__tools"
      :copy-text="internalCode"
      :copy-enabled="copyEnabled"
      :reset-enabled="resetEnabled"
      :notebook-enabled="notebookEnabled"
      @reset="resetRequest"
      @notebook="notebook"
    />
    <div
      v-if="resetNotificationOpen"
      class="code-editor__reset-notification__wrapper"
    >
      <bx-toast-notification
        class="code-editor__reset-notification"
        :title="$translate('Reset code block?')"
        :icon-description="$translate('Cancel')"
        :subtitle="$translate('This will reset the code to the textbook default. Any custom edits will be removed.')"
        :timeout="null"
        :open="resetNotificationOpen"
        :kind="'warning'"
        :theme="'dark'"
        @bx-notification-closed="resetCancel"
      >
        <a class="code-editor__reset-notification__confirm-button" @click="resetConfirm">{{ $translate('Reset code') }}</a>
      </bx-toast-notification>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue-demi'
import CodeEditorTools from './CodeEditorTools.vue'
import CodeArea from './CodeArea.vue'
import 'carbon-web-components/es/components/notification/toast-notification'

export default defineComponent({
  name: 'CodeEditor',
  components: {
    CodeEditorTools,
    CodeArea
  },
  props: {
    code: {
      type: String,
      required: false,
      default: ''
    },
    initialCode: {
      type: String,
      required: false,
      default: ''
    },
    copyEnabled: {
      type: Boolean,
      required: false,
      default: true
    },
    resetEnabled: {
      type: Boolean,
      required: false,
      default: true
    },
    notebookEnabled: {
      type: Boolean,
      required: false,
      default: true
    }
  },
  data () {
    return {
      internalCode: '',
      resetNotificationOpen: false
    }
  },
  watch: {
    code (newVal) {
      this.internalCode = newVal
    }
  },
  mounted () {
    this.internalCode = this.code
  },
  methods: {
    resetRequest () {
      this.resetNotificationOpen = true
    },
    resetCancel () {
      this.resetNotificationOpen = false
    },
    resetConfirm () {
      this.resetNotificationOpen = false
      this.internalCode = this.initialCode
      this.$emit('codeChanged', this.internalCode)
    },
    codeChanged (code: string) {
      this.internalCode = code
      this.$emit('codeChanged', this.internalCode)
    },
    notebook () {
      this.$emit('notebookCopyRequest', this.internalCode)
    }
  }
})
</script>

<style lang="scss" scoped>
@import 'carbon-components/scss/globals/scss/spacing';
@import '~/../scss/variables/colors.scss';

.code-editor {
  &__text-area {
    width: 100%;
    height: 100%;
  }
  &__tools {
    position: absolute;
    right: 0;
    top: 0;
    z-index: 2;
  }
  &__reset-notification {
    --cds-inverse-01: #{$text-color-dark};
    --cds-inverse-02: #{$background-color-white};
    width: 22rem;
    height: 11rem;

    &__wrapper {
      position: absolute;
      top: 0;
      left: 0;
      right: 0;
      bottom: 0;
      background-color: $disabled-background-color;
      display: flex;
      justify-content: center;
      z-index: 10;
    }

    &__confirm-button {
      display: block;
      margin-bottom: $spacing-05;
    }
  }
}
</style>
