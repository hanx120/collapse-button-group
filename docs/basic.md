基本用法

```vue
<template>
  <div style="margin: 20px;">
    <el-button-group-enhance :buttons="buttons" :collapseAt="2" :data="{a: 1}"></el-button-group-enhance>
  </div>
</template>

<script>
export default {
  data() {
    return {
      buttons: [
        {
          text: '按钮1',
          click(data) {
            return new Promise(resolve => {
              setTimeout(() => {
                resolve()
                console.log(data)
              }, 3000)
            })
          },
        },
        {
          text: '按钮2'
        },
        {
          text: '按钮5',
        },
        {
          text: '按钮6',
          show(data) {
            return data.a !== 1;
          }
        },
      ],
    }
  },
}
</script>
```