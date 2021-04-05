懂得都懂

# transition

```js
function App() {
  const [inProp, setInProp] = useState(false);
  return (
    <div>
      <Transition in={inProp} timeout={500}>
        {state => (
          // ...
        )}
      </Transition>
      <button onClick={() => setInProp(true)}>
        Click to Enter
      </button>
    </div>
  );
}
```

四种状态
* 'entering'
* 'entered'
* 'exiting'
* 'exited'

通过```state```来控制属性