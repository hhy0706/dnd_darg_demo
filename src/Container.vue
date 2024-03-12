<script lang="ts" setup>
import { useDrop } from 'vue3-dnd'
import Card from './Card.vue'
import { ItemTypes } from './ItemTypes'
import { ref } from 'vue'

const ITEMS = [
  {
    id: 1,
    text: 'AAAAAAAAAAAAAAA',
    type: 'left'
  },
  {
    id: 2,
    text: 'BBBBBBBBB',
    type: 'left'
  },
  {
    id: 3,
    text: 'CCCCCCCCC',
    type: 'left'
  },
  {
    id: 4,
    text: 'DDDDDDDDD',
    type: 'left'
  },
  {
    id: 5,
    text: 'EEEEEEEE',
    type: 'left'
  },
  {
    id: 6,
    text: 'FFFFFFFF',
    type: 'left'
  },
  {
    id: 7,
    text: 'GGGGGGGG',
    type: 'left'
  },
]
type ItemType = {
  card: {
    id: number
    text: string
    type: 'left'
  };
  index: number
}
const cards = ref(ITEMS)
const rightCards = ref<any[]>([]);
const findCard = (type: string, id: string | number) => {
  let arr = type === 'left' ? cards.value : rightCards.value
  const card = arr.filter(c => c.id == id)[0] as {
    id: number
    text: string
    type: string
  }
  return {
    card,
    index: arr.indexOf(card),
  }
}

const moveCard = (type: string, id: string | number, atIndex: number, same: boolean) => {

  const [arr1, arr2] = getDragDropData(type)
  const { card, index } = findCard(type, id);
  if (same) {
    console.log("moveCard", arr1[atIndex])
    arr1.splice(index, 1);
    arr1.splice(atIndex, 0, card);

  }
}
const flyCard = (type: string, id: string | number, atIndex: number) => {
  const [arr1, arr2] = getDragDropData(type)
  const { card, index } = findCard(type, id);
  const otherIndex = arr2.findIndex(item => item.id == id);
  if (otherIndex != -1) {
    arr2.splice(otherIndex, 1);
  }
  arr2.splice(atIndex, 0, card);
}
const getRemoveType = (type: string) => {
  return type === 'left' ? 'right' : 'left';
}
const getDragDropData = (type: string) => {
  return type === 'left' ? [cards.value, rightCards.value] : [rightCards.value, cards.value];
}
const dropCard = (item, monitor) => {
  if (monitor.didDrop()) {
    const { type } = monitor.getDropResult();
    console.log(1111111111, type)

    if (type === undefined) {
      const rType = getRemoveType(type);
      const [arr1, arr2] = getDragDropData(item.type)
      const { index: rIndex } = findCard(rType, item.id);
      if (rIndex != -1) arr2.splice(rIndex, 1);
      return;
    };
    const rType = getRemoveType(type);
    const [arr1, arr2] = getDragDropData(type)
    const { index: rIndex } = findCard(rType, item.id);
    if (rIndex != -1) arr2.splice(rIndex, 1);
    const { card } = findCard(type, item.id);
    card.type = type;
  } else {
    const rType = getRemoveType(item.type);
    const [arr1, arr2] = getDragDropData(item.type)
    const { index: rIndex } = findCard(rType, item.id);
    if (rIndex != -1) arr2.splice(rIndex, 1);
  }
  console.log(1111, cards.value)
}
const [collect, drop] = useDrop(() => ({
  accept: ItemTypes.CARD, collect(monitor) {
    return {
      isMyOver: monitor.isOver({ shallow: true })
    }
  },
  drop(item: any, monitor) {

    if (item.type == 'left') return;
    if (!collect.value.isMyOver) return { type: 'left' };
    console.log(111111122222, cards.value)
    rightCards.value.splice(findCard(item.type, item.id).index, 1)
    const rIndex = findCard('left', item.id).index
    if (rIndex != -1) cards.value.splice(rIndex, 1)
    item.type = 'left'
    cards.value.push(item)
    console.log(1111113333333, cards.value)
  }
}))
const [rightCollect, rightDrop] = useDrop(() => ({
  accept: ItemTypes.CARD,
  collect(monitor) {
    return {
      isMyOver: monitor.isOver({ shallow: true })
    }
  },
  drop(item: any, monitor) {

    if (item.type == 'right') return;
    if (!rightCollect.value.isMyOver) return { type: 'right' };
    cards.value.splice(findCard(item.type, item.id).index, 1)
    const rIndex = findCard('right', item.id).index

    if (rIndex != -1) rightCards.value.splice(rIndex, 1)
    item.type = 'right'
    rightCards.value.push(item)

  }
}))
</script>

<template>
  <div class="body">
    <div :ref="drop" style="width: 400px;height: 800px;background:yellow">
      <Card v-for="card in cards" :type="card.type" :id="card.id" :key="card.id" :text="card.text" :move-card="moveCard"
        :find-card="findCard" :fly-card="flyCard" :drop-card="dropCard" />
    </div>
    <div :ref="rightDrop" style="width: 400px;height: 800px;background: red;overflow: auto;">
      <Card v-for="card in rightCards" :type="card.type" :id="card.id" :key="card.id" :text="card.text"
        :move-card="moveCard" :find-card="findCard" :fly-card="flyCard" :drop-card="dropCard" />
    </div>
  </div>
</template>
<style lang="less" scoped>
.body {
  display: flex;
}
</style>