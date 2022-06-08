-
- 公平锁和非公平锁
  默认为非公平锁，效率高
  
  
  ```java
  /** Synchronizer providing all implementation mechanics */
  private final Sync sync;
  public ReentrantLock() {
      // 默认非公平锁
      sync = new NonfairSync();
  }
  public ReentrantLock(boolean fair) {
      sync = fair ? new FairSync() : new NonfairSync();
  }
  ```
  ReentrantLock源码实现公平锁和非公平锁
  ```java
  //ReentrantLock.FairSync
  static final class FairSync extends Sync {
      final void lock() {
          acquire(1);
      }
      //来自AbstractQueuedSynchronizer.acquire(int arg)
      public final void acquire(int arg) {
          if (!tryAcquire(arg) &&
              acquireQueued(addWaiter(Node.EXCLUSIVE), arg))
              selfInterrupt();
      }
      protected final boolean tryAcquire(int acquires) {
          final Thread current = Thread.currentThread();
          int c = getState();
          if (c == 0) {
              // 1. 和非公平锁相比，这里多了一个判断：是否有线程在等待
              if (!hasQueuedPredecessors() &&
                  compareAndSetState(0, acquires)) {
                  setExclusiveOwnerThread(current);
                  return true;
              }
          }
          else if (current == getExclusiveOwnerThread()) {
              int nextc = c + acquires;
              if (nextc < 0)
                  throw new Error("Maximum lock count exceeded");
              setState(nextc);
              return true;
          }
          return false;
      }
  }
  ```
  非公平锁的实现
  ```java
  //ReentrantLock.NonfairSync
  static final class NonfairSync extends Sync {
      final void lock() {
          // 2. 和公平锁相比，这里会直接先进行一次CAS，成功就返回了
          if (compareAndSetState(0, 1))
              setExclusiveOwnerThread(Thread.currentThread());
          else
              acquire(1);
      }
      // AbstractQueuedSynchronizer.acquire(int arg)
      public final void acquire(int arg) {
          if (!tryAcquire(arg) &&
              acquireQueued(addWaiter(Node.EXCLUSIVE), arg))
              selfInterrupt();
      }
      protected final boolean tryAcquire(int acquires) {
          return nonfairTryAcquire(acquires);
      }
  }
  /**
   * Performs non-fair tryLock.  tryAcquire is implemented in
   * subclasses, but both need nonfair try for trylock method.
   */
  final boolean nonfairTryAcquire(int acquires) {
      final Thread current = Thread.currentThread();
      int c = getState();
      if (c == 0) {
          // 这里没有对阻塞队列进行判断
          if (compareAndSetState(0, acquires)) {
              setExclusiveOwnerThread(current);
              return true;
          }
      }
      else if (current == getExclusiveOwnerThread()) {
          int nextc = c + acquires;
          if (nextc < 0) // overflow
              throw new Error("Maximum lock count exceeded");
          setState(nextc);
          return true;
      }
      return false;
  }
  ```
-