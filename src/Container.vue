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
/// 从drop drag数据数组找到card
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
//在同一边数据，移动card
const moveCard = (type: string, id: string | number, atIndex: number) => {
  const [arr1, arr2] = getDragDropData(type)
  const { card, index } = findCard(type, id);
  
    /// 删除拖拽index位置上的card,添加到滑动card的index上
    console.log("moveCard", arr1[atIndex])
    arr1.splice(index, 1);
    arr1.splice(atIndex, 0, card);

}
//不在同一边数据，移动card
const flyCard = (type: string, id: string | number, atIndex: number) => {
  const [arr1, arr2] = getDragDropData(type)
  const { card, index } = findCard(type, id);
  const otherIndex = arr2.findIndex(item => item.id == id);
  // 如果另一个数组已有该项card，先把之前的移除
  if (otherIndex != -1) {
    arr2.splice(otherIndex, 1);
  }
  ///在划过位置上加，放下的时候再改变type
  arr2.splice(atIndex, 0, card);
}
const getRemoveType = (type: string) => {
  return type === 'left' ? 'right' : 'left';
}
/// 获取拖拽 放置数组
const getDragDropData = (type: string) => {
  return type === 'left' ? [cards.value, rightCards.value] : [rightCards.value, cards.value];
}
/// 处理放置card
const dropCard = (item:any, monitor:any) => {
  if (monitor.didDrop()) {
    const { type } = monitor.getDropResult();
    
    console.log(11111111111, type)
    ///放置在两个大容器 触发，
    if (type === undefined) {
      /// 如果放置在另一边，在addCard处理了删除原来那边card,
      /// 如果先划过另一边 在放置原来的那边，需要把另一边的card移除
      removeCard(item.type, item.id);
      return;
    };
    /// 放置在另一边card,把原来那边card移除
    removeCard(type, item.id);
    const { card } = findCard(type, item.id);
    /// 划过的时候没改type这里改
    card.type = type;
  } else {
    /// 没放置成功直接移除
    removeCard(item.type, item.id);
  }
}
/// 移除某一边card
const removeCard = (type: string, id: number) => {
  /// 如果放置数组的另一边，有同样card就移除
  const rType = getRemoveType(type);
  const [arr1, arr2] = getDragDropData(type)
  const { index: rIndex } = findCard(rType, id);
  if (rIndex != -1) arr2.splice(rIndex, 1);
}
/// 大容器添加card
const addCard = (item: any, type: string, collect: any) => {
  /// 如果是同一边就不处理，划过时候处理了
  if (item.type == type) return;
  // 如果放置在卡片上，就放回这一边type,在card放置的时候处理
  if (!collect.isMyOver) return { type };
  const [arr1, arr2] = getDragDropData(type)
  /// 从拖拽那边删除这项card
  arr2.splice(findCard(item.type, item.id).index, 1)
  // 放置这边已有card 先删除再添加
  const rIndex = findCard(type, item.id).index
  if (rIndex != -1) arr1.splice(rIndex, 1)
  item.type = type
  arr1.push(item)

}
const [collect, drop] = useDrop(() => ({
  accept: ItemTypes.CARD, collect(monitor) {
    return {
      isMyOver: monitor.isOver({ shallow: true })
    }
  },
  drop(item: any, monitor): any {
    return addCard(item, 'left', collect.value);

    // if (item.type == 'left') return;
    // if (!collect.value.isMyOver) return { type: 'left' };
    // console.log(111111122222, cards.value)
    // rightCards.value.splice(findCard(item.type, item.id).index, 1)
    // const rIndex = findCard('left', item.id).index
    // if (rIndex != -1) cards.value.splice(rIndex, 1)
    // item.type = 'left'
    // cards.value.push(item)
    // console.log(1111113333333, cards.value)
  }
}))

const [rightCollect, rightDrop] = useDrop(() => ({
  accept: ItemTypes.CARD,
  collect(monitor) {
    return {
      isMyOver: monitor.isOver({ shallow: true })
    }
  },
  drop(item: any, monitor): any {
    return addCard(item, 'right', rightCollect.value)

    // if (item.type == 'right') return;
    // if (!rightCollect.value.isMyOver) return { type: 'right' };
    // cards.value.splice(findCard(item.type, item.id).index, 1)
    // const rIndex = findCard('right', item.id).index

    // if (rIndex != -1) rightCards.value.splice(rIndex, 1)
    // item.type = 'right'
    // rightCards.value.push(item)

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