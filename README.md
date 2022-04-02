# BMapGLLib路书扩展

本代码库forked form <a href="https://github.com/huiyan-fe/BMapGLLib">huiyan-fe/BMapGLLib</a>,仅基于路书功能模块<a href="https://github.com/huiyan-fe/BMapGLLib/tree/master/Lushu">BMapGLLib/Lushu/</a>的扩展。

路书
---------
<table>
<tr>
<td width='250'>
<img src='https://raw.githubusercontent.com/huiyan-fe/BMap-JavaScript-library/master/images/LuShu.png' width='250' />
</td>
<td width='600'>
百度地图路书类，实现Marker沿路线运动，对外开放。 用户可以在地图上自定义轨迹运动，支持暂停/停止功能，并可以自定义路过某个点的图片，文字介绍等。 <br/><br/>

</td>
</table>

**调用示例**

```
const setLuShu = function (map, opts) {
    const { LuShu } = BMapGLLib;
    const { start, end, carName, carType } = opts;
    let lushu;
    // 实例化一个驾车导航用来生成路线
    const driving = new BMapGL.DrivingRoute(map, {
        onSearchComplete: function (res) {
            if (driving.getStatus() === BMAP_STATUS_SUCCESS) {
                let plan = res.getPlan(0);
                let arrPois = [];
                for (let j = 0; j < plan.getNumRoutes(); j++) {
                    let route = plan.getRoute(j);
                    arrPois = arrPois.concat(route.getPath());
                }
                map.setViewport(arrPois);

                lushu = new LuShu(map, arrPois, {
                    infoWinClassName: 'car-info-window',//信息窗口容器类名，支持自定义样式，添加!important覆盖行内样式
                    defaultContent:
                        `<div style="padding:2px 4px;backgroud-color:#ccc;color:orange">
                        ${carName}<br/>${carType}
                    </div>
                    `, // "信息窗口文案"
                    autoView: false, // 是否开启自动视野调整，如果开启那么路书在运动过程中会根据视野自动调整
                    pathColor:'#20c997',//添加路线,当pathColor设置为"none"时不添加，默认颜色 '#00c0ff'
                    speed: 50,
                    icon: new BMapGL.Icon('/static/images/car-lushu.png', new BMapGL.Size(32, 32), { anchor: new BMapGL.Size(10, 10) }),
                    enableRotation: true, // 是否设置marker随着道路的走向进行旋转
                    completedClear:true,//运动完成后清除路书所创建的marker,line,overlay地图覆盖物
                    pass: p => { //运动过程中经过某点回调
                        console.log('经过点：', p)
                    },
                    complete: () => {//运动完成后回调
                        console.log(carName, '已抵达', end)
                    }
                });

                opts.lushu = lushu;
                lushu.start()
            }
        }
    });

    driving.search(start, end);
}
```


