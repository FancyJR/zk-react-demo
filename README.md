# zk-react-demo
zk-react 示例项目

## Build Setup
> 使用[yarn](https://yarnpkg.com/zh-Hans/)

``` bash
# install dependencies
$ yarn

# serve with hot reload at localhost:8080
yarn run dev

# build for production with minification
yarn run build

```

## 项目结构
```
├── commons             // 系统公共方法，组件
├── constants           // redux相关常量、系统其他常量
├── frame               // 页面框架，头部+左侧等
├── mock                // mock数据，截获ajax请求，便于前端单独调试
│   └── mockdata        // 模拟数据 mockjs
├── pages               // 业务页面，业务开发主要关系目录
│   ├── error           // 一些error页面，404 403 401 等等
│   ├── examples        // 一些例子
│   └── home            // 首页
├── redux               // redux 相关
│   ├── actions         // redux action定义
│   ├── reducers        // redux reducers定义目录
│   └── actionTypes.js  // actions 和 reducers使用的types常量
├── App.jsx             // 项目入口文件
└── global.less         // 全局样式定义

```

## 系统菜单激活状态
> 系统菜单的激活状态根据url地址，自动判定

- 如果是二级页面，不如添加页面，需要保持其父级页面菜单状态，菜单path需要写成`parentPath/+childPath`，使用`/+`作为分界，比如：
```
list页面：
export const PAGE_ROUTE = '/example/users'

list页面的添加按钮，跳转到添加页面，但是页面菜单选中状态要保持list页面状态

export const PAGE_ROUTE = '/example/users/+add'
```

## 页面头部
> 页面头部可以控制显示隐藏、修改标题、修改面包屑

### 显示隐藏
```
componentWillMount() {
    const {actions} = this.props;
    actions.hidePageHeader();
}

```

### 修改标题
```
componentWillMount() {
    const {actions} = this.props;
    actions.setPageTitle('自定义页面标题');
}
```

### 自定义面包屑导航
```
componentWillMount() {
    actions.setPageBreadcrumbs([
        {
            key: 'zidingyi',
            path: '',
            text: '自定义',
            icon: 'fa-user',
        },
        {
            key: 'mianbaoxie',
            path: '',
            text: '面包屑',
            icon: 'fa-user',
        },
        {
            key: 'daohang',
            path: '',
            text: '导航',
            icon: 'fa-user',
        },
    ]);
}
```

## 页面写法
> 为了简化开发，通过脚本自动生成部分代码，需要注意几个约定

### 路由
> 页面导出 PAGE_ROUTE 常量即可，常量的值对应菜单的path

```
export const PAGE_ROUTE = '/example/users';

// 如果二级页面保持父级页面菜单选中状态，二级页面路由约定：parent_page_route/+child_page_route，通过`/+`进行分割
export const page_route = '/example/users/+add';
```

### redux
> 页面导出 `LayoutComponent` `mapStateToProps这两个变量`，系统会自动将当前组件与redux进行连接

```
export class LayoutComponent{...}

export function mapStateToProps(state) {
    return {
        ...state.frame,
    };
}
```

如果页面想使用简化的redux（只数据共享，操作不共享），请定义INIT_STATE常量
```
// 初始化数据
export const INIT_STATE = {
    scope: 'actionsSetState', // 定义命名空间
    sync: true, // 是否与localStorage进行同步
    a: {
        b: {
            c: ['ccc'],
            c2: 'c2',
        },
        b1: [],
        b2: 'b2',
    },
    d: 'd',
    e: 'e',
};

// 注入数据
export function mapStateToProps(state) {
    return {
        ...state.pageState.actionsSetState,
    };
}

// 取值
const {a, d, e} = this.props;

// 赋值
this.props.actions.setState({a: 'new value'});
```

## TODO
- [ ] 登录之后，获取菜单数据，并存入session中，由于页面头部是由菜单生成的，如果菜单是异步获取的，将会存在各种问题，所以进入系统时候保证菜单可用