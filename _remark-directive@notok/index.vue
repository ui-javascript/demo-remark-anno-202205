<template>
sss
</template>

<script setup>

import {read} from 'to-vfile'
import {unified} from 'unified'
import remarkParse from 'remark-parse'
import remarkDirective from 'remark-directive'
import remarkRehype from 'remark-rehype'
import rehypeFormat from 'rehype-format'
import rehypeStringify from 'rehype-stringify'
import {visit} from 'unist-util-visit'
import {h} from 'hastscript'

main()

async function main() {
  const file = await unified()
    .use(remarkParse)
    .use(remarkDirective)
    .use(myRemarkPlugin)
    .use(remarkRehype)
    .use(rehypeFormat)
    .use(rehypeStringify)
    .process(await read('example.md'))

  console.log(String(file))
}

// This plugin is an example to let users write HTML with directives.
// It’s informative but rather useless.
// See below for others examples.
/** @type {import('unified').Plugin<[], import('mdast').Root>} */
function myRemarkPlugin() {
  return (tree) => {
    visit(tree, (node) => {
      if (
        node.type === 'textDirective' ||
        node.type === 'leafDirective' ||
        node.type === 'containerDirective'
      ) {
        const data = node.data || (node.data = {})
        const hast = h(node.name, node.attributes)

        data.hName = hast.tagName
        data.hProperties = hast.properties
      }
    })
  }
}
</script>


<script>
export default {
  name: 'App'
}
</script>

<style lang="less">

</style>
