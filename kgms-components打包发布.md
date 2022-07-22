### 一、kgms-components

1. npm i cnpm -g
2. cnpm config set registry http://192.168.4.3:7001
3. cnpm login
4. 输入账号，密码，邮箱 //注册
5. cnpm whoami
6. kgms-components 项目的 package.json 中，name 改为@plantdata/auto-kgms-components-xxxxx xxxx 为项目名
7. npm run build-components
8. npm run cnpm:publish
9. cnpm owner add jiangrun002 @plantdata/auto-kgms-components-xxxxx
   _打包前配置 export，才能打包完成_
   ![avatar](/picture/build/settings.png)

### 二、plantdata2020

10. npm i @plantdata/auto-kgms-components-xxxxx
11. 替换项目中的 plantdata/auto-kgms-components 为 plantdata/auto-kgms-components-xxxxx
    修改 plugin-kgdatalake 中的路由
12. 打包 build-plugin-kgdatalake
13. 更新前端包 xftp 根据前端更新地址将包放在服务器上
14. 后端添加到导航数据库中

### 三、备注

- 每个侧边栏都可以通过 url 打开
  ![avatar](/picture/build/url.png)
- 在 palantdata2020 中修改路由后可以通过拼接打开自己的页面
  ![avatar](/picture/build/plantdata-url.png)
- 导航请求的数据
  ![avatar](/picture/build/navigation-data.png)
