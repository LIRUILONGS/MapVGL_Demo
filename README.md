# MapVGL_Demo

## 基于MapVGL的地理信息维度数据增长可视化

`MapVGL`，是一款基于`WebGL`的地理信息可视化库，可以用来展示大量基于3D的地理信息点线面数据。设计初衷主要是为了解决`大数据量的三维地理数据展示`问题及一些炫酷的三维效果。

`MapVGL`通过地理信息数据生成`可视化图层`，然后将这些`图层`添加在`地图上层进行管理`。在这个过程中，它使用了`WebGL`技术在`canvas`中绘制图形，从而有效地提高了页面性能。此外，MapVGL还提供了大量的模型，包括点图层、线图层、聚合类图层等，每种模型都提供了示例，可以直接运行查看效果。

需要注意的是，地理信息数据格式是规定好的，必须具有`geometry`字段来定义坐标信息，同时可通过`properties`字段添加附件信息。而`geometry`字段数据格式使用的是`GeoJSON`的规范。

官方Demo: [https://mapv.baidu.com/gl/examples/](https://mapv.baidu.com/gl/examples/)

官网文档：[https://mapv.baidu.com/gl/docs/index.html](https://mapv.baidu.com/gl/docs/index.html)

下面我们看一个通过 `MapVGL` 来实现的数据可视化的Demo，某公司全国网点建设历年增长可视化

效果图

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/d665a6add4f4442794c4d2c0bc0ae9c1.png)

实现相对简单，渲染地图，然后添加图层，通过 `setInterval` 方法对图层进行重复渲染，填充数据为当前数据和增量数据

### 渲染地图

使用百度地图的`WebGL`版本（`BMapGL`）来初始化一个地图实例

```js
 var map = new BMapGL.Map('map_container', {
                restrictCenter: false,
                style: { styleJson: options.style || darkStyle }
            });
```

使用`mapvgl`来创建一个视图（View）对象，该对象与先前创建的地图（map）相关联。

```js
 var view = new mapvgl.View({
            map: map
        });           
```

### 准备图层数据

```js
  // ，绘制带波纹扩散的圆图层数据 
        var data = cities.map((city, index) => {
            var cityCenter = mapv.utilCityCenter.getCenterByCityName(city);
            return {
                geometry: {
                    type: 'Point',
                    coordinates: [cityCenter.lng, cityCenter.lat]
                },
                color: colors[2],
                // 圆的半径
                size: 5
            };
        });

        // 波纹动画图层数据 
        let datas = cities.map((city, index) => {
            let cityCenter = mapv.utilCityCenter.getCenterByCityName(city);
            return {
                geometry: {
                    type: 'Point',
                    coordinates: [cityCenter.lng, cityCenter.lat]
                },

            };
        });
        ................
        ............
        // 柱状体图层数据
        // 飞线绘制

```

周期绘制

```js
        let i = 1
        setInterval(() => {

            var waveLayer = new mapvgl.CircleLayer({
                // 绘制带波纹扩散的圆
                type: 'wave',
                // 扩散半径，支持直接设置和回调两种形式
                radius: r => 3 * r,
                // 周期影响扩散速度，越小越快
                duration: 1 / 3,
                // 拖尾影响波纹数，越大越多
                trail: 3
            });
            view.addLayer(waveLayer);
            waveLayer.setData(data.slice(0, i));

            var rippleLayer = new mapvgl.RippleLayer({
                size: 500000,
                unit: 'm',
                color: '#0ff',
                enablePicked: true,
                onClick: (e) => { // 点击事件
                    console.log(e);
                },
            });
            view.addLayer(rippleLayer);
            rippleLayer.setData(datas.slice(0, i));
            .......
            ..........
            i = i + 1;
            if (i == cities.length + 2) {
                i = 1
                view.removeAllLayers()
            }
        },3000)

```



## 博文部分内容参考

© 文中涉及参考链接内容版权归原作者所有，如有侵权请告知，这是一个开源项目，如果你认可它，不要吝啬星星哦 :)

***




github 地址：[https://github.com/huiyan-fe/mapv](https://github.com/huiyan-fe/mapv)

官方Demo: [https://mapv.baidu.com/gl/examples/](https://mapv.baidu.com/gl/examples/)

官网文档：[https://mapv.baidu.com/gl/docs/index.html](https://mapv.baidu.com/gl/docs/index.html)


***

© 2018-2023 <liruilonger@gmail.com>, All rights reserved. 保持署名-非商用-相同方式共享(CC BY-NC-SA 4.0)
