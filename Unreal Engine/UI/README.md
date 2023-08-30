## 重难点
- [ ] 锚点

## 画布(Canvas)
- 画布的主要作用就是为了调整控件的位置，超出画布之外的空间不渲染。
- 画布本身没有位置，只能通过设置分辨率的方式设置大小
- 在UE4中，画布默认存在于层级中，虚幻5取消了画布默认，需要我们自己添加，增强了灵活性（有些时候是不需要画布的）

## 图像控件(Image)
- 主要用于渲染图片，也可以引用材质。
- 默认为纯色。
- 当为纯色时，不需要引用资源，只需要设置颜色即可
- 大小到内容(size to content)：让图像保持原有大小
- ZOrder：图像显示层级，大的显示在上面

## 锚点（Anchors）
- 用于适配UI控件的相对位置，通常包含三种
  - 九点适配
  - 单轴适配
  - 父节点尺寸适配：父节点放大，子节点也放大。缩小也一样。
 
## 文本控件（Text）
- 用于渲染文本
- 默认字体