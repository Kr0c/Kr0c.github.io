---
layout: post
title: Quartz学习笔记
tags: Java
---

# Quartz学习笔记

## 一、入门

基本概念：
- `Job/JobDetail`：任务，定时任务需要实现Job接口，JobDetail描述了Job的相关信息。
- `Trigger`：触发器，定义任务触发的规则，有SimpleTrigger和CronTrigger两个子类。前者用于固定时间间隔或固定重复次数的任务，后者用于日历相关的重复时间间隔，如每周星期一凌晨3:00运行。
- `Scheduler`：调度器，代表一个Quartz的独立运行容器，Trigger和JobDetail要注册到Scheduler中才会生效，也就是让调度器知道有哪些触发器和任务，才能进行按规则进行调度任务。

### 1.导入quartz、spring依赖包
```
<dependency>
    <groupId>org.quartz-scheduler</groupId>
    <artifactId>quartz</artifactId>
    <version>2.2.1</version>
</dependency>
<dependency>
    <groupId>org.quartz-scheduler</groupId>
    <artifactId>quartz-jobs</artifactId>
    <version>2.2.1</version>
</dependency>
```

### 2.编写任务类，实现Job接口
```
public class MyJob implements Job {

    public void execute(JobExecutionContext jobExecutionContext) throws JobExecutionException {
        DateFormat format = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        String time = format.format(new Date());
        System.out.println(time + " 执行定时任务...");
}
```

### 3.编写任务调度类
```
public class MyScheduler {

    public static void main(String args[]) {
        SchedulerFactory schedulerFactory = new StdSchedulerFactory();
        Scheduler scheduler;
        try {
            // 创建一个JobDetail实例，绑定Job实现类MyJob
            JobDetail jobDetail = JobBuilder.newJob(MyJob.class).withIdentity("job1", "jobgroup1").build();
            // 创建一个Trigger，定义触发规则：立即执行，每10s运行一次
            Trigger trigger = TriggerBuilder.newTrigger()
                    .withSchedule(SimpleScheduleBuilder.repeatSecondlyForever(10)).startNow().build();
            // 通过schedulerFactory获取一个任务调度器
            scheduler = schedulerFactory.getScheduler();
            // 将trigger和job注册到调度器scheduler
            scheduler.scheduleJob(jobDetail, trigger);
            // 启动调度器
            scheduler.start();
        } catch (SchedulerException e) {
            e.printStackTrace();
        }
    }
}
```

### 4.执行结果
```
2017-04-17 10:31:25 执行定时任务...
2017-04-17 10:31:30 执行定时任务...
2017-04-17 10:31:35 执行定时任务...
2017-04-17 10:31:40 执行定时任务...
```

## 二、整合Spring

在（一）的基础上，再导入以下包：
```
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>4.2.3.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-core</artifactId>
    <version>4.2.3.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context-support</artifactId>
    <version>4.2.3.RELEASE</version>
</dependency>
```

### 1.编写任务类
这里通过两种任务来体现JobDetail里的两种FactoryBean的使用区别
```
任务类1（实现Job接口）

public class Job1 implements Job {

    public void execute(JobExecutionContext jobExecutionContext) throws JobExecutionException {
        DateFormat format = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        String time = format.format(new Date());
        System.out.println(time + " 执行定时任务1...");
    }
}
```

```
任务类2（未实现Job接口）

public class Job2 {

    public run() {
        DateFormat format = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        String time = format.format(new Date());
        System.out.println(time + " 执行定时任务2...");
    }
}
```

### 2.编写spring-quartz.xml
在配置文件中定义JobDetail, Trigger和Scheduler，这里需要注意两种JobDetail的定义方式。
这里的触发器使用了SimpleTrigger和可以实现复杂任务的触发器CronTrigger。
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:p="http://www.springframework.org/schema/p"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">

	<!--Job-->
	<bean id="job2" class="task.job.Job2" />
	
	<!--JobDetail-->
	<!--实现Job接口的任务-->
	<bean id="job1Detail" class="org.springframework.scheduling.quartz.JobDetailFactoryBean">
		<property name="jobClass" value="task.job.Job1" />
	</bean>
	<!--未实现Job接口的任务-->
	<bean id="job2Detail" class="org.springframework.scheduling.quartz.MethodInvokingJobDetailFactoryBean">
		<property name="targetObject" ref="job2" />
		<property name="targetMethod" value="execute" />
	</bean>
	
	<!--Trigger-->
	<bean id="job1Trigger" class="org.springframework.scheduling.quartz.SimpleTriggerFactoryBean">
		<property name="jobDetail" ref="job1Detail" />
		<property name="repeatInterval" value="5000" />
	</bean>
	<bean id="job2Trigger" class="org.springframework.scheduling.quartz.CronTriggerFactoryBean">
		<property name="jobDetail" ref="job2Detail" />
		<property name="cronExpression" value="0/5 * * * * ?" />
	</bean>

	<!--Scheduler-->
	<bean id="scheduler" class="org.springframework.scheduling.quartz.SchedulerFactoryBean">
		<property name="triggers">
			<list>
				<ref bean="job1Trigger" />
				<ref bean="job2Trigger" />
			</list>
		</property>
	</bean>
</beans>
```

### 3.测试代码
```
public class Test {

    public static void main(String[] args) throws SchedulerException {
        ApplicationContext context = new ClassPathXmlApplicationContext("spring-quartz.xml");
        Scheduler scheduler = (Scheduler) context.getBean("scheduler");
        scheduler.start();
    }
}
```

### 4.执行结果
```
2017-04-17 11:02:25 执行定时任务1...
2017-04-17 11:02:25 执行定时任务2...
2017-04-17 11:02:30 执行定时任务1...
2017-04-17 11:02:30 执行定时任务2...
2017-04-17 11:02:35 执行定时任务1...
2017-04-17 11:02:35 执行定时任务2...
```
