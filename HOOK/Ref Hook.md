# Ref Hook

useRef(value:可选)

1. 一个参数：默认值
2. 返回一个固定的对象
```js
{
    current:vaule
}
```

```JS
function TextInputWithFocusButton() {
  const inputEl = useRef(null); // 可以书写默认值
  const onButtonClick = () => {
    // `current` 指向已挂载到 DOM 上的文本输入元素
    inputEl.current.focus();
  };
  return (
    <>
      <input ref={inputEl} type="text" />
      <button onClick={onButtonClick}>Focus the input</button>
    </>
  );
}
```

意思就是说对于同一个函数节点，不管调用了多少次这个函数，使用```useRef```得到的对象都是同一个
