---
title: "Accessing Resources"
date: 2020-08-26T10:56:03
weight: 4
geekdocRepo: https://github.com/owncloud/file-picker
geekdocEditPath: edit/master/docs
geekdocFilePath: accessing-resources.md
---

{{< toc >}}

File picker is returning selected resources via event called `selectResources`. To access them, you need to set an event listener where you'll be able to get them as part of the response of the callback function.

## Access resources
```html
<file-picker id="file-picker" variation="resource"></file-picker>

<script>
  const item = document.getElementById('file-picker')
  let resources = []

  item.addEventListener('selectResources', event => {
    resources = event.detail[0]
  })
</script>
```