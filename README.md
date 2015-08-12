SimpleFork
===================
����PCNTL��չ�Ľ��̹�������ӿ�������Java��Thread��Runnable 

����
---------------------------
+ �ṩ���̳�
+ �Զ�����ʬ���̻��գ�֧������������
+ �ṩ�����ڴ桢System V ��Ϣ���С�Semaphore��������IPCͨ�ţ�����ͨ�ţ�
+ �ṩProcess��Runnable���ַ�ʽʵ�ֽ���
+ ����ʵʱ��ȡ������״̬

ע����ѡ��
-----------------------
+ System V ��Ϣ���������ڳ����˳�ʱ���ܴ�����δ����������ݣ����Բ������١������Ҫ���٣������$queue->remove()����ɾ������
+ �����ڴ�������н����˳���ɾ��
+ Semaphore������ڶ������ʱ��������
+ ���̳�start()����Ҫ����wait()���н�ʬ���̻��գ���������������

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



