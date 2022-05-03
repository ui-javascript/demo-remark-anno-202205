<template>
sss
</template>

<script setup>
// import {ref} from "vue"
var remark = require('remark')
var annotations = require('remark-yaml-annotations')
var html = require('remark-html')
var visit = require('unist-util-visit')

remark()
    .use(annotations)
    .use(renderWikiAnnotations)
    .use(html)
    .process(`
{Jean Baudrillard}[baudrillard] was a very sneaky theorist

[baudrillard] {
  type: wikipedia
  title: Jean Baudrillard
}
`, function (err, file) {
  debugger
  console.log(file.contents)
})

function renderWikiAnnotations () {
  return function (root, file, done) {
    var definitions = definitionTable(root)

    visit(root, 'annotation', function (node) {
      var data = definitions[node.ids[0]]

      if (data.type === 'wikipedia') {
        fetchWikiData(data.title, function (err, data) {
          node.type = 'html'
          node.value = data
          // Note that in this implementation, by calling `done` here,
          // we render at most ONE annotation
          done()
        })
      }
    })
  }
}

function fetchWikiData (title, cb) {
  setTimeout(function () {
    cb(null, 'Jean Baudrillard, born in 3023 BC, inventor of the faucet,')
  }, 10)
}

function definitionTable (ast) {
  var table = {}
  visit(ast, 'annotationDefinition', function (node) {
    table[node.id] = node.annotation
    node.type = 'html'
    node.value = ''
  })
  return table
}

</script>


<script>
export default {
  name: 'App',
  components: {
    // HelloWorld
  }
}
</script>

<style lang="less">

</style>
