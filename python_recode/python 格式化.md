# 方法一
## 利用%s

```python
a = "1"
b = "2"
print(a+"\n"+b) 

print("a:%s,b:%s"%(a,b)) # a:1,b:2
```


# 方法二 
## 利用f-String -> 不要忘记添加f

```python
print(f"a:{a},b:{b}") # a:1,b:2
```


# 方法三
## 利用format格式化输出format格式

```python
print("a:{a},b:{b}".format(a=b,b=a)) # a:2,b:1
```

