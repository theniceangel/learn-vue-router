#  数据类型总览

```js
// 返回通过 path-to-regexp 库处理 route.path 的 正则，拥有 keys 属性
declare class RouteRegExp extends RegExp {
  keys: Array<{ name: string, optional: boolean }>;
}

// path-to-regexp 库的第三个参数
declare type PathToRegexpOptions = {
  sensitive?: boolean,
  strict?: boolean,
  end?: boolean
}

// 字典类型的泛型
declare type Dictionary<T> = { [key: string]: T }

// 导航守卫
declare type NavigationGuard = (
  to: Route,
  from: Route,
  next: (to?: RawLocation | false | Function | void) => void
) => any

// 实例化 router 的配置项
declare type RouterOptions = {
  routes?: Array<RouteConfig>;
  mode?: string;
  fallback?: boolean;
  base?: string;
  linkActiveClass?: string;
  linkExactActiveClass?: string;
  parseQuery?: (query: string) => Object;
  stringifyQuery?: (query: Object) => string;
  scrollBehavior?: (
    to: Route,
    from: Route,
    savedPosition: ?Position
  ) => PositionResult | Promise<PositionResult>;
}

// redirect 的配置项
declare type RedirectOption = RawLocation | ((to: Route) => RawLocation)

// routes 每一项的配置项
declare type RouteConfig = {
  path: string;
  name?: string;
  component?: any;
  components?: Dictionary<any>;
  redirect?: RedirectOption;
  alias?: string | Array<string>;
  children?: Array<RouteConfig>;
  beforeEnter?: NavigationGuard;
  meta?: any;
  props?: boolean | Object | Function;
  caseSensitive?: boolean;
  pathToRegexpOptions?: PathToRegexpOptions;
}

// 由 RouteConfig 生成的路由记录
declare type RouteRecord = {
  path: string;
  regex: RouteRegExp;
  components: Dictionary<any>;
  instances: Dictionary<any>;
  name: ?string;
  parent: ?RouteRecord;
  redirect: ?RedirectOption;
  matchAs: ?string;
  beforeEnter: ?NavigationGuard;
  meta: any;
  props: boolean | Object | Function | Dictionary<boolean | Object | Function>;
}

// 位置信息
declare type Location = {
  _normalized?: boolean;
  name?: string;
  path?: string;
  hash?: string;
  query?: Dictionary<string>;
  params?: Dictionary<string>;
  append?: boolean;
  replace?: boolean;
}

declare type RawLocation = string | Location

// 每一次路径切换生成的 Route
declare type Route = {
  path: string;
  name: ?string;
  hash: string;
  query: Dictionary<string>;
  params: Dictionary<string>;
  fullPath: string;
  matched: Array<RouteRecord>;
  redirectedFrom?: string;
  meta?: any;
}

```