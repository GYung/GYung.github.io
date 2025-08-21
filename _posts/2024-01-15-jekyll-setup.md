---
layout: post
title: "TypeScript 高级类型技巧总结"
categories: [前端开发]
tags: [TypeScript, 类型系统, JavaScript, 最佳实践]
---

## 引言

TypeScript 的类型系统非常强大，掌握一些高级类型技巧可以让我们写出更安全、更易维护的代码。本文将分享一些实用的类型技巧。

## 1. 条件类型 (Conditional Types)

条件类型是 TypeScript 中最强大的特性之一：

```typescript
type NonNullable<T> = T extends null | undefined ? never : T;

// 使用示例
type T0 = NonNullable<string | number | null>; // string | number
type T1 = NonNullable<string[] | null | undefined>; // string[]
```

### 实际应用：API 响应类型

```typescript
type ApiResponse<T> = {
  success: true;
  data: T;
} | {
  success: false;
  error: string;
};

// 提取成功时的数据类型
type SuccessData<T> = T extends { success: true; data: infer U } ? U : never;

// 使用示例
type UserResponse = ApiResponse<{ id: number; name: string }>;
type UserData = SuccessData<UserResponse>; // { id: number; name: string }
```

## 2. 映射类型 (Mapped Types)

映射类型可以基于现有类型创建新类型：

```typescript
// 将所有属性变为可选
type Partial<T> = {
  [P in keyof T]?: T[P];
};

// 将所有属性变为只读
type Readonly<T> = {
  readonly [P in keyof T]: T[P];
};

// 将所有属性变为必需
type Required<T> = {
  [P in keyof T]-?: T[P];
};
```

### 实际应用：表单验证

```typescript
interface User {
  id: number;
  name: string;
  email: string;
  age?: number;
}

// 创建表单类型，所有字段都是可选的
type UserForm = Partial<User>;

// 创建验证错误类型
type ValidationErrors<T> = {
  [K in keyof T]?: string;
};

// 使用示例
const formData: UserForm = {
  name: "John",
  email: "john@example.com"
};

const errors: ValidationErrors<UserForm> = {
  email: "邮箱格式不正确"
};
```

## 3. 模板字面量类型 (Template Literal Types)

TypeScript 4.1 引入的新特性：

```typescript
type HttpMethod = 'GET' | 'POST' | 'PUT' | 'DELETE';
type ApiEndpoint = `/api/${string}`;
type FullUrl = `${HttpMethod} ${ApiEndpoint}`;

// 使用示例
const url: FullUrl = 'GET /api/users'; // ✅ 正确
const invalidUrl: FullUrl = 'PATCH /api/users'; // ❌ 错误
```

### 实际应用：路由类型

```typescript
type RouteParams<T extends string> = T extends `${string}:${infer Param}/${infer Rest}`
  ? Param | RouteParams<Rest>
  : T extends `${string}:${infer Param}`
  ? Param
  : never;

type Routes = '/users/:id' | '/posts/:id/comments/:commentId';
type Params = RouteParams<Routes>; // 'id' | 'commentId'
```

## 4. 工具类型组合

组合多个工具类型创建复杂类型：

```typescript
// 深度部分类型
type DeepPartial<T> = {
  [P in keyof T]?: T[P] extends object ? DeepPartial<T[P]> : T[P];
};

// 深度只读类型
type DeepReadonly<T> = {
  readonly [P in keyof T]: T[P] extends object ? DeepReadonly<T[P]> : T[P];
};

// 使用示例
interface Config {
  api: {
    baseUrl: string;
    timeout: number;
  };
  features: {
    darkMode: boolean;
    notifications: boolean;
  };
}

type PartialConfig = DeepPartial<Config>;
// 结果：
// {
//   api?: {
//     baseUrl?: string;
//     timeout?: number;
//   };
//   features?: {
//     darkMode?: boolean;
//     notifications?: boolean;
//   };
// }
```

## 5. 类型守卫和类型断言

```typescript
// 类型守卫函数
function isString(value: unknown): value is string {
  return typeof value === 'string';
}

function isNumber(value: unknown): value is number {
  return typeof value === 'number';
}

// 联合类型处理
function processValue(value: string | number) {
  if (isString(value)) {
    // 这里 TypeScript 知道 value 是 string
    return value.toUpperCase();
  } else {
    // 这里 TypeScript 知道 value 是 number
    return value.toFixed(2);
  }
}
```

## 6. 实用工具类型

```typescript
// 提取函数返回类型
type ReturnType<T extends (...args: any) => any> = T extends (...args: any) => infer R ? R : any;

// 提取函数参数类型
type Parameters<T extends (...args: any) => any> = T extends (...args: infer P) => any ? P : never;

// 提取 Promise 类型
type Awaited<T> = T extends Promise<infer U> ? U : T;

// 使用示例
async function fetchUser(id: number): Promise<{ id: number; name: string }> {
  return { id, name: 'John' };
}

type User = Awaited<ReturnType<typeof fetchUser>>; // { id: number; name: string }
type FetchParams = Parameters<typeof fetchUser>; // [number]
```

## 总结

TypeScript 的高级类型系统让我们能够：

1. **提高代码安全性**：编译时发现潜在错误
2. **改善开发体验**：更好的 IDE 支持和自动补全
3. **增强代码可维护性**：类型即文档
4. **减少运行时错误**：类型检查在编译时完成

掌握这些高级类型技巧，可以让我们的 TypeScript 代码更加健壮和优雅。

## 参考资源

- [TypeScript 官方文档](https://www.typescriptlang.org/docs/)
- [TypeScript 高级类型](https://www.typescriptlang.org/docs/handbook/advanced-types.html)
