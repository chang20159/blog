react 
shoulComponentUpdate return false
props没有数据
props数据没有变化


避免频繁更新store  
immutablejs性能优化



this.state 是否返回新的对象
this.setState 是否深度合并

6s > 3s 无缓存   有缓存2s到3s


componentWillReceiveProps(nextProps){
        const { selected } = nextProps.data
        this.setState({
            selected: [...selected]
        })
    }
    
    
    shouldcomponentupdate 如何判断props内容没有变化
    
    除 transform 或 opacity 属性之外，更改任何属性始终都会触发绘制。