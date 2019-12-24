### 生命周期

#### 挂载阶段

constructor -> getDerivedStateFormProps -> render -> componentDidMount

#### 更新阶段

##### New Props/ setState 导致的更新

getDerivedStateFormProps -> shouldComponentUpdate -> render -> getSnapshotBeforeUpdate -> componmentDidUpdate

注意：v16.3 中，setState 是不会走 getDerivedStateFormProps 周期的，直接从 shouldComponentUpdate 开始

##### forceUpdate 导致的更新

getDerivedStateFormProps -> render -> getSnapshotBeforeUpdate -> componmentDidUpdate

注意：v16.3 中，foreceUpdate 也是不会触发 getDerivedStateFormProps，直接从 render 方法开始

#### 卸载阶段

直接 componentWillUnmont

![React lifecycle v16.4](static/React&#32;lifecycle&#32;v16.4.png)
![React lifecycle v16.3](static/React&#32;lifecycle&#32;v16.3.png)