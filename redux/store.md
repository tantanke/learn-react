# store

Store 就是把它们联系到一起的对象。Store 有以下职责：

Store：用于保存数据

通过createStore方法创建的对象。

该对象的成员：

1. dispatch：分发一个action
2. getState：得到仓库中当前的状态
3. replaceReducer：替换掉当前的reducer
4. subscribe：注册一个监听器，监听器是一个无参函数，该分发一个action之后，会运行注册的监听器。该函数会返回一个函数，用于取消监听