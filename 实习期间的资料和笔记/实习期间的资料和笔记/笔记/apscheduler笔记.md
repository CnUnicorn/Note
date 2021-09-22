[toc]

# 简介

APScheduler是一个定时执行代码的python库。ApScheduler本身不是一个守护线程或者服务（service），它主要是运行在应用内部。



它有三种内置的定时模式：

* Cron-style风格的定时（可以选择开始和结束的时间）
* 间隔执行模式（在指定间隔的时间上运行代码，可以选择开始和结束的时间）
* 只执行一次（在指定日期/时间，运行一次代码）



APScheduler支持后端将任务存储在内存和数据库中。任务如果存储在数据库中，在scheduler重启的时候，任务能够保持原来的状态，并运行那些在scheduler下线时应该运行的任务。

APScheduler支持任务存储在：

* 内存
* SQLAlchemy
* MongoDB
* Redis
* RethinkDB
* ZooKeeper

APScheduler也整合了几个常用的框架：

* asyncio（PEP 3156）
* gevent
* Tornado
* Twisted
* Qt（也可以使用PyQt，PySide2或者PySide）



# 基本概念

APScheduler有四种基本组件：

* triggers

  包含定时逻辑，每个工作都有自己的trigger决定何时运行这个任务

* job stores

  工作默认存放在内存中，也有可能存放在数据库中，如果存放在数据库中，这些工作的数据在存取时，就需要序列化和反序列化。Job stores扮演中间人的角色，用来在后端保存、加载、更新和搜索工作。Job stroes不能被多个scheduler共享。

* executors

  用来控制任务的执行，将任务添加到线程或者进程池（process pool）中。

* schedulers

  用来联系其他的组件，一般不会直接操作triggers、job stores和executors。通常是使用schedulers提供的接口去配置其他的组件。



# scheduler、job store、trigger和executor的具体类



## scheduler

scheduler的配置方法：

1. 可以将配置放在一个字典中，或者作为参数传入
2. 可以先实例化scheduler，然后添加工作，后面再配置scheduler，这种方法在所有环境中都最灵活。



- [:class:`~apscheduler.schedulers.blocking.BlockingScheduler`](https://github.com/agronholm/apscheduler/blob/3.x/docs/userguide.rst#id3): use when the scheduler is the only thing running in your process

  **使用BlockingScheduler时，需要在应用程序初始化完成后，再调用scheduler的start方法**，其他scheduler在调用start方法之后，还可以继续初始化应用程序

- [:class:`~apscheduler.schedulers.background.BackgroundScheduler`](https://github.com/agronholm/apscheduler/blob/3.x/docs/userguide.rst#id5): use when you're not using any of the frameworks below, and want the scheduler to run in the background inside your application

- [:class:`~apscheduler.schedulers.asyncio.AsyncIOScheduler`](https://github.com/agronholm/apscheduler/blob/3.x/docs/userguide.rst#id7): use if your application uses the asyncio module

- [:class:`~apscheduler.schedulers.gevent.GeventScheduler`](https://github.com/agronholm/apscheduler/blob/3.x/docs/userguide.rst#id9): use if your application uses gevent

- [:class:`~apscheduler.schedulers.tornado.TornadoScheduler`](https://github.com/agronholm/apscheduler/blob/3.x/docs/userguide.rst#id11): use if you're building a Tornado application

- [:class:`~apscheduler.schedulers.twisted.TwistedScheduler`](https://github.com/agronholm/apscheduler/blob/3.x/docs/userguide.rst#id13): use if you're building a Twisted application

- [:class:`~apscheduler.schedulers.qt.QtScheduler`](https://github.com/agronholm/apscheduler/blob/3.x/docs/userguide.rst#id15): use if you're building a Qt application



## job store

默认存放在内存中：

* [:class:`~apscheduler.jobstores.memory.MemoryJobStore`](https://github.com/agronholm/apscheduler/blob/3.x/docs/userguide.rst#id17)

if you need your jobs to persist over scheduler restarts or application crashes, then your choice usually boils down to what tools are used in your programming environment. If, however, you are in the position to choose freely, then [:class:`~apscheduler.jobstores.sqlalchemy.SQLAlchemyJobStore`](https://github.com/agronholm/apscheduler/blob/3.x/docs/userguide.rst#id19) on a [PostgreSQL](http://www.postgresql.org/) backend is the recommended choice due to its strong data integrity protection.



## executor

默认情况是**线程池**：

* [:class:`~apscheduler.executors.pool.ThreadPoolExecutor`](https://github.com/agronholm/apscheduler/blob/3.x/docs/userguide.rst#id21) 

如果是CPU密集型任务，可以选择进程池：

*  [:class:`~apscheduler.executors.pool.ProcessPoolExecutor`](https://github.com/agronholm/apscheduler/blob/3.x/docs/userguide.rst#id23)



也可以两个都用，把进程池执行器作为二级执行器（secondary executor）。



## trigger

- [:mod:`~apscheduler.triggers.date`](https://github.com/agronholm/apscheduler/blob/3.x/docs/userguide.rst#id25): use when you want to run the job just once at a certain point of time
- [:mod:`~apscheduler.triggers.interval`](https://github.com/agronholm/apscheduler/blob/3.x/docs/userguide.rst#id27): use when you want to run the job at fixed intervals of time
- [:mod:`~apscheduler.triggers.cron`](https://github.com/agronholm/apscheduler/blob/3.x/docs/userguide.rst#id29): use when you want to run the job periodically at certain time(s) of day

