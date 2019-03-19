### 事件循环机制 （Event Loop） ###


#### 一、JavaScript是单线程单并发语言 ####


- 什么是单线程

	主程序只有一个线程，即同一时间片断内其只能执行单个任务。

- 为什么选择单线程？

	JavaScript的主要用途是与用户互动，以及操作DOM。这决定了它只能是单线程，否则会带来很复杂的同步问题。

- 单线程意味着什么？

	单线程就意味着，所有任务都需要排队，前一个任务结束，才会执行后一个任务。如果前一个任务耗时很长，后一个任务就需要一直等着。这就会导致IO操作（耗时但cpu闲置）时造成性能浪费的问题。

- 如何解决单线程带来的性能问题？

	答案是异步！主线程完全可以不管IO操作，暂时挂起处于等待中的任务，先运行排在后面的任务。等到IO操作返回了结果，再回过头，把挂起的任务继续执行下去。于是，所有任务可以分成两种，一种是同步任务（synchronous），另一种是异步任务（asynchronous）

	注： 当主线程阻塞时，任务队列仍然是能够被推入任务的



#### 二、事件循环Event Loop ####

1、JavaScript 内存模型图

![pic](/pic/jsjmm.jpg)

2、JavaScript 代码执行机制

- 所有同步任务都在主线程上的栈中执行。

- 主线程之外，还存在一个"任务队列"（task queue）。只要异步任务有了运行结果，就在"任务队列"之中放置一个事件。

- 一旦"栈"中的所有同步任务执行完毕，系统就会读取"任务队列"，选择出需要首先执行的任务（由浏览器决定，并不按序）。


3、事件循环（EventLoop）


![pic](/pic/eventloop.jpg)


#### 三、异步任务 ####

1.MacroTask（宏观Task）
setTimeout, setInterval, setImmediate, requestAnimationFrame, I/O, UI rendering

2.MicroTask（微观任务）
process.nextTick, Promise, Object.observe, MutationObserver


规范：

- 每个浏览器环境，至多有一个event loop。
- 一个event loop可以有1个或多个MacroTask queue，而仅有一个 MicroTask Queue。
- 一个task queue是一列有序的task, 每个task定义时都有一个task source，从同一个task source来的task必须放到同一个task queue，从不同源来的则被添加到不同队列。
- tasks are scheduled，所以浏览器可以从内部到JS/DOM，保证动作按序发生。
- Microtasks are scheduled，Microtask queue 在当前 task queue 的结尾执行。microtask中添加的microtask也被添加到Microtask queue的末尾并处理。

	注： event loop的每个turn，是由浏览器决定先执行哪个task queue。这允许浏览器为不同的task source设置不同的优先级，比如为用户交互设置更高优先级来使用户感觉流畅。


#### 四、实例 ####

```javascript

function ELoop() {
        // 当前任务
        let p = new Promise((resolve, reject) => {
            console.log("current Task")
            resolve();
        });
        let nextP;

        setTimeout(() => {
            console.log("MacroTask_1");
            nextP.then(() => {
                // 第一次执行时，这段代码并没有执行到。
                console.log("MicroTask_promise_1"); //第一个MicroTask
            })
            console.log("MacroTask_1 end")
        }, 0) // 第一个 MacroTask

        setTimeout(() => {
            console.log("MacroTask_2");
            console.log("MacroTask_2 end")
        }, 0) // 第二个MacroTask

        nextP = p.then(() => {
            console.log("MicroTask_promise_2"); //第一个MicroTask
        }).then(() => {
            console.log("MicroTask_promise_3"); // 第二个MicroTask
        })

        console.log("current Task end")
    }

    ELoop();

    /**输出结果：
    current Task
    current Task end
    MicroTask_promise_2
    MicroTask_promise_3
    MacroTask_1
    MacroTask_1 end
    MicroTask_promise_1
    MacroTask_2
    MacroTask_2 end
    **/
```