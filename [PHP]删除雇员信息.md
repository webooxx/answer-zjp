



#### 郑巨鹏  12:23:49

![del.1](img/del1.png?raw=true)

当我点击删除用户时，从连接发了这样的东西：

    <a onclick='confirmDele({$row['id']})' href='empProcess.php?return flag=del&id={$row['id']}'>删除用户</a>。

连接是指向empProcess.php的，而empprocess.php的代码是：<?php


            require_once 'empserverice.class.php';

            //接受id
            //创建empserverice对象实例
            $empserverice=new Empserverice();

            //判断admin是删除用户还是添加
            if(!empty($_GET['flag'])){
                $id=$_GET['id'];

                if($empserverice->delEmpByid($id)==1){
                    //ture
                    header("location:guanli.php");
                    exit();
                }else{
                    //false
                    header("location:text.php");
                    exit();
                }
            }

        ?>

其中有 require_once 'empserverice.class.php';，而下面则调用了delEmpByid($sql)方法。

这是empserverice.class.php delEmpByid方法的代码：


            //根据输入的id删除某个用户
            function delEmpByid($id){
                
                $sql="delete from employee where id=$id";
                
                //创建sqlhelper对象实例
                $sqlhelper=new SqlHelper();
                
                return $sqlhelper->execute_dml($sql);
            }
        }

这是sqlhelper.class.php execute_dml方法的代码：
        //dml语句
        public function execute_dml($sql){
            
            $b=mysql_query($sql,$this->link) or die(mysql_error());
            if (!$b) {
                return 0;
            }else {
                if(mysql_affected_rows($this->link)>0){
                    return 1;//表示执行成功
                }else{
                    return 2;//表示没有行数受到影响
                }
            }
        }
####  徐振华  12:24:57

那么错误是什么

####  郑巨鹏  12:25:34

算了，我发文件给你吧

####  徐振华  12:26:02

发文件给我也不行啊，我只能看到你的代码，没有你的 数据库。

#### 郑巨鹏  12:26:02

打开dl.php

#### 郑巨鹏  12:26:11

呃，也对噢

#### 郑巨鹏  12:26:15

![del.2](img/del2.png?raw=true)

我截图

#### 郑巨鹏  12:26:57

这是guanli.php

#### 郑巨鹏  12:27:39

当点击删除用户时，你看 

        echo "<tr><th>{$row['id']}</th><th>{$row['name']}</th><th>{$row['email']}</th><th>{$row['salary']}</th><th><a href='#'>修改用户</a></th><th><a onclick='confirmDele({$row['id']})' href='empProcess.php?return flag=del&id={$row['id']}'>删除用户</a></th></tr>";

#### 徐振华  12:27:59

点确定之后呢？

#### 郑巨鹏  12:29:14

不管点击确定或者取消都会跳转到这里

这是地址栏上面的信息
![del.3](img/del3.png?raw=true)
        http://localhost/shili/Empmanage/EmpManage/empProcess.php?return flag=del&id=35


#### 郑巨鹏  12:30:17

![del.4](img/del4.png?raw=true)

这是empProcess.php


#### 徐振华  12:30:23

那你的问题是？

#### 郑巨鹏  12:32:40

能不能把guanli.

php的超链接中的return去掉！因为我点击删除的时候，有这个return就会删除不成功，但是我如果去掉return的话，当我点击删除用户不论是确定还是取消都会被删掉

#### 郑巨鹏  12:33:48

在guanli.php里，有个javascript代码，

![del.5](img/del5.png?raw=true)
![del.6](img/del6.png?raw=true)

#### 徐振华  12:34:40

1. 不管取消还是确定，都跳转：是因为 confirmDele 函数内部没有组织 A 元素的默认事件，即使你取消了，还是会打开A链接
2. 页面一片空白，是正常的，是因为：你新打开的URL 是 「empProcess.php?return flag=del&id=0」，$_GET['flag']为空，$_GET['return flag'] = 'del'。所以没有进入到你的代码逻辑。

#### 徐振华  12:34:53

confirmDele 函数内部没有 阻止 A 元素的默认事件

#### 徐振华  12:35:21

「但是我如果去掉return的话，当我点击删除用户不论是确定还是取消都会被删掉」 这个是正确的逻辑！！！！

#### 郑巨鹏  12:36:00

我明白，现在就是这么解决的问题啦

#### 郑巨鹏  12:36:05

怎么

#### 徐振华  12:39:10

    <a onclick='confirmDele({$row['id']})' href='empProcess.php?return flag=del&id={$row['id']}'>

    ＝》修改

    <a onclick='confirmDele({$row['id']})' >

    function confirmDele(val){
       if( confirm('删除？') ){}
    }

#### 徐振华  12:39:49


    function confirmDele(val){
       if( confirm('删除？') ){
          location.href=    'empProcess.php?flag=del&id='+val ;
      }
    }

#### 徐振华  12:40:03

函数那块，以最后的为准。