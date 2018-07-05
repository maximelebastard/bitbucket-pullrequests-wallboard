<template id="#pr-template">
  <div class="pr">
    <div class="head">
      <img :src="authoravatar" :alt="authorname" />
      {{ participants }}
      #{{ id }}
      {{ title }}
    </div>
    <div class="branches">{{ source }} <i class="fas fa-arrow-right"></i> {{ destination }}</div>
  </div>
</template>

<script>
export default {
  template: '#pr-template',
  name: 'pr',
  props: [
    'id',
    'title',
    'source',
    'destination',
    'state',
    'authorname',
    'authoravatar',
    'participants'
  ]
}
</script>

<style>
.pr {
  background: #e4e4e4;
  padding: 4px;
  border-radius: 4px;
  box-shadow: -1px 7px 11px 0px rgba(0,0,0,0.36);
}

.pr img {
  vertical-align: middle;
}

.pr .head {
  font-size: 24px;
  text-overflow: ellipsis;
  overflow: hidden;
  white-space: nowrap;
}

.pr .branches {
  font-size: 12px;
}

</style>
