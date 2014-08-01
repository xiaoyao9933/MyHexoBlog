comments: true
date: 2012-08-11 13:56:22+00:00
layout: post
slug: android-ics-current-running-modify
title: 如何让你的Android 4.0 ICS/JB最近任务界面只显示真正运行的程序
categories:
- 手机移动平台
tags:
- android
- ics
- 反汇编
- 最近任务

---
## 引言


在Android 4.0+的系统里，长按住HOME键，可以激活出“最近任务列表”，非常fashion，手一拽动，程序就会被彻底关闭。本来这个功能非常好，但是对于中国人的使用习惯来说，我们仿佛更适应windows tab或者或者Symbian的任务切换。也就是说我们更喜欢任务界面里显示的都是真正运行的程序，而不是显示很久前已经关闭的最近运行程序，让我们重新花费大量时间去运行。本文将从源代码修改和反汇编修改两种方法，讲解修改。只要理解了程序的源代码，其实修改起来并不难，希望以后Android 4.0的ROM开发者们，都可以采用我这一修改方法，让人们更舒适的使用Android。

<!-- more -->
## 源代码分析及修改法


我找到4.0.4的一份[源代码](http://grepcode.com/file/repository.grepcode.com/java/ext/com.google.android/android/4.0.4_r1.2/com/android/systemui/recent/RecentsPanelView.java?av=f)，在这个主Activity的RecentsPanelView.java类里，我们可以追踪show（）以后的各种调用，其中最明显的一处便是：

``` java
public void show(boolean show, boolean animate,
ArrayList recentTaskDescriptions) {
if (show) {
// Need to update list of recent apps before we set visibility so this view's
refreshRecentTasksList(recentTaskDescriptions);
```

其调用了 [refreshRecentTasksList](http://grepcode.com/file/repository.grepcode.com/java/ext/com.google.android/android/4.0.4_r1.2/com/android/systemui/recent/RecentsPanelView.java#RecentsPanelView.refreshRecentTasksList%28java.util.ArrayList%29)方法

``` java
private void refreshRecentTasksList(ArrayList recentTasksList) {
if (mRecentTasksDirty) {
if (recentTasksList != null) {
mRecentTaskDescriptions = recentTasksList;
} else {
mRecentTaskDescriptions = mRecentTasksLoader.getRecentTasks();
}
mListAdapter.notifyDataSetInvalidated();
updateUiElements(getResources().getConfiguration());
mRecentTasksDirty = false;
}
}
```

在这个方法里，其又调用了[getRecentTasks()](http://grepcode.com/file/repository.grepcode.com/java/ext/com.google.android/android/4.0.4_r1.2/com/android/systemui/recent/RecentTasksLoader.java#RecentTasksLoader.getRecentTasks%28%29)函数在RecentTasksLoader.java中，这就是我们要修改的地方,我简单注释下：

``` java
// return a snapshot of the current list of recent apps
ArrayList getRecentTasks() {
cacelLoadingThumbnails();
ArrayList tasks = new ArrayList<TaskDescription>();
final PackageManager pm = mContext.getPackageManager();
final ActivityManager am = (ActivityManager)//调取服务
mContext.getSystemService(Context.ACTIVITY_SERVICE);
final List<ActivityManager.RecentTaskInfo> recentTasks =
am.getRecentTasks(MAX_TASKS, ActivityManager.RECENT_IGNORE_UNAVAILABLE);//调取最近活动列表
ActivityInfo homeInfo = new Intent(Intent.ACTION_MAIN).addCategory(Intent.CATEGORY_HOME)
.resolveActivityInfo(pm, 0);
HashSet<Integer> recentTasksToKeepInCache = new HashSet<Integer>();//最前端的任务不显示
int numTasks = recentTasks.size();
// skip the first task - assume it's either the home screen or the current activity.
final int first = 1;
recentTasksToKeepInCache.add(recentTasks.get(0).persistentId);
for (int i = first, index = 0; i < numTasks && (index < MAX_TASKS); ++i) {//开始轮询
final ActivityManager.RecentTaskInfo recentInfo = recentTasks.get(i);//获取单个taskinfo
//在这里可以添加代码
TaskDescription item = createTaskDescription(recentInfo.id,
recentInfo.persistentId, recentInfo.baseIntent,
recentInfo.origActivity, recentInfo.description, homeInfo);
if (item != null) {
tasks.add(item);
++index;
}
}
// when we're not using the TaskDescription cache, we load the thumbnails in the
// background
loadThumbnailsInBackground(new ArrayList<TaskDescription>(tasks));//读缩略图
return tasks;
}
```

所以我们为了剔除掉已经被关闭掉的程序，只需在轮询处加入代码如下
``` java
if(recentInfo.id＜0)
{
continue;
}
```

也就是说，我们在这里判断下recentInfo.id的正负，我们知道，不运行的程序，id号会为 -1,我们踢掉这些代码就可以了。


## 反编译及回编译法


上面修改源代码的方法虽然看似简单，但实际上，很多人也不会编译rom，编起来耗时又多，而且我们也不知道自己的ROM是不是已经被第三方修改过很多了，反汇编法还是比较适合主流。具体反编译工具的使用方法，我只能是简单介绍了，我的是ubuntu环境下，先配置好jre环境（windows下也差不多，配置好java 环境，加个 java+命令就执行了）。下载好baksmali，和smali两个工具,假设程序分别为[baksmali.jar，smali.jar](http://code.google.com/p/smali/downloads/list)。smali语法参见：[Dalvik opcodes](http://www.blogjava.net/midea0978/archive/2012/01/04/367847.html)，下面是CM9的改法。



	
  1. 把手机下的/System/app/SystemUI.apk程序想办法搞到电脑上来，方法很多。
  2. 给baksmali，和smali两个工具赋权，+x就可以了,大概如下chmod +x baksmali.jar	
  3. 对SystemUI.apk解包，用解压工具打开，拖出里面的classes.dex到同一目录。
  4. 反汇编  ./baksmali.jar classes.dex -x -o classes
  5. 打开classes目录下的com/android/systemui/recent/RecentTasksLoader.smali文件
  6. 搜索这段代码 iget v2, v15, Landroid/app/ActivityManager$RecentTaskInfo;->id:I
  7. 在其下行加入：if-ltz v2, :cond_7c	
  8. （上面的那个cond_7c是单次循环结束的入口，可能会因机而异，您可以自己搜索：add-int/lit8 v10, v10, 0x1这句，找到入口，替换cond_7c即可）	
  9. 回编译: ./smali.jar classes/ -o classes.dex
  10. 把回编译的classes.dex拖回原来的SystemUI.apk替换即可。
  11. 想办法把SystemUI.apk放回手机，记得赋权rw-r--r-。
  12. 重启手机后，看看彻底关闭一个程序后，是不是任务列表里就没有了，大功告成。




## 特殊情况





	
  1. 有的ROM天生SystemUI.apk就被分割成odex了，这种apk里是没有classes.dex的，所以你需要根据相关教程将odex合并后再弄
  2. **cm10，也就是JELLY BEAN，4.1.1由于源代码做了较大改动，处理获取任务列表的地方在[这里](http://grepcode.com/file/repository.grepcode.com/java/ext/com.google.android/android/4.1.1_r1/com/android/systemui/recent/RecentTasksLoader.java?av=f)loadTasksInBackground()这个函数里，这也可见JB之所以较顺滑的原因就是，很多任务挪到了背景缓存，背景执行。改的方法类似4.0的，反汇编的方法稍有变动：修改的文件将是 RecentTasksLoader$1.smali ，搜索这句 iget v2, v0, Landroid/app/ActivityManager$RecentTaskInfo;->id:I ，下面添加这句，if-ltz v2,:cond_e6  。其他都一样。**




## 终语


本文可能不适合新手自己修改，但是可以给rom定制者提供一个好的方法。我下一步的计划是添加一个新的手势，如向上滑动，关闭全部程序，欢迎继续关注我的博客。




