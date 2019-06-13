# handsontable 表格插件

## 参考链接

- [handsontable官网文档](https://docs.handsontable.com/6.0.1/tutorial-introduction.html)

## 安装(version 6.0.0+)

- npm install handsontable @handsontable/vue

## api

```html

<template>
  <div>
      <div id="hotTable" class="hotTable">
           <HotTable :root="root" ref="testHot" :settings="hotSettings"></HotTable>
      </div>
  </div>
</template>

<script>
import Vue from 'vue'
import HotTable from '@handsontable/vue';

export default {
    data: function () {
        return {
            root: 'test-hot',
            hotSettings: {
                data: [        //数据可以是二维数组，也可以是数组对象
                    ['20080101', 10, 11, 12, 13,true],
                    ['20090101', 20, 11, 14, 13,true],
                    ['20010101', 30, 15, 12, 13,true],
                    ['20010101', 32, 213, 21, 312,true],
                    ['20010201', 32, 213, 21, 312,true],
                    ['20010301', 32, 213, 21, 312,true],
                    ['20010401', 32, 213, 21, 312,true],
                    ['20010501', 32, 213, 21, 312,true],
                    ['20010601', 32, 213, 21, 312,true]
                ],
                startRows: 11,//行列范围
                startCols: 6,
                minRows: 5,  //最小行列
                minCols: 5,
                maxRows: 20,  //最大行列
                maxCols: 20,
                rowHeaders: true,//行表头，可以使布尔值（行序号），可以使字符串（左侧行表头相同显示内容，可以解析html），也可以是数组（左侧行表头单独显示内容）。
                colHeaders:   [ '题目', 'A选项', 'B选项', 'C选项', 'D选项','答案'],//自定义列表头or 布尔值
                minSpareCols: 2, //列留白
                minSpareRows: 2,//行留白
                currentRowClassName: 'currentRow', //为选中行添加类名，可以更改样式
                currentColClassName: 'currentCol',//为选中列添加类名
                autoWrapRow: true, //自动换行
                contextMenu: {   //自定义右键菜单，可汉化，默认布尔值
                    items: {
                    "row_above": {
                        name:'上方插入一行'
                    },
                    "row_below": {
                        name:'下方插入一行'
                    },
                    "col_left": {
                        name:'左方插入列'
                    },
                    "col_right": {
                        name:'右方插入列'
                    },
                    "hsep1": "---------", //提供分隔线
                    "remove_row": {
                        name: '删除行',
                    },
                    "remove_col": {
                        name: '删除列',
                    },
                    "make_read_only": {
                        name: '只读',
                    },
                    "borders": {
                        name: '表格线',
                    },
                    "commentsAddEdit": {
                        name: '添加备注',
                    },
                    "commentsRemove": {
                        name: '取消备注',
                    },
                    "freeze_column": {
                        name: '固定列',
                    },
                    "unfreeze_column": {
                        name: '取消列固定',
                    },
                    "hsep2": "---------",
                            }
                },//右键效果
                fillHandle: true, //选中拖拽复制 possible values: true, false, "horizontal", "vertical"
                fixedColumnsLeft: 0,//固定左边列数
                fixedRowsTop: 0,//固定上边列数
                mergeCells: [   //合并
                    {row: 1, col: 1, rowspan: 3, colspan: 3},  //指定合并，从（1,1）开始行3列3合并成一格
                    {row: 3, col: 4, rowspan: 2, colspan: 2}
                ],
                columns: [     //添加每一列的数据类型和一些配置
                    {
                        //data: 'title', 此设置标识，数组对象数据格式时的列字段
                        type: 'date',   //时间格式
                        dateFormat: 'YYYYMMDD',
                        correctFormat: true,
                        defaultDate: '19000101'
                    },
                    {
                        type: 'dropdown', //下拉选择
                        source: ['BMW', 'Chrysler', 'Nissan', 'Suzuki', 'Toyota', 'Volvo'],
                        strict: false   //是否严格匹配
                    },
                        {type: 'numeric'},  //数值
                        {type: 'numeric',
                        readOnly: true  //设置只读
                    },
                    {
                        type: 'numeric',
                        format: '$ 0,0.00'
                    },  //指定的数据格式
                    {
                        type: 'checkbox'
                    },  //多选框
                ],
                afterChange: function (changes, source) { //数据改变时触发此方法
                    console.log(this.getData())
                },
                manualColumnFreeze: true, //手动固定列
                manualColumnMove: true, //手动移动列
                manualRowMove: true,   //手动移动行
                manualColumnResize: true,//手工更改列距
                manualRowResize: true,//手动更改行距
                comments: true, //添加注释
                cell: [
                    {row: 1, col: 1, comment: {value: 'this is test'}},
                ],
                customBorders:[],//添加边框
                columnSorting: true,//排序
                stretchH: 'all',//根据宽度横向扩展，last:只扩展最后一列，none：默认不扩展
            }
        };
    },
    name: 'handsonTable',
    components: {
        HotTable
    },
    methods: {
        saveData (){
            var examData = this.$refs.testHot.table.getData(); //这里要注意，如果使用this.hotSettings.data 保存表格数据，拖拽完以后数据的顺序将不会更新，因此使用 this.$refs.testHot.table.getData(); 来获取数据，获取的数据格式为二维数组。
            console.log(examData );
        }
    }
}
</script>

```

## handsontable 插件事件api

- afterChange (changes: Array, source: String)：1个或多个单元格的值被改变后调用
  - changes：是一个2维数组包含row，prop，oldVal，newVal 4个属性。
  - source：其值为一个字符串，值可以为：alter，empty，populateFromArray，loadData，autofill，paste
- beforeChange (changes: Array, source: String)：开始改变单元格前被调用
  - changes：是一个2维数组，包括[row，prop，oldVal，newVal]这4个公共属性列
  - source是被改变的资源的名称
- afterCellMetaReset ()：重置单元格后调用
- afterColumnMove (oldIndex: Number, newIndex: Number)：列顺序被移动后触发
- afterRowMove (oldIndex: Number, newIndex: Number)：行被移动后调用
- afterRowResize (col: Number, size: Number)：行高改变后调用
- afterRemoveCol (index: Number, amount: Number)：当一列或多列被移动后调用
  - index为开始移动的列的索引
  - amount为移动的列的总数量
- afterRemoveRow (index: Number, amount: Number)：当一行或多行被移动后调用
  - index为被移动的行的索引
  - amount为被移动的行的总数量
- beforeRemoveCol (index: Number, amount: Number)：一列或多列被移动前调用
- beforeRemoveRow (index: Number, amount: Number)：一行或多行被移动前被调用
- afterColumnSort (column: Number, order: Boolean)：列排序后调用
- beforeColumnSort (column: Number, order: Boolean)：列排序前被调用
  - order：值为true时为升序，false时为降序
- afterCreateCol (index: Number, amount: Number)：添加行后被调用
  - index：新列的索引
  - amount：新列的数目
- afterCreateRow (index: Number, amount: Number)：添加行后被调用
  - index：新行的索引
  - amount：新行的数目
- afterGetCellMeta (row: Number, col: Number, cellProperties: Object)：获取单元格的配置信息后被调用
- beforeGetCellMeta (row: Number, col: Number, cellProperties: Object)：获取单元格属性前被调用
- afterSetCellMeta(row: Number, col: Number, key: String, value: *)：单元格样式被改变后调用
  - cellProperties是一个单元格的渲染对象
  - key是改变样式的方式，例如合并单元格（merge），水平对齐（align）等。
- afterGetColHeader (col: Number, TH: DOM Node)：获取列头信息后被调用，TH是行头节点
- afterGetColWidth (col: Number, response: Object)：获取列宽后被调用
- afterColumnResize (col: Number, size: Number)：列宽度被手动修改后调用
- afterCopyLimit (s-electedRowsCount: Number, s-electedColsCount: Number,copyRowsLimit: Number, copyColsLimit: Number)，当 copyRowsLimit 或者 copyColumnsLimit实现时被调用
- afterDestroy ()：销毁Handsontable实例后被调用
- afterInit ()：Handsontable实例被初始化后调用
- beforeInit ()：Handsontable实例被初始化前调用
- beforeInitWalkontable()：Walkontable实例被初始化前调用
- afterLoadData ()：新的数据被加载到数据资源后被调用afterOnCellCornerMouseDown (event)：鼠标点击单元格边角后被调用
- afterOnCellMouseDown (event: Object, coords: Object, TD: Object)：点击单元格或行头/列头后被调用
  - 注意：点击行头或列头后索引的坐标为负数。例如点击列头单元格(0,0)，则调用后的坐标为(0,-1)。
- afterOnCellMouseOver (event: Object, coords: Object, TD: Object)：鼠标停悬在单元格或行头/列头后调用
  - 注意：点击行头或列头后索引的坐标为负数。例如点击行头单元格(0,0)，则调用后的坐标为(0,-1)。
- afterRender (isForced: Boolean)：渲染表格后被调用
  - isForced：当其值为true表示是通过改变配置或数据引起的渲染，当值为false时表示通过滚动或移动、选中引起的渲染
- beforeRender (isForced: Boolean)：渲染前被调用
- afterRenderer (TD: Object, row: Number, col: Number, prop: String, value: String, cellProperties: Object)：手动渲染后调用
- beforeChangeRender ()：渲染被改变前调用
- afterDes-elect ()：当前单元格被取消选中时调用
- afterS-election (r: Number, c: Number, r2: Number, c2: Number)：当一个或多个单元格被选中后调用
  - r：选中的单元格起始行
  - r2：选中单元格的终止行
  - c：选中的单元格的起始列
  - c2：选中的单元格的终止列
- afterS-electionByProp (r: Number, p: String, r2: Number, p2: String)：通过属性名选中单元格后调用
- afterS-electionEnd (r: Number, c: Number, r2: Number, c2: Number)：选中单元格鼠标抬起后调用
- afterS-electionEndByProp (r: Number, p: String, r2: Number, p2: String)：通过属性选中单元格鼠标抬起后调用
- afterUpdateSettings ()：配置参数配修改后调用
- afterValidate (isValid: Boolean, value: Mixed, row: Number, prop: String,source: String) 当有验证器的时候调用验证器时被调用，验证结果作为第一个参数。
- beforeValidate (value: Mixed, row: Number, prop: String, source: String)：验证器被调用前调用该事件
- beforeAutofill (start: Object, end: Object, data: Array)：开始自动填充前调动
  - start：是一个第一个填充的单元格对象，例如:{row:4,col:3}
  - end：是最后一个填充的单元格对象，例如：{row:7,col:5}
  - data:是一个2维数组
- beforeKeyDown (event: Object)：按键按下前被调用
- beforeSet (var: Object)：单个配置值被设置前调用
- beforeSetRangeEnd(coords: Array)：设置范围结束前被调用
  - coords：是范围坐标
- modifyCol(col: Number)：列被修改时调用
- modifyRow( row: Number)：行被修改时调用
- modifyColWidth (width: Number, col: Number)：列宽被修改时调用
- modifyRowHeight (height: Number, row: Number)：行高被修改时调用