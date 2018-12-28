# ECHARTS

## 配置项

### legend(图例)

图例的数据数组，每一项代表一个系列的`name`

### tooltip(提示框)

cross 指示器可以在这里配置
formatter 是显示内容的格式化函数

### toolbox(工具)

配置工具项，比如保存图片

### grid(整个图表的样式)

宽高，边框等

### series

每个 serie 通过 type 来决定图表类型

- showSybmbol 点显示出来
- symbol 点位的形状

### x/yAxis

- splitLine y 轴则是等间距垂直的分割线，通过 show 控制显示
- axisPointer 在两个轴显示的内容可以在这里格式化
- boundaryGap 坐标轴两侧留白
- splitNumber 坐标轴分段数量
- min/max 坐标轴最大最小的刻度
- nameGap 坐标轴名称与轴线之间距离
- axisLabel

#### line

- smooth 控制曲线平滑

### 自定义图表

```js
var option = {
  ...,
  series: [{
    type: 'custom',
    renderItem: function (params, api) {
      // 对于 data 中的每个 dataItem，都会调用这个 renderItem 函数。
      // （但是注意，并不一定是按照 data 的顺序调用）

      // 这里进行一些处理，例如，坐标转换。
      // 这里使用 api.value(0) 取出当前 dataItem 中第一个维度的数值。
      var categoryIndex = api.value(0);
      // 这里使用 api.coord(...) 将数值在当前坐标系中转换成为屏幕上的点的像素值。
      var startPoint = api.coord([api.value(1), categoryIndex]);
      var endPoint = api.coord([api.value(2), categoryIndex]);
      // 这里使用 api.size(...) 获得 Y 轴上数值范围为 1 的一段所对应的像素长度。
      var height = api.size([0, 1])[1] * 0.6;

      // 这里返回为这个 dataItem 构建的图形元素定义。
      return {
          // 表示这个图形元素是矩形。还可以是 'circle', 'sector', 'polygon' 等等。
          type: 'rect',
          // shape 属性描述了这个矩形的像素位置和大小。
          // 其中特殊得用到了 echarts.graphic.clipRectByRect，意思是，
          // 如果矩形超出了当前坐标系的包围盒，则剪裁这个矩形。
          shape: echarts.graphic.clipRectByRect({
              // 矩形的位置和大小。
              x: startPoint[0],
              y: startPoint[1] - height / 2,
              width: endPoint[0] - startPoint[0],
              height: height
          }, {
              // 当前坐标系的包围盒。
              x: params.coordSys.x,
              y: params.coordSys.y,
              width: params.coordSys.width,
              height: params.coordSys.height
          }),
          // 用 api.style(...) 得到默认的样式设置。这个样式设置包含了
          // option 中 itemStyle 的配置和视觉映射得到的颜色。
          style: api.style()
      };
    },
    data: [
      [12, 44, 55, 60], // 这是第一个 dataItem
      [53, 31, 21, 56], // 这是第二个 dataItem
      [71, 33, 10, 20], // 这是第三个 dataItem
      ...
    ]
  }]
}
```
