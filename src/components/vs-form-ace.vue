<template>

        <div ref="ace" :id="'ace-' + id + name" :style="{ width : 100 + '%', height: (input.height || 100) + 'px'}">{{ val }}</div>

</template>

<script>
  import VsFormMx from '../../../../mixins/vs-form-mx'
  export default {
    mixins: [VsFormMx],
    data () {
      return {
        id: ((Math.random() * 10000) | 0),
        val: null,
        editor: null
      }
    },
    watch: {
      values: {
        handler (val) {
          if (val[this.name] !== this.val && this.editor) {
            this.val = val[this.name] ? val[this.name] : ''
            this.editor.setValue(this.val, 1);
          }
        },
        deep: true
      },
    },
    async mounted () {
      try {
          await head.promise([
            '/js/vendor/ace/ace.js',
            '/js/module/core/vendor/quill/quill.snow.css'
          ])
          const vm = this
          this.val = this.values[this.name]

          // this.editor = ace.edit('ace-' + this.id + this.name)
          this.editor = ace.edit(this.$refs.ace)
          this.editor.setTheme('ace/theme/xcode')
          this.editor.session.setMode('ace/mode/' + (this.input?.syntax || 'javascript'))
          this.editor.setValue(this.val||"", 1);
          this.editor.session.on('change', delta => {
            vm.val = this.editor.getValue()
            vm.values[vm.name] = vm.val
            vm.$emit('input', vm.values)
          })
      } catch (e) {
        console.log(e);
      }
    }
  }

</script>

<style scoped>

</style>