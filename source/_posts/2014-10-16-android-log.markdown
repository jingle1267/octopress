---
layout: post
title: "Android Log工具类"
date: 2014-10-16 22:38:12 +0800
comments: true
categories: 
keywords: Android Log工具类,LogUtils,LogHelper,Log
---

工欲善其事，必先利其器。在Android开发过程中，相信都会遇到这样的问题：

*   Log太多，不容易找到自己设置打印的内容
*   找到Log但是定位到Java源码有不是很方便
*   对Log打印需要设置Tag，大多时候Tag就是本类的类名，存在重复的工作
*   发布正式版本需要注释或者关掉很多Log开关
 
<!--more-->
 
参考Android源码，使用官方的Log打印方法（设置Tag），然后再Eclipse里面设置过滤标签，这样就可以查看某一个类的Log了，但是这样效率比较低下。基于上面的一些问题，我研究实现了一种简单易用的Log工具类。我的工具类主要有如下特点：

* 减少一个Log参数
* 调用某些Log方法，可以实现跳转到Java源代码的功能
* 采用此工具类，屏蔽Log只需要关闭Log总开关即可
 
###减少一个Log参数###

实现方法是采用<code>StackTraceElement</code>获取调用类的方法，代码如下：

``` java
private static String getClassName() {
	String result = "";
	StackTraceElement thisMethodStack = (new Exception()).getStackTrace()[2];
	result = thisMethodStack.getClassName();
	return result;
}
```

###跳转到源代码Log调用处###

实现双击Log跳转到Java源代码调用处，实现方法是参考Android Logcat错处日志，发现Eclipse实现跳转是因为Log中有<code>at MyClassName.MyMethodName(MClassName.java:行数)</code>。具体见代码：

``` java
/**
 * Realization of double click jump events.
 * 
 * @return
 */
private static String callMethodAndLine() {
	String result = "at ";
	StackTraceElement thisMethodStack = (new Exception()).getStackTrace()[2];
	result += thisMethodStack.getClassName() + ".";
	result += thisMethodStack.getMethodName();
	result += "(" + thisMethodStack.getFileName();
	result += ":" + thisMethodStack.getLineNumber() + ")  ";
	return result;
}
```

###屏蔽所有Log###

