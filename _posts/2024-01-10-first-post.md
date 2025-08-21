---
layout: post
title: "React Hooks 最佳实践总结"
categories: [前端开发]
tags: [React, Hooks, JavaScript, 最佳实践]
---

## 引言

React Hooks 自 16.8 版本发布以来，已经成为现代 React 开发的标准。本文将总结我在实际项目中使用 Hooks 的最佳实践。

## 核心原则

### 1. 只在顶层调用 Hooks

```javascript
// ✅ 正确
function MyComponent() {
  const [count, setCount] = useState(0);
  const [name, setName] = useState('');
  
  useEffect(() => {
    document.title = `Count: ${count}`;
  }, [count]);
}

// ❌ 错误
function MyComponent() {
  if (condition) {
    const [count, setCount] = useState(0); // 违反规则
  }
}
```

### 2. 自定义 Hooks 的命名

```javascript
// ✅ 正确：以 use 开头
function useLocalStorage(key, initialValue) {
  const [storedValue, setStoredValue] = useState(() => {
    try {
      const item = window.localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch (error) {
      return initialValue;
    }
  });

  const setValue = value => {
    try {
      setStoredValue(value);
      window.localStorage.setItem(key, JSON.stringify(value));
    } catch (error) {
      console.log(error);
    }
  };

  return [storedValue, setValue];
}
```

## 常用模式

### 1. 数据获取

```javascript
function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchData = async () => {
      try {
        setLoading(true);
        const response = await fetch(url);
        const result = await response.json();
        setData(result);
      } catch (err) {
        setError(err);
      } finally {
        setLoading(false);
      }
    };

    fetchData();
  }, [url]);

  return { data, loading, error };
}
```

### 2. 防抖 Hook

```javascript
function useDebounce(value, delay) {
  const [debouncedValue, setDebouncedValue] = useState(value);

  useEffect(() => {
    const handler = setTimeout(() => {
      setDebouncedValue(value);
    }, delay);

    return () => {
      clearTimeout(handler);
    };
  }, [value, delay]);

  return debouncedValue;
}
```

## 性能优化

### 1. 使用 useMemo 和 useCallback

```javascript
function ExpensiveComponent({ items, onItemClick }) {
  // 缓存计算结果
  const processedItems = useMemo(() => {
    return items.map(item => ({
      ...item,
      processed: item.value * 2
    }));
  }, [items]);

  // 缓存回调函数
  const handleClick = useCallback((id) => {
    onItemClick(id);
  }, [onItemClick]);

  return (
    <div>
      {processedItems.map(item => (
        <div key={item.id} onClick={() => handleClick(item.id)}>
          {item.name}
        </div>
      ))}
    </div>
  );
}
```

## 总结

React Hooks 让函数组件变得更加强大，但需要遵循一定的规则和最佳实践。通过合理使用 Hooks，我们可以写出更清晰、更易维护的代码。

## 参考资源

- [React Hooks 官方文档](https://reactjs.org/docs/hooks-intro.html)
- [useHooks](https://usehooks.com/) - 常用 Hooks 集合
