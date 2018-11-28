pip install apscheduler

四部分组成

触发器(trigger)包含调度逻辑，每一个作业有它自己的触发器，用于决定接下来哪一个作业会运行。除了他们自己初始配置意外，触发器完全是无状态的。

作业存储(job store)存储被调度的作业，默认的作业存储是简单地把作业保存在内存中，其他的作业存储是将作业保存在数据库中。一个作业的数据讲在保存在持久化作业存储时被序列化，并在加载时被反序列化。调度器不能分享同一个作业存储。

执行器(executor)处理作业的运行，他们通常通过在作业中提交制定的可调用对象到一个线程或者进城池来进行。当作业完成时，执行器将会通知调度器。

调度器(scheduler)是其他的组成部分。你通常在应用只有一个调度器，应用的开发者通常不会直接处理作业存储、调度器和触发器，相反，调度器提供了处理这些的合适的接口。配置作业存储和执行器可以在调度器中完成，例如添加、修改和移除作业。　


### 持久化
client = pymongo.MongoClient(Config.MONGO_CORE_HOST, Config.MONGO_CORE_PORT)
store = MongoDBJobStore(collection="tb_xx", database="db_xx", client=client)
scheduler = BackgroundScheduler()
scheduler.add_jobstore(store, 'mongo')
scheduler.start(）


### 添加作业
import time
from apscheduler.schedulers.blocking import BlockingScheduler
 
sched = BlockingScheduler()
 
@sched.scheduled_job('interval', seconds=5)
def my_job():
    print time.strftime('%Y-%m-%d %H:%M:%S', time.localtime(time.time()))
 
sched.start()


### cron定时调度（某一定时时刻执行）
(int|str) 表示参数既可以是int类型，也可以是str类型
(datetime | str) 表示参数既可以是datetime类型，也可以是str类型
 
year (int|str) – 4-digit year -（表示四位数的年份，如2008年）
month (int|str) – month (1-12) -（表示取值范围为1-12月）
day (int|str) – day of the (1-31) -（表示取值范围为1-31日）
week (int|str) – ISO week (1-53) -（格里历2006年12月31日可以写成2006年-W52-7（扩展形式）或2006W527（紧凑形式））
day_of_week (int|str) – number or name of weekday (0-6 or mon,tue,wed,thu,fri,sat,sun) - （表示一周中的第几天，既可以用0-6表示也可以用其英语缩写表示）
hour (int|str) – hour (0-23) - （表示取值范围为0-23时）
minute (int|str) – minute (0-59) - （表示取值范围为0-59分）
second (int|str) – second (0-59) - （表示取值范围为0-59秒）
start_date (datetime|str) – earliest possible date/time to trigger on (inclusive) - （表示开始时间）
end_date (datetime|str) – latest possible date/time to trigger on (inclusive) - （表示结束时间）
timezone (datetime.tzinfo|str) – time zone to use for the date/time calculations (defaults to scheduler timezone) -（表示时区取值）

eg：
表示2017年3月22日17时19分07秒执行该程序
sched.add_job(my_job, 'cron', year=2017,month = 03,day = 22,hour = 17,minute = 19,second = 07)

表示任务在6,7,8,11,12月份的第三个星期五的00:00,01:00,02:00,03:00 执行该程序
sched.add_job(my_job, 'cron', month='6-8,11-12', day='3rd fri', hour='0-3')
 
表示从星期一到星期五5:30（AM）直到2014-05-30 00:00:00
sched.add_job(my_job(), 'cron', day_of_week='mon-fri', hour=5, minute=30,end_date='2014-05-30')
 
表示每5秒执行该程序一次，相当于interval 间隔调度中seconds = 5
sched.add_job(my_job, 'cron',second = '*/5')


### interval 间隔调度（每隔多久执行）

weeks (int) – number of weeks to wait
days (int) – number of days to wait
hours (int) – number of hours to wait
minutes (int) – number of minutes to wait
seconds (int) – number of seconds to wait
start_date (datetime|str) – starting point for the interval calculation
end_date (datetime|str) – latest possible date/time to trigger on
timezone (datetime.tzinfo|str) – time zone to use for the date/time calculations

eg：

表示每隔3天17时19分07秒执行一次任务
sched.add_job(my_job, 'interval',days  = 03,hours = 17,minutes = 19,seconds = 07)

### date 定时调度（作业只会执行一次）


run_date (datetime|str) – the date/time to run the job at  -（任务开始的时间）
timezone (datetime.tzinfo|str) – time zone for run_date if it doesn’t have one already

eg：

The job will be executed on November 6th, 2009
sched.add_job(my_job, 'date', run_date=date(2009, 11, 6), args=['text'])

The job will be executed on November 6th, 2009 at 16:30:05
sched.add_job(my_job, 'date', run_date=datetime(2009, 11, 6, 16, 30, 5), args=['text'])
 
 

