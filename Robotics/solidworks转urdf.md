# solidworks转urdf

以clicbot的四足机器人为例子，进行了solidworks导出urdf机器人模型，然后导入unity

首先是先打开clicbot的stp关节文件，这些关节都是高度定义过的装配体，无法更改，所以直接用就可以，通过配合进行装配。

导出urdf需要去ROS官网下个包，可以自动导出。然后需要自己选link然后提前设置点，坐标系，和基准轴，这很重要。

clicbot的joint很特殊，自己本身还有转动关节，所以需要对他进行分开，仔细看过机械结构导出urdf，这里有个很重要的地方，就是我第一次导出的时候发现有些零件缺失，其实是因为没有选中到子零件那里，直接省事选的父零件，所以不会生成相关的零件。