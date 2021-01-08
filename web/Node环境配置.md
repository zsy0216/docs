# 1. 下载

官网：https://nodejs.org/en/download/

**这里选择 Windows 64位 解压版示例：** 

![image-20210108111052419](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20210108111052419.png)

# 2. 解压到指定文件夹

![image-20210108111823015](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20210108111823015.png)

# 3. 配置环境变量

> 注意：node所在目录 `D:\devtools\node-v14.15.4` 
>
> 配置时需修改为自己电脑node所在目录

## 右键此电脑->属性->高级系统设置->环境变量->Path

<img src="https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20210108112126147.png" alt="image-20210108112126147" style="zoom: 33%;" />

## 点击编辑->新建->输入node所在目录

<img src="https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20210108112241822.png" alt="image-20210108112241822" style="zoom:33%;" />

确定保存

# 4. 测试

Win+R -> CMD

分别输入 

- `node -v`
- `npm -v` 

![image-20210108112529858](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20210108112529858.png)

# 5. 设置npm全局路径及cache路径

在nodejs路径下创建node_global 和 node_cache两个文件夹；

cmd 命令，分别输入如下命令

```shell
npm config set prefix "D:\devtools\node-v14.15.4\node_global"

npm config set cache"D:\devtools\node-v14.15.4\node_cache"
```

# 其他配置

## 设置淘宝源

```shell
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

## 安装 yarn

```shell
npm install -g yarn
```

常用命令：

- yarn / yarn install 等同于npm install 批量安装依赖
- yarn add xxx 等同于 npm install xxx —save 安装指定包到指定位置
- yarn remove xxx 等同于 npm uninstall xxx —save 卸载指定包
- yarn add xxx —dev 等同于 npm install xxx —save-dev
- yarn upgrade 等同于 npm update 升级全部包
- yarn global add xxx 等同于 npm install xxx -g 全局安装指定包

# Node安装完成