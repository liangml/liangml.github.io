```python
count = 0
while True:
    print("hello world",count)
    count +=1
    if count == 100:
        print("已暂停")
        break
-------------------------------------------------
打印100个数字50-60之间不打印
count = 0
while True:
    count += 1
    if count > 50 and count <60:
        continue
    print("hello world",count)
    if count == 100:
        print("已暂停")
        break
```
