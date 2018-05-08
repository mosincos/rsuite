
### 异步

<!--start-code-->
```js



function asynGetData(node, callback) {
  const findNode = findNodeOfTree(province, item => (node.id === item.id));
  const children = get(findNode, 'children');
  const data = children.map((item) => {
    if (item.children) {
      return {
        ...item,
        children: []
      };
    }
    return item;
  });

  setTimeout(() => {
    callback && callback(data);
  }, 1000);
}

class AsynExample extends React.Component {

  constructor(props) {
    super(props);
    this.state = {
      data: province.map(item => ({ ...item, children: [] }))
    };
  }

  render() {
    return (
      <div className="example-item">
        <Cascader
          data={this.state.data}
          valueKey="id"
          labelKey="name"
          placeholder="请选择"
          value={this.state.value}
          renderMenu={(children, menu, parentNode) => {

            if (children.length === 0) {
              return (
                <p style={{ padding: 4, color: '#999', textAlign: 'center' }}>
                  <IconFont icon="spinner" spin /> 加载中...
                </p>
              );
            }
            return menu;

          }}
          onSelect={(node) => {

            if (node.children && node.children.length === 0) {
              asynGetData(node, (children) => {
                const nextData = cloneDeep(this.state.data);
                const selectedNode = findNodeOfTree(nextData, item => (node.id === item.id));
                selectedNode.children = children;
                this.setState({
                  data: nextData
                });
              });
            }

          }}
          onChange={(value, event) => {
            console.log(value, 'onChange');
            this.setState({
              value
            });
          }}
        />
      </div>
    );
  }
}

ReactDOM.render(<AsynExample />);

```
<!--end-code-->


如果节点中的数据需要通过异步加载的，可以在 `onSelect` 回调函数中处理，把 `data` 中的 `children`值设置为 `[]`, 但点击到这个节点是再去异步获取数据填充到 `children` 中。


通过 `renderMenu` 回调函数可以设置异步加载过程中显示的内容。
