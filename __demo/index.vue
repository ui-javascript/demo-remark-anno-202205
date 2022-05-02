<template>
  {{ content }}
</template>

<script setup>
import {ref} from "vue"
import { remark } from 'remark'
import { annotations } from 'remark-yaml-annotations'
import { html } from'remark-html'
import { visit } from 'unist-util-visit'


const content = ref("")

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

remark()
.use(annotations)
.use(renderWikiAnnotations)
.use(html)
.process(`
{BYAAAA!}[bya]

[bya] {
  type: image
  src: lord-have-mercy.bmp
  alt: me killing the game
}
`, function (err, file) {
  if (err) {
    console.log(err)
    return 
  }
  console.log(file.contents)
  content.value = file.contents
})

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
