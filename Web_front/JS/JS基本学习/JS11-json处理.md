```js
在 JavaScript 中，处理 JSON 数据涉及几个常用的方法和技术，这些方法使得开发者可以轻松地读取、解析、转换和操作 JSON 格式的数据。以下是一些基本方法和技巧，你可以用它们来处理 JSON 数据：

1. 解析 JSON 字符串
使用 JSON.parse() 方法将 JSON 格式的字符串转换为 JavaScript 对象。这是处理从服务器接收的数据或读取文件内容时的常用方法。

示例代码
javascript
const jsonString = '{"name": "John", "age": 30}';
const jsonObject = JSON.parse(jsonString);
console.log(jsonObject.name); // 输出 "John"
2. 将 JavaScript 对象转换为 JSON 字符串
使用 JSON.stringify() 方法将 JavaScript 对象转换成 JSON 格式的字符串。这对于发送数据到服务器或将数据保存到本地存储时非常有用。

示例代码
javascript
const jsonObject = { name: "Alice", age: 25 };
const jsonString = JSON.stringify(jsonObject);
console.log(jsonString); // 输出 '{"name":"Alice","age":25}'
3. 遍历 JSON 对象
利用 for...in 循环或 Object.keys() 方法遍历 JSON 对象的键（properties）。

示例代码
javascript
const user = {
  name: "Bob",
  age: 28,
  occupation: "Engineer"
};

for (let key in user) {
  console.log(key, user[key]); // 输出每个键和对应的值
}

// 使用 Object.keys() 获取所有键的数组
Object.keys(user).forEach(key => {
  console.log(key, user[key]); // 同样输出每个键和对应的值
});
4. 过滤和处理 JSON 数组
使用数组方法如 Array.prototype.filter(), Array.prototype.map(), 和 Array.prototype.reduce() 来处理存储在 JSON 格式中的数组。

示例代码
javascript
const users = [
  { name: "Dave", age: 34 },
  { name: "Katherine", age: 27 },
  { name: "John", age: 45 }
];

// 使用 filter() 方法找到所有年龄大于 30 的用户
const olderThan30 = users.filter(user => user.age > 30);
console.log(olderThan30);

// 使用 map() 方法创建新数组，包含用户的姓名
const names = users.map(user => user.name);
console.log(names);

// 使用 reduce() 方法计算所有用户的年龄总和
const totalAge = users.reduce((sum, user) => sum + user.age, 0);
console.log(totalAge);
5. 错误处理
处理 JSON 数据时，尤其是解析 JSON 字符串时，使用 try...catch 结构来捕获并处理可能的错误，如格式错误。

示例代码
javascript
const jsonData = '{"name": "Steve", "age": "unknown"}'; // age 应该是一个数字

try {
  const user = JSON.parse(jsonData);
  if (typeof user.age !== 'number') {
    throw new Error("Invalid data type for age.");
  }
  console.log(user);
} catch (error) {
  console.error("Failed to parse JSON!", error);
}
```

