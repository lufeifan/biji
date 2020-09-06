# 配置

```
git config --global user.name "lululua"
```

```
git config --global user.email 1714004230@qq.com
```

```
git config --list 
```

## 添加远程库





## 生成 SSH Key

```
ssh-keygen -t rsa -C "1714004230@qq.com"
```

## 连接github

![image-20200907042440106](C:\Users\17140\AppData\Roaming\Typora\typora-user-images\image-20200907042440106.png)

![image-20200907042515814](C:\Users\17140\AppData\Roaming\Typora\typora-user-images\image-20200907042515814.png) 

## 测试连接是否成功

```
 ssh -T git@github.com
```

输入yes

![image-20200907042639287](C:\Users\17140\AppData\Roaming\Typora\typora-user-images\image-20200907042639287.png)



```
echo "# biji" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M master
git remote add origin git@github.com:lufeifan/biji.git
git push -u origin master
```

```
git remote add origin git@github.com:lufeifan/biji.git
git branch -M master
git push -u origin master
```

