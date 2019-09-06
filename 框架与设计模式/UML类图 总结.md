## UML类图

##### 1. 继承
![](https://design-patterns.readthedocs.io/zh_CN/latest/_images/uml_generalize.jpg)

##### 2. 实现
![](https://design-patterns.readthedocs.io/zh_CN/latest/_images/uml_realize.jpg)

##### 3. 关联

表示A知道B，但B不知道A。强关系。

![](https://design-patterns.readthedocs.io/zh_CN/latest/_images/uml_association.jpg)

关联是有方向的，可以有单向，双向。

关联对象通常是以成员变量的形式实现的。

##### 4. 依赖

如下图表示A依赖于B；他描述一个对象在运行期间会用到另一个对象的关系，弱关系。

![](https://design-patterns.readthedocs.io/zh_CN/latest/_images/uml_dependency.jpg)

依赖也有方向，双向依赖是一种非常糟糕的结构，我们总是应该保持单向依赖，杜绝双向依赖的产生；

依赖关系体现为类构造方法及类方法的传入参数。

##### 5. 聚合

A聚合到B上，或者说B由A组成

![](https://design-patterns.readthedocs.io/zh_CN/latest/_images/uml_aggregation.jpg)

聚合关系:表示整体与部分的关系。例如一个部门由多个员工组成。

整体和部分不是强依赖的，即使整体不存在了，部分仍然存在。例如部门撤销了，人员不会消失，他们依然存在。

##### 6. 组合

A组成B，或者B由A组成

![](https://design-patterns.readthedocs.io/zh_CN/latest/_images/uml_composition.jpg)

组合关系：表示整体与部分的关系；比如公司由多个部门组成；

组合关系是一种强依赖的特殊聚合关系，如果整体不存在了，则部分也不存在了。例如，公司不存在了，部门也将不存在了。

