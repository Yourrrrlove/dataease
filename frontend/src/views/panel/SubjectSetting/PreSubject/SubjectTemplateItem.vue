<template>
  <div class="subject-template">
    <div class="vertical-layout" @click.stop="subjectChange">
      <i v-if="subjectItem.type==='self'" class="el-icon-error" @click.stop="subjectDelete" />
      <!--      <i class="el-icon-edit" />-->
      <!-- 背景-->
      <div class="allBack" :style="customBackground" style="inset: 1px; position: absolute;" />
      <!-- 视图组件 背景-->
      <div style="inset: 17px 10px 10px; position: absolute;" :style="chartBackground" />
      <!-- 视图组件 主题-->
      <div style="inset: 20px 13px 15px; position: absolute;">
        <div style="position: absolute; inset: 0px 4px; width: auto; height: auto;">
          <!--柱形-->
          <div style="left: 0px; top: 11px; bottom: 0px; width: 3px; position: absolute;" :style="columnBackgroundLeft" />
          <div style="left: 5px; top: 6px; bottom: 0px; width: 3px; position: absolute; " :style="columnBackgroundMiddle" />
          <div style="left: 10px; top: 2px; bottom: 0px; width: 3px; position: absolute;" :style="columnBackgroundRight" />

          <!--柱形-->
          <div style="left: 20px; top: 11px; bottom: 0px; width: 3px; position: absolute;" :style="columnBackgroundLeft" />
          <div style="left: 25px; top: 2px; bottom: 0px; width: 3px; position: absolute; " :style="columnBackgroundMiddle" />
          <div style="left: 30px; top: 6px; bottom: 0px; width: 3px; position: absolute; " :style="columnBackgroundRight" />

          <!--柱形-->
          <div style="left: 40px; top: 2px; bottom: 0px; width: 3px; position: absolute;" :style="columnBackgroundLeft" />
          <div style="left: 45px; top: 6px; bottom: 0px; width: 3px; position: absolute; " :style="columnBackgroundMiddle" />
          <div style="left: 50px; top: 11px; bottom: 0px; width: 3px; position: absolute; " :style="columnBackgroundRight" />

          <!--柱形-->
          <div style="left: 60px; top: 6px; bottom: 0px; width: 3px; position: absolute;" :style="columnBackgroundLeft" />
          <div style="left: 65px; top: 11px; bottom: 0px; width: 3px; position: absolute; " :style="columnBackgroundMiddle" />
          <div style="left: 70px; top: 2px; bottom: 0px; width: 3px; position: absolute; " :style="columnBackgroundRight" />
        </div>
      </div>

      <!-- 表格表头颜色 -->
      <div style="left: 10px; right: 10px; top: 10px; height: 6px; position: absolute;" :style="tableHeadBackground" />

      <!-- 字体颜色 -->
      <div style="left: 14px; top: 10px; height: 6px; position: absolute; vertical-align: middle">
        <div style="width: 1px; height: 2px; position: relative; flex-shrink: 0; margin-top: 2px;margin-right: 1px; float: left;" :style="tableFontColor" />
        <div style="width: 1px; height: 2px; position: relative; flex-shrink: 0; margin-top: 2px;margin-right: 1px; float: left;" :style="tableFontColor" />
        <div style="width: 1px; height: 2px; position: relative; flex-shrink: 0; margin-top: 2px;margin-right: 1px; float: left;" :style="tableFontColor" />
        <div style="width: 1px; height: 2px; position: relative; flex-shrink: 0; margin-top: 2px;margin-right: 1px; float: left;" :style="tableFontColor" />
        <div style="width: 1px; height: 2px; position: relative; flex-shrink: 0; margin-top: 2px;margin-right: 1px; float: left;" :style="tableFontColor" />
        <div style="width: 1px; height: 2px; position: relative; flex-shrink: 0; margin-top: 2px;margin-right: 1px; float: left;" :style="tableFontColor" />
      </div>

    </div>
    <div style="position: absolute; left: 0px; right: 0px; bottom: 0px; height: 30px;" @dblclick="setEdit">
      <div style=" background-color:#f7f8fa;color:#3d4d66;font-size:12px;height: 30px; line-height: 30px; text-align: center; white-space: pre; text-overflow: ellipsis; margin-left: 1px; margin-right: 1px;overflow: hidden">
        <el-input
          v-if="canEdit"
          ref="nameInput"
          v-model="subjectItem.name"
          size="mini"
          @blur="loseFocus()"
        />
        <span v-if="!canEdit" style="margin-top: 8px" :title="subjectItem.name">{{ subjectItem.name }}</span>
      </div>
    </div>
  </div>
</template>

<script>
import { chartTransStr2Object } from '@/views/panel/panel'
import { mapState } from 'vuex'
import bus from '@/utils/bus'
import { saveOrUpdateSubject } from '@/api/panel/panel'

