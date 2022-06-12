<template>
  <div id="cover">
    <h1 id="cover__title">カンタン！あなたのオンライン本棚</h1>
  </div>
</template>
<script>
export default {
    name:'LayoutCover',
}
</script>
<style scoped>
#cover {
  color: white;
  height: 300px;
  background-color: black;
  background-image: url("~assets/images/cover.jpg");
  background-position: center center;
  background-repeat: no-repeat;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-flow: column nowrap;
}

#cover__title {
  font-size: 36px;
  letter-spacing: 10px;
}
</style>