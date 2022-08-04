---
description: >-
  My learning around creating a synthetic task stack of multiple activities with
  TaskStackBuilder or startActivities
---

# Building a synthetic task stack of multiple activities in Android

The scenario we wanted to implement in Android was as follows:

1. Launch activity A - SplashScreen
2. Activity B should be launched immediately- SecondActivity
3. On back of Activity B, we should launch Activity C. - ParentActivity

To implement this, we used **`TaskStackBuilder`**

In `onCreate`of Activity A (Splash Screen), we implemented:

```java
 Intent secondActivity = new Intent(this, SecondActivity.class);
 TaskStackBuilder stackBuilder = TaskStackBuilder
 .create(secondActivity);
  stackBuilder.addNextIntentWithParentStack(secondActivity.class);

 PendingIntent pendingIntent = stackBuilder
 .getPendingIntent( 0, PendingIntent.FLAG_UPDATE_CURRENT);
 pendingIntent.send(this, 0,  secondActivity);
```

Instead of using `.addNextIntentWithParentStack` you can also use:

```java
stackBuilder.addParentStack(secondActivity.class);
stackBuilder.addNextIntent(secondActivity);
```

**In the manifest we have to define the parent:**

{% tabs %}
{% tab title="If you are using below API 16:" %}
```java
<activity android:name =".SecondActivity"> 
  <meta-data
    android:name="android.support.PARENT_ACTIVITY"
    android:value=".ThirdActivity"/>
</activity>
```
{% endtab %}

{% tab title="API 16+ " %}
```java
<activity android:name=".SecondActivity"
    android:parentActivityName=".ThirdActivity"
.../>
```
{% endtab %}
{% endtabs %}

{% hint style="danger" %}
But we faced a issue - **Two tasks were getting created for the app when the above scenario was executed.**
{% endhint %}

Looking at the internals, we saw the `TaskStackBuilder.create`returns a new TaskStackBuilder for launching a fresh task stack consisting of a series of activities. Internally, `Intent.FLAG_ACTIVITY_NEW_TASK` is set.\
Then we realized that our activity C (ParentActivity) has a particular taskAffinity; different from the default one.

By default all activities have the same taskAffinity and therefore even though we set `Intent.FLAG_ACTIVITY_NEW_TASK`, the new Activity will still be started in the same task**.**\
But, if the new Activity has a different taskAffinity, the new Activity will be started in a new task.

{% hint style="success" %}
**Solution:** We added the particular taskAffinity to Activity A and B.\
**\*\*But a better approach, we heard is use** `startActivities`.\*\*
{% endhint %}

On digging further, we understood it can be used to create a synthetic back stack, when starting a new Task. You want to have a ready-made back stack, so that the back key navigates hierarchically inside this task.

**`startActivities(intents)`** is available from API 11

So we created intents for Activity B and C and this worked as a breeze\
`Intents[] intents = {parentActivity, secondActivity}`  \
`startActivities(intents);`

{% hint style="danger" %}
**UPDATE:** We found a bug in our app while using `startActivities`
{% endhint %}

As stated above we had to launch ActivityA(Splash Screen) and then launch ActivityB. While launching the Splash Screen, we put the application in the background, the app crashes. What we understood is, as we are starting ActivityB from the non UI thread, it led to the crash. [Another related raised issue with startActivities.](https://code.google.com/p/android/issues/detail?id=70194)

{% hint style="info" %}
Therefore, we reverted to using TaskStackBuilder.
{% endhint %}

**References:**

[http://developer.android.com/reference/android/support/v4/content/ContextCompat.html#startActivities%28android.content.Context,%20android.content.Intent%5B%5D%29 ](http://developer.android.com/reference/android/support/v4/content/ContextCompat.html#startActivities%28android.content.Context,%20android.content.Intent%5B%5D%29)\
[http://stackoverflow.com/questions/21374610/use-of-android-taskaffinity](http://stackoverflow.com/questions/21374610/use-of-android-taskaffinity)

We learned quite some new things while solving the above mentioned issue and therefore thought of sharing our learnings.

Hope you find this post helpful.\
Do you all know of any other better approach. Let us know.
