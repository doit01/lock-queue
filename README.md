Java中线程安全队列

JDK 中常见的线程安全的队列如下：
队列名字	锁	是否有界
ArrayBlockingQueue	加锁（ReentrantLock）	有界
LinkedBlockingQueue	加锁（ReentrantLock）	有界
LinkedTransferQueue	无锁（CAS）	无界
ConcurrentLinkedQueue	无锁（CAS）	无界

从上表中可以看出：这些队列要不就是加锁有界，要不就是无锁无界。而加锁的的队列势必会影响性能，无界的队列又存在内存溢出的风险。
因此，一般情况下，我们都是不建议使用 JDK 内置线程安全队列。
Disruptor 就不一样了！它在无锁的情况下还能保证队列有界，并且还是线程安全的。
————————————————

                            版权声明：本文为博主原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接和本声明。
                        
原文链接：https://blog.csdn.net/u012060033/article/details/131529257


LinkedBlockingQueue是一个基于链表实现的阻塞队列，默认情况下，该阻塞队列的大小为Integer.MAX_VALUE，由于这个数值特别大，所以 LinkedBlockingQueue 也被称作无界队列，代表它几乎没有界限，队列可以随着元素的添加而动态增长，但是如果没有剩余内存，则队列将抛出OOM错误。所以为了避免队列过大造成机器负载或者内存爆满的情况出现，我们在使用的时候建议手动传一个队列的大小。

　　【2】LinkedBlockingQueue内部由单链表实现，只能从head取元素，从tail添加元素。LinkedBlockingQueue采用两把锁的锁分离技术实现入队出队互不阻塞，添加元素和获取元素都有独立的锁，也就是说LinkedBlockingQueue是读写分离的，读写操作可以并行执行。

 
LinkedBlockingQueue使用

//指定队列的大小创建有界队列
BlockingQueue<Integer> boundedQueue = new LinkedBlockingQueue<>(100);
//无界队列
BlockingQueue<Integer> unboundedQueue = new LinkedBlockingQueue<>();




// 容量,指定容量就是有界队列
private final int capacity;
// 元素数量，用原子操作类的原因在于有两个线程都会操作需要保证可见性
private final AtomicInteger count = new AtomicInteger();
// 链表头  本身是不存储任何元素的，初始化时item指向null
transient Node<E> head;
// 链表尾
private transient Node<E> last;
// take锁   锁分离，提高效率
private final ReentrantLock takeLock = new ReentrantLock();
// notEmpty条件
// 当队列无元素时，take锁会阻塞在notEmpty条件上，等待其它线程唤醒
private final Condition notEmpty = takeLock.newCondition();
// put锁
private final ReentrantLock putLock = new ReentrantLock();
// notFull条件
// 当队列满了时，put锁会会阻塞在notFull上，等待其它线程唤醒
private final Condition notFull = putLock.newCondition();

//典型的单链表结构
static class Node<E> {
    E item;  //存储元素



    https://www.baeldung.com/lmax-disruptor-concurrency


    https://blog.csdn.net/guanmao4322/article/details/103395271?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_baidulandingword~default-0-103395271-blog-131529257.235^v43^pc_blog_bottom_relevance_base4&spm=1001.2101.3001.4242.1&utm_relevant_index=1