采用[LogUtils](https://github.com/jingle1267/android-utils/blob/master/src/com/worthed/util/LogUtils.java)工具类，只需要将此类中<code>DEBUG_LEVEL</code>设置的值大一点。

* 如果想要屏蔽所有的Log只需要将<code>DEBUG_LEVEL</code>设置为<code>Log.ASSERT</code>即可。
* 如果想要只显示Error则只需要将<code>DEBUG_LEVEL</code>设置为<code>Log.WARN</code>即可。
* 如果只想要显示Error和Warn则将<code>DEBUG_LEVEL</code>设置为<code>Log.INFO</code>即可。

##使用方法##

只需要用LogUtils替换掉Android系统的Log类，并去掉log方法的第一个参数，例如：

``` java
LogUtils.d(Object); // 次行代码将打印Object类的toString()
```


无代码无真相，下面是此工具类源码：

``` java
/**
 * Copyright 2014 Zhenguo Jin
 *
 * Licensed under the Apache License, Version 2.0 (the "License");
 * you may not use this file except in compliance with the License.
 * You may obtain a copy of the License at
 *
 *   http://www.apache.org/licenses/LICENSE-2.0
 *
 * Unless required by applicable law or agreed to in writing, software
 * distributed under the License is distributed on an "AS IS" BASIS,
 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 * See the License for the specific language governing permissions and
 * limitations under the License.
 */
package com.worthed.util;

import android.util.Log;

/**
 * This class can replace android.util.Log.
 * 
 * @author jingle1267@163.com
 * 
 * @description And you can turn off the log by set DEBUG_LEVEL = Log.ASSERT.
 * 
 */
public class LogUtils {

	/**
	 * Master switch.To catch error info you need set this value below Log.WARN
	 */
	public static final int DEBUG_LEVEL = 0;

	/**
	 * 'System.out' switch.When it is true, you can see the 'System.out' log.
	 * Otherwise, you cannot.
	 */
	public static final boolean DEBUG_SYSOUT = false;

	/**
	 * Send a {@link #VERBOSE} log message.
	 * 
	 * @param obj
	 */
	public static void v(Object obj) {
		if (Log.VERBOSE > DEBUG_LEVEL) {
			String tag = getClassName();
			String msg = obj != null ? obj.toString() : "obj == null";
			Log.v(tag, msg);
		}
	}

	/**
	 * Send a {@link #DEBUG_LEVEL} log message.
	 * 
	 * @param obj
	 */
	public static void d(Object obj) {
		if (Log.DEBUG > DEBUG_LEVEL) {
			String tag = getClassName();
			String msg = obj != null ? obj.toString() : "obj == null";
			Log.d(tag, msg);
		}
	}

	/**
	 * Send an {@link #INFO} log message.
	 * 
	 * @param obj
	 */
	public static void i(Object obj) {
		if (Log.INFO > DEBUG_LEVEL) {
			String tag = getClassName();
			String msg = obj != null ? obj.toString() : "obj == null";
			Log.i(tag, msg);
		}
	}

	/**
	 * Send a {@link #WARN} log message.
	 * 
	 * @param obj
	 */
	public static void w(Object obj) {
		if (Log.WARN > DEBUG_LEVEL) {
			String tag = getClassName();
			String msg = obj != null ? obj.toString() : "obj == null";
			Log.w(tag, msg);
		}
	}

	/**
	 * Send an {@link #ERROR} log message.
	 * 
	 * @param obj
	 */
	public static void e(Object obj) {
		if (Log.ERROR > DEBUG_LEVEL) {
			String tag = getClassName();
			String msg = obj != null ? obj.toString() : "obj == null";
			Log.e(tag, msg);
		}
	}

	/**
	 * What a Terrible Failure: Report a condition that should never happen. The
	 * error will always be logged at level ASSERT with the call stack.
	 * Depending on system configuration, a report may be added to the
	 * {@link android.os.DropBoxManager} and/or the process may be terminated
	 * immediately with an error dialog.
	 * 
	 * @param obj
	 */
	public static void wtf(Object obj) {
		if (Log.ASSERT > DEBUG_LEVEL) {
			String tag = getClassName();
			String msg = obj != null ? obj.toString() : "obj == null";
			Log.wtf(tag, msg);
		}
	}

	/**
	 * Send a {@link #VERBOSE} log message.
	 * 
	 * @param tag
	 *            Used to identify the source of a log message. It usually
	 *            identifies the class or activity where the log call occurs.
	 * @param msg
	 *            The message you would like logged.
	 */
	public static void v(String tag, String msg) {
		if (Log.VERBOSE > DEBUG_LEVEL) {
			Log.v(tag, msg);
		}
	}

	/**
	 * Send a {@link #DEBUG_LEVEL} log message.
	 * 
	 * @param tag
	 *            Used to identify the source of a log message. It usually
	 *            identifies the class or activity where the log call occurs.
	 * @param msg
	 *            The message you would like logged.
	 */
	public static void d(String tag, String msg) {
		if (Log.DEBUG > DEBUG_LEVEL) {
			Log.d(tag, msg);
		}
	}

	/**
	 * Send an {@link #INFO} log message.
	 * 
	 * @param tag
	 *            Used to identify the source of a log message. It usually
	 *            identifies the class or activity where the log call occurs.
	 * @param msg
	 *            The message you would like logged.
	 */
	public static void i(String tag, String msg) {
		if (Log.INFO > DEBUG_LEVEL) {
			Log.i(tag, msg);
		}
	}

	/**
	 * Send a {@link #WARN} log message.
	 * 
	 * @param tag
	 *            Used to identify the source of a log message. It usually
	 *            identifies the class or activity where the log call occurs.
	 * @param msg
	 *            The message you would like logged.
	 */
	public static void w(String tag, String msg) {
		if (Log.WARN > DEBUG_LEVEL) {
			Log.w(tag, msg);
		}
	}

	/**
	 * Send an {@link #ERROR} log message.
	 * 
	 * @param tag
	 *            Used to identify the source of a log message. It usually
	 *            identifies the class or activity where the log call occurs.
	 * @param msg
	 *            The message you would like logged.
	 */
	public static void e(String tag, String msg) {
		if (Log.ERROR > DEBUG_LEVEL) {
			Log.e(tag, msg);
		}
	}

	/**
	 * What a Terrible Failure: Report a condition that should never happen. The
	 * error will always be logged at level ASSERT with the call stack.
	 * Depending on system configuration, a report may be added to the
	 * {@link android.os.DropBoxManager} and/or the process may be terminated
	 * immediately with an error dialog.
	 * 
	 * @param tag
	 *            Used to identify the source of a log message.
	 * @param msg
	 *            The message you would like logged.
	 */
	public static void wtf(String tag, String msg) {
		if (Log.ASSERT > DEBUG_LEVEL) {
			Log.wtf(tag, msg);
		}
	}

	/**
	 * Send a {@link #VERBOSE} log message. And just print method name and
	 * position in black.
	 */
	public static void print() {
		if (Log.VERBOSE > DEBUG_LEVEL) {
			String tag = getClassName();
			String method = callMethodAndLine();
			Log.v(tag, method);
			if (DEBUG_SYSOUT) {
				System.out.println(tag + "  " + method);
			}
		}
	}

	/**
	 * Send a {@link #DEBUG_LEVEL} log message.
	 * 
	 * @param object
	 *            The object to print.
	 */
	public static void print(Object object) {
		if (Log.DEBUG > DEBUG_LEVEL) {
			String tag = getClassName();
			String method = callMethodAndLine();
			String content = "";
			if (object != null) {
				content = object.toString() + "                    ----    "
						+ method;
			} else {
				content = " ## " + "                ----    " + method;
			}
			Log.d(tag, content);
			if (DEBUG_SYSOUT) {
				System.out.println(tag + "  " + content + "  " + method);
			}
		}
	}

	/**
	 * Send an {@link #ERROR} log message.
	 * 
	 * @param object
	 *            The object to print.
	 */
	public static void printError(Object object) {
		if (Log.ERROR > DEBUG_LEVEL) {
			String tag = getClassName();
			String method = callMethodAndLine();
			String content = "";
			if (object != null) {
				content = object.toString() + "                    ----    "
						+ method;
			} else {
				content = " ## " + "                    ----    " + method;
			}
			Log.e(tag, content);
			if (DEBUG_SYSOUT) {
				System.err.println(tag + "  " + method + "  " + content);
			}
		}
	}

	/**
	 * Print the array of stack trace elements of this method in black.
	 * 
	 * @return
	 */
	public static void printCallHierarchy() {
		if (Log.VERBOSE > DEBUG_LEVEL) {
			String tag = getClassName();
			String method = callMethodAndLine();
			String hierarchy = getCallHierarchy();
			Log.v(tag, method + hierarchy);
			if (DEBUG_SYSOUT) {
				System.out.println(tag + "  " + method + hierarchy);
			}
		}
	}

	/**
	 * Print debug log in blue.
	 * 
	 * @param object
	 *            The object to print.
	 */
	public static void printMyLog(Object object) {
		if (Log.DEBUG > DEBUG_LEVEL) {
			String tag = "MYLOG";
			String method = callMethodAndLine();
			String content = "";
			if (object != null) {
				content = object.toString() + "                    ----    "
						+ method;
			} else {
				content = " ## " + "                ----    " + method;
			}
			Log.d(tag, content);
			if (DEBUG_SYSOUT) {
				System.out.println(tag + "  " + content + "  " + method);
			}
		}
	}

	private static String getCallHierarchy() {
		String result = "";
		StackTraceElement[] trace = (new Exception()).getStackTrace();
		for (int i = 2; i < trace.length; i++) {
			result += "\r\t" + trace[i].getClassName() + "."
					+ trace[i].getMethodName() + "():"
					+ trace[i].getLineNumber();
		}
		return result;
	}

	private static String getClassName() {
		String result = "";
		StackTraceElement thisMethodStack = (new Exception()).getStackTrace()[2];
		result = thisMethodStack.getClassName();
		return result;
	}

	/**
	 * Realization of double click jump events.
	 * 
	 * @return
	 */
	private static String callMethodAndLine() {
		String result = "at ";
		StackTraceElement thisMethodStack = (new Exception()).getStackTrace()[2];
		result += thisMethodStack.getClassName() + ".";
		result += thisMethodStack.getMethodName();
		result += "(" + thisMethodStack.getFileName();
		result += ":" + thisMethodStack.getLineNumber() + ")  ";
		return result;
	}

}
```


获取更多请参看我Github分享的Android工具类库[android-utils](https://github.com/jingle1267/android-utils)。