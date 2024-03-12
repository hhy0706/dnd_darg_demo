<script  lang="ts" setup>
import { useDrag, useDrop } from 'vue3-dnd'
import { ItemTypes } from './ItemTypes'
import { computed, unref } from 'vue'

const props = defineProps<{
  id: string
  text: string
  type: string
  moveCard: (type: string, id: string, to: number,same:boolean) => void
  findCard: (type: string, id: string) => { index: number }
  flyCard: (type: string, id: string, to: number) => void
}>()

interface Item {
  id: string
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
    // if (!monitor.didDrop()) return;
    // const { id: droppedId, originalIndex, type } = monitor.getDropResult()
    // if (type !== props.type) {
    //   props.flyCard(type, droppedId,);
    // }
  },
}))

const [, drop] = useDrop(() => ({
  accept: ItemTypes.CARD,
  hover({ id: draggedId, type }: Item) {
    if (draggedId !== props.id && type === props.type) {
      const { index: overIndex } = props.findCard(props.type, props.id)
      props.moveCard(type, draggedId, overIndex,type === props.type)
    }
  },
  drop(item) {
    return item;
  }
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