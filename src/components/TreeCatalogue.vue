<template>
  <div class="treeCatalogue">
    <ul v-for="(treeNode, index) in treeData" :key="index">
      <div class="labal" @click="trriger(treeData, index, 'open')">
        <img class="arrow icon" src="../assets/A-right.svg" v-if="treeNode.children"
          :class="{ 'rotate': treeNode.isOpen }">
        <!-- 节点集合的名字 -->
        <!-- <span class="nodeText" v-show="!treeNode.children">{{ treeNode.labal }}</span> -->
        <input ref="inputRef" type="text" class="inputArea"
          @keydown.enter="saveChange(treeData, index)" @click.stop :placeholder="treeNode.labal" @blur="recover(index)">
        <img class="shudian" src="../assets/shudian.svg" @click.stop="trriger(treeData, index, 'panel')">
        <!-- 功能操作面板 -->
        <div :class="{ 'active-funcsPanel': treeNode.panel }" class="funcsPanel" @mouseenter="showPanelOrNot(treeData, index, 'mouseenter')" @mouseleave="showPanelOrNot(treeData, index, 'mouseleave')">
          <span class="funcItem" v-if="treeData[index].children" @click.stop="operateFunc(treeData, index, 'onCreateRoot')">新增目录</span>
          <span class="funcItem" v-if="treeData[index].children" @click.stop="operateFunc(treeData, index, 'onCreateBase')">新增单点</span>
          <span class="funcItem" @click.stop="operateFunc(treeData, index, 'onRemove')">删除节点</span>
          <span class="funcItem" @click.stop="operateFunc(treeData, index, 'onRename')">节点修改</span>
        </div>
      </div>
      <!-- 节点下的子节点  v-if="treeNode.isOpen" -->
      <div ref="listBar"class="listBar" :class="{ 'active-listBar': treeNode.isOpen }" style="width: calc(100% - 25px); margin-left: 25px;">
        <TreeCatalogue v-if="treeNode.children != []" :treeData="treeNode.children" style="width: 100%;" ref="treeCatalogue"/>
      </div>
    </ul>
  </div>

</template>

<script>
import TreeCatalogue from '@/components/TreeCatalogue';
export default {
  name: 'TreeCatalogue',
  data() {
    return {
      timer: null,
    }
  },
  props: {
    treeData: {
      type: Array,
      default: () => []
    }
  },
  components: {
    TreeCatalogue
  },
  methods: {
    /***  
     * @param { Array } - treeData 节点数组
     * @param { Number } - index 节点索引
     * @param { string }  - type 操作类型   onCreateRoot | onCreateBase ｜ onRemove ｜ onRename     
     * @return { void }
     */
     operateFunc(treeData, index, type) {
      const operateResolver = {
        "onCreateRoot": (treeData, index) => {
          treeData[index].children.push({
            labal: '新增目录',
            isOpen: false,
            panel: false,
            children: []
          });
          treeData[index].isOpen = true;
        },
        "onCreateBase": (treeData, index) => {
          treeData[index].children.push({
            labal: '新增单点',
            isOpen: false,
            panel: false,
            children: null,
          });
          treeData[index].isOpen = true;
        },
        "onRemove": (treeData, index) => {
          treeData.splice(index, 1);
        },
        "onRename": (treeData, index) => {
          this.$refs.inputRef[index].focus()
        }
      }
      treeData[index].panel = !treeData[index].panel;
      operateResolver[type](treeData, index)
      clearTimeout(this.timer); 
    },
    /***  
     * @param { Array } - treeData 节点数组
     * @param { Number } - index 节点索引
     * @param { string }  - type 操作类型   onCreateRoot | mouseleave        
     * @return { void }
     */
    showPanelOrNot(treeData, index, type) {
      if(type ==  'mouseenter') {
        treeData[index].panel = true;
      }else {
        this.timer = setTimeout(() => {
        treeData[index].panel = false;
      }, 200)
      }
    },
    // input按下回车后保存修改
    saveChange(treeData, index) {
      treeData[index].labal = this.$refs.inputRef[index].value;
      this.$refs.inputRef[index].blur()
    },
    // input失去焦点时恢复原本状态
    recover(index) {
      this.$refs.inputRef[index].value = ''
    },
    /***
     * 开启关闭函数
     * @param { Array } - treeData 节点数组
     * @param { Number } - index 节点索引
     * @param { string }  - type 操作类型  panel | isOpen
     * @return { void }
     */
    trriger(treeData, index, type) {
      if(type == 'panel') {
        treeData[index].panel = !treeData[index].panel;
      }else {
        treeData[index].isOpen = !treeData[index].isOpen;
      }
    },
  }
}
</script>

<style scoped lang="less">

.treeCatalogue {
  border-radius: 5px;
  background-color: #fff;
  box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
  ul {
    min-height: 50px;

    .labal {
      position: relative;
      height: 50px;
      cursor: pointer;
      transition: .3s;
      display: flex;
      align-items: center;
      justify-content: flex-start;
      border-radius: 5px;

      &:hover {
        background-color: #f7f5f5;

        .shudian {
          opacity: 1;
        }
      }

      .inputArea {
        caret-color: blue;
        height: 30px;
        width: 70%;
        background: none;
        border: none;
        margin: 0;
        padding-left: 1vw;
        outline: none;
        font-size: 16px;

        &::placeholder {
          line-height: normal;
          color: black;
          font-size: 16px;
        }

        /* 移除iOS默认的外观 */
      }

      .funcsPanel {
        pointer-events: none;
        transition: .5s;
        opacity: 0;
        border: 1px solid yellowgreen;
        border-radius: 5px;
        background-color: #fff;
        box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
        width: 100px;
        display: flex;
        flex-direction: column;
        position: absolute;
        top: 0;
        left: 100%;

        span {
          width: 100%;
          height: 25px;
          line-height: 25px;
          border-radius: 3px;
          transition: .3s;
          font-weight: 500;
          font-size: 14px;

          &:hover {
            background-color: #f7f5f5;
          }
        }
      }

      .active-funcsPanel {
        pointer-events: all;
        opacity: 1;
      }

      .nodeText {
        padding-left: 1vw;
      }

      .arrow {
        margin-left: 1.5vw;
        transition: .2s;
        width: 1.5vw;
        height: 1.5vw;
      }

      .rotate {
        transform: rotate(90deg);
      }

      .shudian {
        transition: .3s;
        opacity: 0;
        width: 2vw;
        height: 2vw;
        position: absolute;
        right: 3%;
      }
    }

    .listBar {
      height: 0;
      overflow: hidden;
      transition: .2s;
      width: 300vw;
    }
    .active-listBar {
      height: auto;
      overflow: visible;
    }
  }
}
</style>