export default {
  name: 'StyleTemplateItem',
  props: {
    subjectItem: {
      type: Object,
      required: true
    }
  },
  data() {
    return {
      defaultSubject: {

      },
      subjectItemDetails: null,
      canEdit: false
    }
  },
  computed: {
    customBackground() {
      let style = {
        background: 'background: 0% 0% / cover rgb(255, 255, 255)'
      }
      if (this.subjectItemDetails) {
        if (this.subjectItemDetails.panel.backgroundType === 'image' && this.subjectItemDetails.panel.imageUrl) {
          style = {
            width: '100%',
            height: '100%',
            background: `url(${this.subjectItemDetails.panel.imageUrl}) no-repeat`,
            'background-size': '100% 100% !important'
          }
        } else {
          style = {
            width: '100%',
            height: '100%',
            background: this.subjectItemDetails.panel.color
          }
        }
      }
      return style
    },
    columnBackgroundLeft() {
      let style = {}
      if (this.subjectItemDetails) {
        style = {
          opacity: this.subjectItemDetails.chart.customAttr.color.alpha / 100,
          background: this.subjectItemDetails.chart.customAttr.color.colors[0]
        }
      }
      return style
    },
    columnBackgroundMiddle() {
      let style = {}
      if (this.subjectItemDetails) {
        style = {
          opacity: this.subjectItemDetails.chart.customAttr.color.alpha / 100,
          background: this.subjectItemDetails.chart.customAttr.color.colors[1]
        }
      }
      return style
    },
    columnBackgroundRight() {
      let style = {}
      if (this.subjectItemDetails) {
        style = {
          opacity: this.subjectItemDetails.chart.customAttr.color.alpha / 100,
          background: this.subjectItemDetails.chart.customAttr.color.colors[2]
        }
      }
      return style
    },
    tableHeadBackground() {
      let style = {}
      if (this.subjectItemDetails) {
        style = {
          opacity: this.subjectItemDetails.chart.customAttr.tableColor.alpha / 100,
          background: this.subjectItemDetails.chart.customAttr.tableColor.tableHeaderBgColor
        }
      }
      return style
    },
    tableFontColor() {
      let style = {}
      if (this.subjectItemDetails) {
        style = {
          background: this.subjectItemDetails.chart.customAttr.tableColor.tableFontColor
        }
      }
      return style
    },
    chartBackground() {
      let style = {}
      if (this.subjectItemDetails) {
        style = {
          background: this.subjectItemDetails.chart.customStyle.background.color,
          opacity: this.subjectItemDetails.chart.customStyle.background.alpha / 100
        }
      }
      return style
    },
    ...mapState([
      'canvasStyleData'
    ])
  },
  watch: {
    subjectItem: {
      handler(newVal, oldVla) {
        this.subjectItemDetails = chartTransStr2Object(JSON.parse(newVal.details), 'Y')
      },
      deep: true
    },
    resize() {
      this.drawEcharts()
    }
  },
  mounted() {
    this.subjectItemDetails = chartTransStr2Object(JSON.parse(this.subjectItem.details), 'Y')
  },
  methods: {
    subjectDelete() {
      this.$emit('subjectDelete', this.subjectItem.id)
      // this.$alert('是否删除主题：' + this.subjectItem.name + '？', '', {
      //   confirmButtonText: '确认',
      //   callback: (action) => {
      //     if (action === 'confirm') {
      //       this.$emit('subjectDelete', this.subjectItem.id)
      //     }
      //   }
      // })
    },
    subjectChange() {
      this.$store.commit('setCanvasStyle', JSON.parse(this.subjectItem.details))
      this.$store.commit('recordSnapshot', 'subjectChange')
      bus.$emit('onSubjectChange')
    },
    templateEdit() {
      this.$emit('templateEdit', this.template)
    },
    handleDelete() {
      // console.log('handleDelete')
    },
    // 双击事件
    setEdit() {
      if (this.subjectItem.type === 'self') {
        this.canEdit = true
      } else {
        this.$warning(this.$t('panel.subject_no_edit'))
      }
      // 将单元格变为输入框
      // // 聚焦到单元格
      setTimeout(() => {
        this.$refs['nameInput'].focus()
      }, 20)
    },
    // 当输入框失去焦点时不显示输入框
    loseFocus() {
      if (this.subjectItem.name && this.subjectItem.name.length > 0 && this.subjectItem.name.length < 20) {
        const request = {
          id: this.subjectItem.id,
          name: this.subjectItem.name
        }
        saveOrUpdateSubject(request).then(response => {
          this.$message({
            message: '保存成功',
            type: 'success',
            showClose: true
          })
          this.canEdit = false
        })
      } else {
        this.$warning(this.$t('panel.subject_name_not_null'))
      }
    },
    selectChange(callback, editCell) {
      if (!callback) {
        editCell.edit = false
      }
    }
  }
}
</script>

<style scoped>

  .allBack {
    background-size: 100% 100%!important;
  }
  .subject-template {
    width: 110px;
    height: 90px;
    position: relative;
    float: left;
    margin: 5px;
    display: inline-block;
  }

  .demonstration {
    display: block;
    text-align: center;
    margin: 10px auto;
    width: 150px;
    white-space:nowrap;
    overflow:hidden;
    text-overflow:ellipsis;
  }

  .vertical-layout {
    overflow: hidden;
    position: absolute;
    inset: 0px 0px 30px;
    width: 108px; height: 58px;
    margin: 0 auto;
    box-shadow: 0 0 2px 0 rgba(31,31,31,0.15), 0 1px 2px 0 rgba(31,31,31,0.15);
    border: solid 1px #fff;
    box-sizing: border-box;
    border-radius: 3px;
  }

  .vertical-layout:hover {
    border: solid 1px #4b8fdf;
    border-radius: 3px;
    color: deepskyblue;
    cursor: pointer;
  }

  .vertical-layout > i{
    float: right;
    color: gray;
    margin: 2px;
    position: relative;
  }

  .vertical-layout > i:hover {
    color: red;
  }

  .vertical-layout:hover > .el-icon-error {
    z-index: 10;
    display:block;
  }

  .vertical-layout:hover > .el-icon-edit {
    z-index: 10;
    display:block;
  }
  .vertical-layout>>>.el-icon-error {
    display:none
  }
  .vertical-layout>>>.el-icon-edit {
    display:none
  }

</style>
