<style lang="stylus" scoped>
input[name='path']
  box-sizing: border-box
  width: 100%
</style>

<template lang="jade">
article.form.fileAdd(transition='driftFade')
  form(@keyup.enter='submit')
    header
      h1.
        Start watching a file in #[strong {{watcher.name}}]
    p {{appName}} can watch an track all the files in your server that need to be monitored to ensure system intigrity.
    p.
      Recommended files to watch are those containing the system and access logs, such as #[code.clickable(@click='watchMe') /var/log/syslog] and #[code.clickable(@click='watchMe') /var/log/auth.log].
    fieldset
      label(for='path') File path
      input(type='text', name='path', placeholder='/var/log/syslog', v-model='path', v-focus-auto)
      span.tip.
        Please consign the #[strong absolute path] of the file to watch.
    footer
      button.ok(@click='submit', v-if="path.split('/').pop()").
        Start watching #[i {{path.split('/').pop()}}]
</template>

<script lang="coffee">
app = document.app
module.exports =
  mixins: [ (require 'vue-focus').mixin ]
  data: ->
    $.extend app.data(),
      path: ''
      index: @$parent.index
  computed:
    watcher: ->
      @$parent.currentWatcher
  methods:
    watchMe: (e) ->
      @path = e.target.innerText
    submit: (e) ->
      e.preventDefault()
      settings = @watcher.settings
      settings.files[@path] =
        policies: []
      @$parent.$set 'currentWatcher.settings', settings
      document.vault.replace 'settings', $.extend(settings, {encrypt: true})
      app.save()
      app.router.go
        name: 'file'
        params:
          watcher: @index
          file: encodeURIComponent @path
</script>
