郑巨鹏  00:28:


    <?php
    $arr=$_REQUEST['search_grade'];
    $arr=explode(" ",$arr);
    $n=$_REQUEST['num'];
    $n=explode(" ",$n);
    function search(&$arr,&$n){
        $flag=false;
        for($i=0;$i<count($arr);$i++){
            if($n==$arr[$i]){
                echo"其下标为$i";
                $flag=true;
                break;
            }
        }
        if(!$flag){
            echo"该数组没有其值";
        }
    }
    search($arr,$n);
    ?>


这段代码哪里出错了？


徐振华  10:43:04


代码的功能是什么？


徐振华  10:52:20


![php.array.1](img/php.array.1.jpg?raw=true)


徐振华  10:58:45


另外，函数参数，不建议用 「引用传参」。

1. 引用传参是全局的，全局变量性能比局部变量差。
2. 函数一般只因该「返回」什么，而不应该直接「修改参数」（引用传参允许直接修改）


郑巨鹏  12:42:03


代码的功能是查找数组下标


郑巨鹏  12:42:25


    <h3>输入数值,查找其数组下标--->顺序查找</h3>
    <form action="search.php" method="post">
    输入数组：<input type="test" name="search_grade"/>
    输入被查找数:<input type="test" name="num"/>
    <input type="submit" value="查找下标"/>
    </form>



郑巨鹏  12:42:54


    <?php
    $arr=$_REQUEST['search_grade'];
    $arr=explode(" ",$arr);
    $n=$_REQUEST['num'];
    $n=explode(" ",$n);
    function search(&$arr,&$n){
        $flag=false;
        for($i=0;$i<count($arr);$i++){
            if($n==$arr[$i]){
                echo"其下标为$i";
                $flag=true;
                break;
            }
        }
        if(!$flag){
            echo"该数组没有其值";
        }
    }
    search($arr,$n);
    ?>


郑巨鹏  12:50:10

if($n==$arr[$i]){这段改成if($n[0]==$arr[$i]){
或者把$n=explode(" ",$n);删掉就可以运行成功对不对？


徐振华  13:20:54


不对，你查找下标干嘛？


徐振华  13:22:48


数组是一个一个的  「键->值 对」 没错吧？你的功能是什么？输入一个「值」找到对应的键？


徐振华  13:23:07


array_search 函数已经实现了这个功能。


徐振华  13:31:53



你的代码思路不清晰。。


输入数值,查找其数组下标--->顺序查找


 ===> 这个描述我没看懂，你看是不是下面的这个意思


『定义一个有N个数字的数组，格式（3,5,7），随意输入一个数字，如果在这个数组中，则返回这个数字的位置』


郑巨鹏  13:36:16


嗯


郑巨鹏  13:39:12


比如一个商城，里面有很多商品，用户想查找商品，我觉得下标会有用的----这是我乱想的，其实那样写代码主要是练习


徐振华  13:43:15


嗯，没错。你能想到这里，思路很不错。


徐振华  13:58:54


商城的商品数据，一般是存在「表」里面的，格式如下。用户传入的一般是商品名称。


【编号：1】，【名称：牙刷】，【价格：6】，【详细参数：……若干文字】


徐振华  13:59:10


对你那个需求，我写了一些代码。


徐振华  13:59:11

![php.array.2](img/php.array.2.jpg?raw=true)

    <?php

     // 模拟GET请求:  index.php?arr=1,2,4,5,6,7&num=5


    // 获得浏览器传来的参数
    $arr = explode(",",$_REQUEST['arr']);
    $num = (int)$_REQUEST['num'];


    /**
     * 从数组中查找一个成员，使用系统的方法
     * @return {int||null} 如果有这个成员，反回该成员的索引，否则反回 null
     */
    function search( $arr ,$num){
        return array_search($num, $arr);
    }

    /**
     * 从数组中查找一个成员，使用遍历方法
     * @return {int||null} 如果有这个成员，反回该成员的索引，否则反回 null
     */
    function search_custom( $arr ,$num ){
        foreach ($arr as $key => $value) {
            if( $num == $value){
                return $key;
            }
        }
        return null;
    }

    /**
     * 从数组中查找一个成员，使用特别的系统函数，对原始函数进行转换
     * @return {int||null} 如果有这个成员，反回该成员的索引，否则反回 null
     */
    function search_custom( $arr ,$num ){
        $filp = array_flip($arr);
        return isset($filp[$num]) ? $filp[$num] : null

    }

    ?>


郑巨鹏  14:19:11


我要去上课了，下课后我回来再看看你写的代码


徐振华  14:19:22

ok