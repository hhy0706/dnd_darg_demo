<script setup lang="ts">
import { useDrop } from 'vue3-dnd'
import { ItemTypes } from './ItemTypes'
import { computed, unref,toRefs } from 'vue'
// import { toRefs } from '@vueuse/core'

const style = {
  height: '12rem',
  width: '12rem',
  marginRight: '1.5rem',
  marginBottom: '1.5rem',
  color: 'white',
  padding: '1rem',
  textAlign: 'center',
  fontSize: '1rem',
  lineHeight: 'normal',
  float: 'left',
}

const [collect, drop] = useDrop(() => ({
  accept: ItemTypes.BOX,
  drop: (item,monitor) => ({ name: `${item.name} - Dustbin` }),
  collect: monitor => ({
    isOver: monitor.isOver(),
    canDrop: monitor.canDrop(),
    itemTypes: monitor.getItemType()
  }),
}))

const isActive = computed(() => collect.value.canDrop && collect.value.isOver)
const backgroundColor = computed(() => {
  console.log(222222, collect.value);
 return isActive.value ? 'darkgreen' : collect.value.canDrop ? 'red' : '#222'
}
 
)
</script>

<template>
  <div :ref="drop" role="Dustbin" :style="{ ...style, backgroundColor }">
    {{ isActive ? 'Release to drop' : 'Drag a box here' }}
  </div>
</template>