<template>
  <div
    class="flex flex-col md:flex-row -mx-6 px-6 py-2 md:py-0 space-y-2 md:space-y-0"
    :dusk="field.attribute"
  >
    <div class="md:w-1/4 md:py-3">
      <slot>
        <h4 class="font-bold md:font-normal">
          <span>
            <span v-if="tooltip===''">{{ label }}</span>

            <Tooltip :triggers="['click']" v-if="tooltip!==''" class="inline ml-4">
              <span class="cursor-pointer">{{ label }}</span>

              <template v-slot:content >
                <span v-html="tooltip"></span>
              </template>
            </Tooltip>
          </span>
        </h4>
      </slot>
    </div>
    <div class="md:w-3/4 md:py-3 break-all lg:break-words">
      <slot name="value">
        <CopyButton
          v-if="fieldValue && field.copyable && !shouldDisplayAsHtml"
          @click.prevent.stop="copy"
          v-tooltip="__('Copy to clipboard')"
        >
          <span ref="theFieldValue">
            {{ fieldValue }}
          </span>
        </CopyButton>

        <p
          v-else-if="fieldValue && !field.copyable && !shouldDisplayAsHtml"
          class="text-90 flex items-center"
        >
          {{ fieldValue }}
        </p>
        <div
          v-else-if="fieldValue && !field.copyable && shouldDisplayAsHtml"
          v-html="fieldValue"
        />
        <p v-else>&mdash;</p>
      </slot>
    </div>
  </div>
</template>

<script>
import { CopiesToClipboard, FieldValue } from 'laravel-nova'

export default {
  mixins: [CopiesToClipboard, FieldValue],

  props: {
    index: {
      type: Number,
      required: true,
    },

    field: {
      type: Object,
      required: true,
    },

    fieldName: {
      type: String,
      default: '',
    },
  },

  methods: {
    copy() {
      this.copyValueToClipboard(this.field.value)
    },
  },

  computed: {
    label() {
      return this.fieldName || this.field.name
    },

    /**
     * Return the tooltip that should be used for the field.
     */
    tooltip() {
      return this.field.tooltip || ''
    },
  },
}
</script>
