### DistributedLock

```java
package com.uioqv.zookeeper;

import org.apache.zookeeper.*;
import org.apache.zookeeper.data.Stat;
import java.io.IOException;
import java.util.List;
import java.util.SortedSet;
import java.util.TreeSet;
import java.util.concurrent.CountDownLatch;
import java.util.concurrent.TimeUnit;

public class DistributedLock {
    private String lockId;
    private static final String LOCK_ROOT = "/LOCKS";
    private final static byte[] data = {0x12, 0x34};
    private final CountDownLatch latch = new CountDownLatch(1);
    private ZooKeeper zk;
    private int sessionTimeout;

    public DistributedLock(ZooKeeper zk, int sessionTimeout) {
        this.zk = zk;
        this.sessionTimeout = sessionTimeout;
    }

    public DistributedLock() throws IOException, InterruptedException {
        this.zk = ZookeeperClient.getInstance();
        this.sessionTimeout = ZookeeperClient.getSessionTimeout();
    }

    /**
     * 节点的监听器
     */
    class LockWatcher implements Watcher {

        public void process(WatchedEvent event) {
            /**
             * 如果节点被删除， 线程继续运行
             */
            if (event.getType() == Event.EventType.NodeDeleted)
                latch.countDown();
        }
    }

    public synchronized boolean lock() {
        /*
         获得锁时创建一个锁的根节点
         */
        createLockRootIfNotExists();

        try {
            /*
             获得锁时创建一个临时有序的节点
             */
            lockId = zk.create(LOCK_ROOT + "/", data,
                    ZooDefs.Ids.OPEN_ACL_UNSAFE,
                    CreateMode.EPHEMERAL_SEQUENTIAL);
            //打印线程名， 节点名
            System.out.println("thread " + Thread.currentThread().getName() +
                    " create the lock node: " + lockId + ", trying to get lock now");
            //获得根节点的所有子节点
            List<String> nodes = zk.getChildren(LOCK_ROOT, true);
            SortedSet<String> sortedNode = new TreeSet<String>();
            //拼接好节点名放入有序集合中
            for (String node : nodes) {
                sortedNode.add(LOCK_ROOT + "/" + node);
            }
            //获得有序集合中第一个节点名
            String first = sortedNode.first();
            //返回集合中从首元素到lockId的集合视图。
            SortedSet<String> lessThanMe = sortedNode.headSet(lockId);

            if (lockId.equals(first)) {
                //如果当前节点是第一个节点
                //打印线程名， 节点名
                //返回true
                System.out.println("thread " + Thread.currentThread().getName() +
                        " has get the lock, lockId is " + lockId);
                return true;
            } else if (!lessThanMe.isEmpty()) {
                //如果有比当前节点小的节点
                String prevLockId = lessThanMe.last();
                //为当前节点的前一个节点添加监听器
                zk.exists(prevLockId, new LockWatcher());
                //线程进入等待状态
                latch.await(sessionTimeout, TimeUnit.MILLISECONDS);
                //打印线程名， 节点名
                System.out.println("thread " + Thread.currentThread().getName() +
                        " has get the lock, lockId is " + lockId);
            }
        } catch (KeeperException e) {
            e.printStackTrace();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        return true;
    }

    public synchronized boolean unlock() {

        try {
            //打印线程名 节点名 锁id
            System.out.println("thread " + Thread.currentThread().getName() +
                    " unlock the lock: " + lockId + ", the node: " + lockId + " had been deleted");
            //删除节点
            zk.delete(lockId, -1);
            return true;
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (KeeperException e) {
            e.printStackTrace();
        } finally {
            try {
                zk.close();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        return false;
    }

    /**
     * 1- 创建一个锁的根节点
     */
    private void createLockRootIfNotExists() {
        try {
            /*
                1.1- 判断根节点是否存在， 不启用事件监听
             */
            Stat stat = zk.exists(LOCK_ROOT, false);
            if (stat == null) {
                /*
                    1.2- 如果根节点不存在， 创建根节点， 持久性的根节点。
                 */
                zk.create(LOCK_ROOT, data, ZooDefs.Ids.OPEN_ACL_UNSAFE, CreateMode.PERSISTENT);
            }
        } catch (KeeperException e) {
            e.printStackTrace();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) {
        final  CountDownLatch latch = new CountDownLatch(10);
        for (int i = 0; i < 10; i++) {
            new Thread(new Runnable() {
                public void run() {
                    DistributedLock lock = null;
                    try {
                        lock = new DistributedLock();
                        //计数减一并等待
                        latch.countDown();
                        latch.await();

                        lock.lock();
                        Thread.sleep((int)(Math.random()*300) + 200);
                    } catch (IOException e) {
                        e.printStackTrace();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    } finally {
                        if(lock != null) {
                            lock.unlock();
                        }
                    }

                }
            }).start();

        }
    }
}

```

### ZookeepeClient

```java
package com.uioqv.zookeeper;

import org.apache.zookeeper.WatchedEvent;
import org.apache.zookeeper.Watcher;
import org.apache.zookeeper.ZooKeeper;
import java.io.IOException;
import java.util.concurrent.CountDownLatch;
import java.util.concurrent.TimeUnit;

public class ZookeeperClient {
    private static String connectionString = "localhost:2181";
    private static int sessionTimeout = 10000;

    public static ZooKeeper getInstance() throws IOException, InterruptedException {
        final CountDownLatch connectedSignal = new CountDownLatch(1);
        ZooKeeper zk = new ZooKeeper(connectionString, sessionTimeout, new Watcher() {
            public void process(WatchedEvent event) {
                if(event.getState() == Event.KeeperState.SyncConnected) {
                    connectedSignal.countDown();
                }
            }
        });

        connectedSignal.await(sessionTimeout, TimeUnit.MILLISECONDS);
        return zk;
    }


    public static String getConnectionString() {
        return connectionString;
    }

    public static void setConnectionString(String connectionString) {
        ZookeeperClient.connectionString = connectionString;
    }

    public static int getSessionTimeout() {
        return sessionTimeout;
    }

    public static void setSessionTimeout(int sessionTimeout) {
        ZookeeperClient.sessionTimeout = sessionTimeout;
    }
}

```

