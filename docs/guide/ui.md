# 界面类

本章中的内容主要为 class 索引，以及 UI 实现上一些关于关键 UI 颜色、大小的修改方式。

由于作者一直在优化其代码，故一些注解有其时效性。

## Bus（母线） 

Bus 为网络中的基础节点，相当于母线。在Bus上，可以添加发电机，负载，接地等等

### /Gui/GridEditorWidget.py/BusGraphicItem/__init__

Bus 对象的初始化方法。其中可修改绘图板内的 Bus 的默认正方形图标为圆形。

```python
# bus 默认为长条状的长方形，宽180,长20，修改数字即可修改长宽
self.min_w = 180.0
self.min_h = 20.0

# 此处为 bus 的实现方法，默认为矩形
self.tile = QGraphicsRectItem(0, 0, self.min_h, self.min_h, self)
self.tile.setOpacity(0.7)

# 修改为圆形
self.tile = QGraphicsEllipseItem(0, 0, 100, 100, self)
self.tile.setOpacity(0.7)

# 此处为标签，可进行字体颜色和内容的设置
self.label = QGraphicsTextItem(bus.name, self)
# self.label.setDefaultTextColor(QtCore.Qt.white)
self.label.setDefaultTextColor(QtCore.Qt.black)

```

### /Gui/GridEditorWidget.py/GridEditor/__init__

将左侧列表中的小Bus图标，可将正方形改为圆形。

```python
self.libItems.append(QStandardItem(object_factory.get_box(), 'Bus'))
# get_box 修改为 get_circle
self.libItems.append(QStandardItem(object_factory.get_circle(), 'Bus'))
```

### /Gui/GridEditorWidget/ObjectFactory/get_circle

可修改左侧列表中的小Bus图标的圆形的参数，例如加黑边，更换颜色。

```python
# 修改刷子的颜色以及圆形的大小
painter.setBrush(Qt.black)
painter.drawEllipse(5, 5, 30, 30)
painter.setBrush(Qt.white)
painter.drawEllipse(8, 8, 24, 24)
```

### /gui/GridEditorWidget/BusGraphicItem/contextMenuEvent

修改在Bus上点击右键后，所展示的内容

```python
# addAction 中的参数为展示的文字
pe = menu.addAction('Enable/Disable')
# connect 用来关联点击后的方法
pe.triggered.connect(self.enable_disable_toggle)
```

## Load（负载）

Load（负载）可以附加在 Bus 上，默认为简单的黑色空心三角形

### /Gui/GridEditorWidget.py/LoadGraphicItem/__init__

可修改 Load 的大小和颜色

```python
triangle = Polygon(self)
# 修改多边形形状
triangle.setPolygon(QPolygonF([QPointF(0, 0), QPointF(self.w, 0), QPointF(self.w/2, self.h)]))
# 修改颜色
triangle.setPen(QPen(Qt.black, 2))
```

## Shunt (分流器)

Shunt(分流器) 可以附加在 Bus 上，默认为黑色的电容器接地状。

### /Gui/GridEditorWidget.py/ShuntGraphicItem/__init__

可修改 shunt 的颜色

```python
# 默认为黑色
pen = QPen(self.color, self.width, self.style)
# 修改成蓝色
pen = QPen(Qt.blue, self.width, self.style)
```

## Controlled Generator（受控发电机）

Controlled Generator（受控发电机）可以附加在 Bus 上，默认为黑色圆圈内带字母'G'

### /Gui/GridEditorWidget.py/ControlledGeneratorGraphicItem/__init__

```python
# 默认内部字母为 G
self.label = QGraphicsTextItem('G',parent=self.glyph)
# 修改字母为 CG
self.label = QGraphicsTextItem('CG',parent=self.glyph)

# 默认字体颜色为黑色
self.label.setDefaultTextColor(self.color)
# 修改字体颜色为红色
self.label.setDefaultTextColor(Qt.red)

```

## Static Generator（静止发电机）

Static Generator（静止发电机）可以附加在 Bus 上，默认为黑色正方形内带字母'S',其内部方法和 Controlled Generator 基本一致，故不再赘述。

### /Gui/GridEditorWidget.py/StaticGeneratorGraphicItem/__init__


## Battery（电池）

Battery（电池）可以附加在 Bus 上，默认为黑色正方形内带字母'B'，其内部方法和 Controlled Generator 基本一致，故不再赘述。

### /Gui/GridEditorWidget.py/BatteryGraphicItem/__init__
