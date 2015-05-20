
ZJP：下面这些代码是我从书上抄上去运行d，但是不对！浏览器返回3个错误，你帮我分析看看

    <?php
    
    //count.txt为保存在线用户信息的文件
    $user_online = "cuont.txt";
    //touch函数的作用是若果没有此文件，则创建
    touch($user_online);
    //30秒没有动作则被认为掉线
    $timeout = 30;
    //读入文件内容，以#分离所有字符串，储存在数组$user_arr
    $user_arr = file_get_contents($user_online);
    $user_arr = explode('#', rtrim($user_arr, '#'));
    //$temp为放置所有users的array
    $temp = array();
    //对文件中的储存所有在线user进行循环处理，非本用户保存到$temp数组中
    foreach ($user_arr as $value) {
        $user = explode(",", trim($value));
        //若果不是本用户id，且时间没有超时则放入数组temp中
        if (($user[0] != getenv('REMOTE_ADDR')) && ($user[1] > time())) {//getenv获取环境变量
            array_push($temp, $user[0] . "," . $user[1]);
        }
    }
    //保存笨用户的信息(id,链接,时间)
    array_push($temp, getenv('REMOTE_ADDR') . "," . ($time() + ($timeout)) . '#');
    $user_arr = implode("#", $temp);
    //以更新的形式打开文件conut.txt，既不保存原来的内容。fopen函数的第二个函数的第二个参数[w],表示
    $fp = fopen($user_online, "w");//文件以更新的形式打开文件
    //
    flock($fp, LOCK_EX);//独占锁定的文件--写入程序
    fputs($fp, $user_arr);//写入
    flock($fp, LOCK_UN);//释放锁定
    fclose($fp);//关闭
    ?>
    
XZH：错误信息是什么？

ZJP：

    Notice: Undefined offset: 1 in /Users/lyn/wwwroot/a.php on line 18
    Notice: Undefined variable: time in /Users/lyn/wwwroot/a.php on line 23
    Fatal error: Function name must be a string in /Users/lyn/wwwroot/a.php on line 23

XZH：2个 Notice 提示信息，1个 Fatal error 致命错误

第一个，未定义的偏移。在18行，应该是  $user[1] 的问题（根本原因在于你的文件里面没有初始数据！）
第二个，未定义的变量：time，在23行。
第三个，个致命错误，函数名称必须是一个字符串，在23行。

ZJP：$user[1] 在16行不是已经定义成数组了吗？

XZH：但数组不一定有 $user[1] 这个下标，如果是空字符串，切出来，只有一个成员即 $user[0] === ''；

ZJP：两个错误解决了，剩下第18行的$user[1]
XZH：这个错误你有什么想法
ZJP：书上给的东西不完整

XZH：你看看我的代码吧，书上代码，可能是结合了其他程序，所以略微复杂了点。并且需要其他程序配合。

    <?php
    
    // 1. 定义&初始化 数据文件
    $db_table = 'count.json';
    if(!file_exists($db_table) && !touch($db_table) ){
        die('无法写入文件!');
    }
    
    // 2. 开启会话支持；声明：当前用户名、当前时间
    session_start();
    $user = session_id();
    $time = time();
    
    // 3. 把数据文件 转到 内存中 为一个 数组
    $db = (array)json_decode( file_get_contents($db_table) , true );
    
    // 4. 更新当前用户的时间
    $db[$user] = time();
    
    
    //var_dump($db);  // [DEBUG]看看当前的用户数据是什么情况
    
    // [TEST]假装有其他人在
    $db['old'] = 1;
    $db['yon'] = $time-10;
    
    //  5.清理老人
    foreach($db as $k => $v){
        if( $v < $time-30){
            unset( $db[$k] );
        }
    }
     
    //var_dump( $db ); // [DEBUG]看看现在用户数据是什么情况
    
    // 6. 保存数据
    file_put_contents($db_table, json_encode($db) );
    
    echo '<p>在线人数:'.count($db);
    ?>

ZJP：

$db['old'] = 1;
$db['yon'] = $time-10;这个我不懂  ，你从没有声明下标啊，为什么有old和yon出来？

XZH：这块是模拟了2个用户，一个用户 是 时间1访问的，  另一个用户是 10秒之前访问的，不模拟的话，用户永远是1啦，就你自己一个人。。。

ZJP：那么运行20秒后不也是剩下一个人？

XZH：:-P， 理论上是这样的，但是代码里面是写死了的。每次运行都会更新 $db['yon'] 是10秒前访问的。所以永远是2个人。。

ZJP：我要慢慢消化下

XZH：

这个代码有几个关键点：

1. 如何用服务器文件，「读取」、「保存」数据。
2. 如何解析 数据文件 的 格式，使其可以被更新（包括新增、删除和修改、查询统计）（延伸出 数据表的基本操作支持 增删改查）
3. 如何获得唯一用户标识？（ REMOTE_ADD 、 和 session_id() 的区别）
4. 如何设计一种「数据结构」和对应的「逻辑」，来淘汰过期的用户。



XZH：可以用我给的 4个关键点，一一去从代码中找到解决方案。

比如：如何用服务器文件，「读取」「保存」数据，用2个API   file_get_contetns、file_put_contents，来读取、写入文件；

同时用如下代码

    if(!file_exists($db_table) && !touch($db_table) ){
        die('无法写入文件!');
    }

保证数据文件存在，如果不存在就创建一个，如果无法创建，则程序不继续运行，报告一个错误。

ZJP：嗯，我试试看
