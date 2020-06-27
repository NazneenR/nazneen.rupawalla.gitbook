# Linking a text having a phone number in Android

The requirement was simple: On clicking the phone number in a error dialog, it should open up a dialer on the Android phone.  
But while implementing this requirement, I learnt three new things:

## Use of a spannable

Let us see how to implement the above requirement without a spannable to understand it better.  
Inorder to show the number, without any text, we can add autoLink attribute, in the layout file:  
`<TextView    
android:id="@+id/contact_no"    
android:autoLink="phone”/>`

If you use autoLink, you can click on it & it will open up a dialler.  
So, taking this into consideration, if you have to show a string containing a number, it should be the same right?  
`<TextView    
android:id="@+id/contact_no_text"    
android:autoLink="phone”/>`  
and this worked perfectly fine….until it was verified on API 16\(JELLY\_BEAN\) :\(

{% hint style="warning" %}
**On phones with JELLY\_BEAN, the link was not clickable. So number with text is not clickable with autoLink.**
{% endhint %}

How do we solve this?

By using a spannable

```java
public static void setTextViewToContainCertainClickablePart(
                                          Activity activity,
                                          TextView textView,
                                          int preStringResourceId,
                                          int clickableResourceId,
                                          int postStringResourceId,
                                          Intent intent) {
  final String clickableString = activity
                         .getString(clickableResourceId);
  String displayString = activity
                         .getString(preStringResourceId) + " " +
                         clickableString + " " + 
                         activity.getString(postStringResourceId);
  SpannableString spannableString = new SpannableString(displayString);

  ClickableSpan clickableSpan = getClickableSpan(activity, intent);

  int startIndex = displayString.indexOf(clickableString);
  int endIndex = startIndex + clickableString.length();

  spannableString.setSpan(clickableSpan, startIndex, endIndex,
                         SPANNED.SPAN_EXCLUSIVE_EXCLUSIVE);
  spannableString.setSpan(new ForegroundColorSpan(activity.getResources(),
                                                  getColor(R.color.default_href)),
                                                  startIndex, endIndex, 
                                                  SPANNABLE.SPAN_EXCLUSIVE_EXCLUSIVE);
  textView.setText(spannableString);
  textView.setMovementMethod(LinkMovementMethod.getInstance());                                                
}
```

```java
private static ClickableSpanWithoutUnderline getClickableSpan(
                                final Activity activity,
                                final Intent intent) {
  return new ClickableSpanWithoutUnderline() {
    @Override
    public void onClick(View textView) {
      activity.startActivity(intent);
    }
  }
}
```

```java
private abstract class ClickableSpanWithoutUnderline extends ClickableSpan {
  @Override
  public void updateDrawState(TextPaint ds){
    super.updateDrawState(ds);
    ds.setUnderlineText(false);  
  }
}
```

This solved the issue we faced on phones with JELLY\_BEAN.

## ACTION\_VIEW vs ACTION\_DIAL

If you observe, we pass an intent to the `setTextView` method. This is the implicit intent, to invoke a dialler.  
I would think, `ACTION_DIAL` is the intent to use, because that is what the name suggests.  
It worked fine, until it was realised that on clicking the number, the default dialler was opening up, and not Skype or other apps that could make a phone call.  
And I wondered why..?  
Interestingly, `ACTION_VIEW` with the data as “tel” invoked Skype, and I found it rather weird that Skype app has subscribed with the `ACTION_VIEW` in the intent filter whereas Android has given an option of `ACTION_DIAL`.  
and then it clicked me, but of course, Skype and other apps are catering to tablets without diallers too.

{% hint style="warning" %}
**`ACTION_DIAL` cannot be used for tablets that do not provide telephony support and hence `ACTION_VIEW` with the correct data is to be used.**
{% endhint %}

Hence the intent which was previously used `ACTION_DIAL` now became:  
`Intent intent = new Intent(Intent.ACTION_VIEW, Uri.parse("tel:" + getResources().getString(R.string.message_number)));`

## **Use of non breaking unicode for Hyphen**

So the number we had to add was of the format - +1-800-900-6008  
In the strings.xml, we added -+1&\#8211;800&\#8211;325&\#8211;6008, as you should use unicode instead of plain text.  
But this resulted in the breaking of the number due to the screen resolution of the phone:  
Please call +1-  
800-900-6008

{% hint style="success" %}
**We fixed it by using non breaking hyphen code -**
{% endhint %}

In the strings.xml, we used,  
+1&\#8209;800&\#8209;325**&\#8209;**6008 and that worked fine.

Phew, and it was just a requirement of adding a clickable phone number to a screen  
:\) :P

Hope you find this post helpful. If you know of a better approach, do let us know.

