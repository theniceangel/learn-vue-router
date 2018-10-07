#  Vue-Router 的工具库

## path

  - **cleanPath**

  ```js
  export function cleanPath (path: string): string {
    return path.replace(/\/\//g, '/')
  }

  '//foo//baz' => '/foo/baz'
  ```

  - **parsePath**

  ```js
  export function parsePath (path: string): {
    path: string;
    query: string;
    hash: string;
  } {
    let hash = ''
    let query = ''

    const hashIndex = path.indexOf('#')
    if (hashIndex >= 0) {
      hash = path.slice(hashIndex)
      path = path.slice(0, hashIndex)
    }

    const queryIndex = path.indexOf('?')
    if (queryIndex >= 0) {
      query = path.slice(queryIndex + 1)
      path = path.slice(0, queryIndex)
    }

    return {
      path,
      query,
      hash
    }
  }

  '/a?foo=bar#ok?baz=qux'' => {path: '/a', query: 'foo=bar', hash: '#ok?baz=qux'}
  ```

  - **resolvePath**

  ```js
  export function resolvePath (
    relative: string,
    base: string,
    append?: boolean
  ): string {
    const firstChar = relative.charAt(0)
    if (firstChar === '/') {
      return relative
    }

    if (firstChar === '?' || firstChar === '#') {
      return base + relative
    }

    const stack = base.split('/')

    // remove trailing segment if:
    // - not appending
    // - appending to trailing slash (last segment is empty)
    if (!append || !stack[stack.length - 1]) {
      stack.pop()
    }

    // resolve relative path
    const segments = relative.replace(/^\//, '').split('/')
    for (let i = 0; i < segments.length; i++) {
      const segment = segments[i]
      if (segment === '..') {
        stack.pop()
      } else if (segment !== '.') {
        stack.push(segment)
      }
    }

    // ensure leading slash
    if (stack[0] !== '') {
      stack.unshift('')
    }

    return stack.join('/')
  }

  "resolvePath('/a', '/b')" => '/a'
  "resolvePath('c/d', '/b')" => '/c/d'
  "resolvePath('c/d', '/b', true)" => '/b/c/d'
  "resolvePath('../d', '/a/b/c')" => '/a/d'
  "resolvePath('../d', '/a/b/c', true)" => '/a/b/d'
  "resolvePath('?foo=bar', '/a/b?foo=bar')" => '/a'
  "resolvePath('#hi', '/a/b')" => '/a/b#hi'
  ```
