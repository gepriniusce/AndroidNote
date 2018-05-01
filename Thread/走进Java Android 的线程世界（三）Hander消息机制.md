# ActivityThread-->Android的主线程。

![main方法.png](http://upload-images.jianshu.io/upload_images/3481116-7d8bd23009f9a293.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![H.png](http://upload-images.jianshu.io/upload_images/3481116-56edef4be507d152.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![HcodeToString.png](http://upload-images.jianshu.io/upload_images/3481116-c9a2a5a07ce6891c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![HhandleMessage.png](http://upload-images.jianshu.io/upload_images/3481116-258fccabf71f429d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

main方法通过Looper获取主线程、结束主线程。
H就是ActivityThread的Handler，主要包含了四大组件的启动和停止等过程。
通过ApplicationThread和AMS进行进程间通信，AMS以进程间通信的方式完成ActivityThread的请求后会回到ApplicationThread中的Binder方法，然后ApplicationThread会向H发送消息，H收到消息后会将ApplicationThread中的逻辑切换到ActivityThread中执行，即切换到主线程中去执行，这个过程就是主线程的消息循环模式。

# Looper

![子线程中用Handler的方法.png](http://upload-images.jianshu.io/upload_images/3481116-664f637f04877768.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![prepare.png](http://upload-images.jianshu.io/upload_images/3481116-ce07bd20f9662dbf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![loop.png](http://upload-images.jianshu.io/upload_images/3481116-498b488cf5f92b02.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![quit.png](http://upload-images.jianshu.io/upload_images/3481116-d33d93809a7218f8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# MessageQueue
## next
```
    Message next() {
        // Return here if the message loop has already quit and been disposed.
        // This can happen if the application tries to restart a looper after quit
        // which is not supported.
        final long ptr = mPtr;
        if (ptr == 0) {
            return null;
        }

        int pendingIdleHandlerCount = -1; // -1 only during first iteration
        int nextPollTimeoutMillis = 0;
        for (;;) {
            if (nextPollTimeoutMillis != 0) {
                Binder.flushPendingCommands();
            }

            nativePollOnce(ptr, nextPollTimeoutMillis);

            synchronized (this) {
                // Try to retrieve the next message.  Return if found.
                final long now = SystemClock.uptimeMillis();
                Message prevMsg = null;
                Message msg = mMessages;
                if (msg != null && msg.target == null) {
                    // Stalled by a barrier.  Find the next asynchronous message in the queue.
                    do {
                        prevMsg = msg;
                        msg = msg.next;
                    } while (msg != null && !msg.isAsynchronous());
                }
                if (msg != null) {
                    if (now < msg.when) {
                        // Next message is not ready.  Set a timeout to wake up when it is ready.
                        nextPollTimeoutMillis = (int) Math.min(msg.when - now, Integer.MAX_VALUE);
                    } else {
                        // Got a message.
                        mBlocked = false;
                        if (prevMsg != null) {
                            prevMsg.next = msg.next;
                        } else {
                            mMessages = msg.next;
                        }
                        msg.next = null;
                        if (DEBUG) Log.v(TAG, "Returning message: " + msg);
                        msg.markInUse();
                        return msg;
                    }
                } else {
                    // No more messages.
                    nextPollTimeoutMillis = -1;
                }

                // Process the quit message now that all pending messages have been handled.
                if (mQuitting) {
                    dispose();
                    return null;
                }

                // If first time idle, then get the number of idlers to run.
                // Idle handles only run if the queue is empty or if the first message
                // in the queue (possibly a barrier) is due to be handled in the future.
                if (pendingIdleHandlerCount < 0
                        && (mMessages == null || now < mMessages.when)) {
                    pendingIdleHandlerCount = mIdleHandlers.size();
                }
                if (pendingIdleHandlerCount <= 0) {
                    // No idle handlers to run.  Loop and wait some more.
                    mBlocked = true;
                    continue;
                }

                if (mPendingIdleHandlers == null) {
                    mPendingIdleHandlers = new IdleHandler[Math.max(pendingIdleHandlerCount, 4)];
                }
                mPendingIdleHandlers = mIdleHandlers.toArray(mPendingIdleHandlers);
            }

            // Run the idle handlers.
            // We only ever reach this code block during the first iteration.
            for (int i = 0; i < pendingIdleHandlerCount; i++) {
                final IdleHandler idler = mPendingIdleHandlers[i];
                mPendingIdleHandlers[i] = null; // release the reference to the handler

                boolean keep = false;
                try {
                    keep = idler.queueIdle();
                } catch (Throwable t) {
                    Log.wtf(TAG, "IdleHandler threw exception", t);
                }

                if (!keep) {
                    synchronized (this) {
                        mIdleHandlers.remove(idler);
                    }
                }
            }

            // Reset the idle handler count to 0 so we do not run them again.
            pendingIdleHandlerCount = 0;

            // While calling an idle handler, a new message could have been delivered
            // so go back and look again for a pending message without waiting.
            nextPollTimeoutMillis = 0;
        }
    }
```

![enqueueMessage.png](http://upload-images.jianshu.io/upload_images/3481116-0bf4649c0aaacf0f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![quit.png](http://upload-images.jianshu.io/upload_images/3481116-24e8604352edc6de.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![removeAllFutureMessagesLocked.png](http://upload-images.jianshu.io/upload_images/3481116-5d5ea7e82ff7fdc7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


# Message

![callback.png](http://upload-images.jianshu.io/upload_images/3481116-bbd6d7059ed80d12.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![obtain.png](http://upload-images.jianshu.io/upload_images/3481116-d43349792b25bd99.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![recycle.png](http://upload-images.jianshu.io/upload_images/3481116-d540f8c278d2d032.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![recycleUnchecked.png](http://upload-images.jianshu.io/upload_images/3481116-d0e541a820c2ba5c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# Handler

![Handler构造方法.png](http://upload-images.jianshu.io/upload_images/3481116-d1aee30f717cad98.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![Callback.png](http://upload-images.jianshu.io/upload_images/3481116-394d18fc1ab4fe69.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![dispatchMessage.png](http://upload-images.jianshu.io/upload_images/3481116-cbdf1b47a7bd561c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![post.png](http://upload-images.jianshu.io/upload_images/3481116-374fc575654989ea.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![循环.png](http://upload-images.jianshu.io/upload_images/3481116-af3b86977bb56d25.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![handleMessage.png](http://upload-images.jianshu.io/upload_images/3481116-a7c012650e558041.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![send.png](http://upload-images.jianshu.io/upload_images/3481116-01f808c119f65c9c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![sendMessageAtTime.png](http://upload-images.jianshu.io/upload_images/3481116-3b5e83a26dea9117.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![enqueueMessage.png](http://upload-images.jianshu.io/upload_images/3481116-161cc7e7b12b1aad.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# ThreadLocal

![hash.png](http://upload-images.jianshu.io/upload_images/3481116-d9293f54f0edb4d4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![set.png](http://upload-images.jianshu.io/upload_images/3481116-632ad292243c02ed.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![initializeValues.png](http://upload-images.jianshu.io/upload_images/3481116-0e294445e1b74c33.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![values.png](http://upload-images.jianshu.io/upload_images/3481116-a09018487e271091.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![values.put.png](http://upload-images.jianshu.io/upload_images/3481116-2850902b24fe2346.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#Hander机制

![总.png](http://upload-images.jianshu.io/upload_images/3481116-26611e703d72cce4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)