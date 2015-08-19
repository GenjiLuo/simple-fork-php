SimpleFork
===================
����PCNTL��չ�Ľ��̹�������ӿ�������Java��Thread��Runnable 

ΪʲôҪдSimpleFork
------------------------
����̳���ı�д��Ƚ϶��̱߳�д���Ӹ��ӣ���Ҫ���ǽ��̻��ա�ͬ�������⡢ͨ�ŵ����⡣���ڳ�ѧ����˵���������������Ƚ����ѡ�
�������źŴ���ͽ���ͨ����飬���������������⡣
SimpleFork�ṩһ��������JAVA���̵߳Ľ��̿��ƽӿڣ��ṩ���ա�ͬ�������⡢ͨ�ŵȷ����������߿��Թ�עҵ�����⣬����Ҫ���࿼�ǽ��̿��ơ�

����
---------------------
```bash
composer require jenner/simple_fork
```
```php
require path/to/SimpleFork/autoload.php
```

����
----------------------
����  
+ ext-pcntl ���̿���  
��ѡ  
+ ext-sysvmsg ��Ϣ����
+ ext-sysvsem ͬ��������
+ ext-sysvshm �����ڴ�

����
---------------------------
+ �ṩ���̳�
+ �Զ�����ʬ���̻��գ�֧������������
+ �ṩ�����ڴ桢System V ��Ϣ���С�Semaphore��������IPCͨ�ţ�����ͨ�ţ�
+ �ṩProcess��Runnable���ַ�ʽʵ�ֽ���
+ ����ʵʱ��ȡ������״̬
+ shutdown���н��̻򵥶�stopһ������ʱ������ע�Ḳ��beforeExit()����������true���˳���false�������У���ĳЩ���������̲��������˳���
+ ֧���ӽ�������ʱreload

ע������
-----------------------
+ System V ��Ϣ���������ڳ����˳�ʱ���ܴ�����δ����������ݣ����Բ������١������Ҫ���٣������$queue->remove()����ɾ������
+ �����ڴ�������н����˳���ɾ��
+ Semaphore������ڶ������ʱ��������
+ ���̳�start()����Ҫ����wait()���н�ʬ���̻��գ���������������
+ ��ȡ����״̬(����isAlive()����)ǰ����õ���һ����������wait(false)����һ�λ��գ����ڽ�������״̬���жϲ���ԭ�Ӳ���������isAlive()��������֤��ʵ��״̬��ȫһ��
+ ����㲻�����ʲô�������Ҫ�ڳ�����ʼ����declare(ticks=1);����ô���Ĭ�ϵ�һ�ж��������������

���ʹ��declare(ticks=1);
--------------------------
+ declare(ticks=1); ����������ڽ����źŴ������ע�����źŴ������������ûִ��һ�д�����Զ�����Ƿ�����δ������źš�[http://php.net/manual/zh/control-structures.declare.php](http://php.net/manual/zh/control-structures.declare.php �ٷ��ĵ�)

TODO
---------------------------
+ �ṩ���๦�ܵĽ��̳أ�ģ��java
+ �ṩ����������ͨ�Ż��ƣ�Redis�ȣ�
+ ����Ĳ��Լ�ʾ������

ʾ������
-------------------------
����ʾ�������exmplesĿ¼  
simple.php  
```php
class TestRunnable extends \Jenner\SimpleFork\Runnable{

    /**
     * ����ִ�����
     * @return mixed
     */
    public function run()
    {
        echo "I am a sub process" . PHP_EOL;
    }
}

$process = new \Jenner\SimpleFork\Process(new TestRunnable());
$process->start();
```

shared_memory.php
```php
class Producer extends \Jenner\SimpleFork\Process{
    public function run(){
        for($i = 0; $i<10; $i++){
            $this->cache->set($i, $i);
            echo "set {$i} : {$i}" . PHH_EOL;
        }
    }
}

class Worker extends \Jenner\SimpleFork\Process{
    public function run(){
        sleep(5);
        for($i=0; $i<10; $i++){
            echo "get {$i} : " . $this->cache->get($i) . PHP_EOL;
        }
    }
}

$memory = new \Jenner\SimpleFork\IPC\SharedMemory();
$producer = new Producer();
$producer->setCache($memory);

$worker = new Worker();
$worker->setCache($memory);

$pool = new \Jenner\SimpleFork\Pool();
$pool->submit($producer);
$pool->submit($worker);
$pool->start();
$pool->wait();
```



