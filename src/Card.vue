<script lang="ts" setup>
import { useDrag, useDrop } from 'vue3-dnd'
import { ItemTypes } from './ItemTypes'
import { computed, unref } from 'vue'

const props = defineProps<{
  id: number
  text: string
  type: string
  moveCard: (type: string, id: number, to: number, same: boolean) => void
  findCard: (type: string, id: number) => { index: number }
  flyCard: (type: string, id: number, to: number) => void
  dropCard: (item: any, monitor: any) => void
}>()

interface Item {
  id: number
  originalIndex: number,
  text: string
  type: string
}

const originalIndex = computed(() => props.findCard(props.type, props.id).index)
const [collect, drag] = useDrag(() => ({
  type: ItemTypes.CARD,
  item: () => ({ id: props.id, originalIndex: originalIndex.value, text: props.text, type: props.type }),
  collect: monitor => ({
    isDragging: monitor.isDragging(),
  }),
  end: (item, monitor) => {
    
    props.dropCard(item, monitor);
  },
}))

const [, drop] = useDrop(() => ({
  accept: ItemTypes.CARD,
  hover({ id: draggedId, type }: Item) {

    if (draggedId != props.id) {
      const { index: overIndex } = props.findCard(props.type, props.id)
      if (type === props.type) {
        props.moveCard(type, draggedId, overIndex, type === props.type)
      } else {
       
        props.flyCard(type, draggedId, overIndex)
      }

    }
  },
}))

// const { isDragging } = toRefs(collect)
const opacity = computed(() => (unref(collect.value.isDragging) ? 0 : 1))
</script>

<template>
  <div :ref="node => drag(drop(node))" class="card" :style="{ opacity }">
    {{ text }}
  </div>
</template>

<style lang="less" scoped>
.card {
  margin-bottom: 0.5rem;
  padding: 0.5rem 1rem;
  background-color: white;
  border: 1px dashed gray;
  cursor: move;
}
</style>