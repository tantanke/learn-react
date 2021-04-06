# redux-actions

该库用于简化action-types、action-creator以及reducer 

## createAction(s)
createAction
该函数用于帮助你创建一个action创建函数（action creator）
在创建的时候自动绑定type，后续传入参数绑定payload
```js
export const increment = createAction('INCREMENT');
export const decrement = createAction('DECREMENT');

increment(); // { type: 'INCREMENT' }
decrement(); // { type: 'DECREMENT' }
increment(10); // { type: 'INCREMENT', payload: 10 }
decrement([1, 42]); // { type: 'DECREMENT', payload: [1, 42] }
```
createActions
该函数用于帮助你创建多个action创建函数

## handleAction(s)

handleAction
简化针对单个action类型的reducer处理，当它匹配到对应的action类型后，会执行对应的函数

handleActions
简化针对多个action类型的reducre处理

## combineActions
配合createActions和handleActions两个函数，用于处理多个action-type对应同一个reducer处理函数。