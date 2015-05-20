郑巨鹏  01:16:30


    class a{ function __construct(){ echo "asd";}}
    class b extends a{ function __construct(){ echo "zxc";}}
    class c extends b{ function __construct(){}}


为什么c类里什么都没写，却能调用a和b的构造方法？


徐振华  10:44:50


这是PHP代码吗？完成的PHP代码？必须是不能啊。


1. 子类只会继承父类的方法
2. 子类如果想要调用父类的构造方法，那么应在自的构造方法中调用『 parent::__construct()』


徐振华  10:46:46


![php.class](img/php.class.jpg?raw=true)

徐振华  10:46:53


    <?php
    class a{
     function __construct(){ echo "init a";}
    }
    class b extends a{ 

        function __construct(){ echo "init b";}

        public  function getName(){
            return 'i am  class b';
        }
        
    }
    class c extends b{

        function __construct(){
            // 必须手动调用，父类 b 的构造方法。
            parent::__construct();
        }
    }

    $c = new c(); // 输出 'init b'
    echo $c->getname(); // 输出 'i am class b'
    ?>


郑巨鹏  11:52:21


上面我发的那段代码是a类的构造函数被b类的调用了，然后c类继承a和b的....噢，我明白了


郑巨鹏  11:54:01


    //多层继承
    class A{
        public $n1=90;
        public function __construct(){
            echo"A__construct";
        }
    }
    class B extends A{
        public function __construct(){
            echo"B__construct";
            //显式调用父类构造方法
            //parent::__construct;
            A::__construct();
        }
        
        public $n2="ABC";
    }
    class C extends B{
        //在C类也能调用A和B的构造方法，因为它继承了
    }

    $C=new C();
    echo $C->n1;
    echo $C->n2;