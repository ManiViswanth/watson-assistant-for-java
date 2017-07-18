# Overview
This is the Java library for Watson Conversation Service.
You can easily develop a chat bot that supports multiple users
 and easily be integrated with chat front end such as slack.

It is licensed under [MIT](https://opensource.org/licenses/MIT).

[![Maven Central](https://maven-badges.herokuapp.com/maven-central/org.riversun/wcs/badge.svg)](https://maven-badges.herokuapp.com/maven-central/org.riversun/wcs)

# Quick Start

Java Example for Watson Conversation<br>

## Example:Slack Bot Integration

Slack integration example here.Easy to integrate with Slack.

<img src="https://riversun.github.io/wcs/img/watson_slacklet_en.gif" width=75%>

https://github.com/riversun/watson-java-slackbot

----

## Example:Send message simply

Send message to Watson and set Watson's context variables from Java program

### How to run

- Download workspace file for Watson conversation from here
https://riversun.github.io/wcs/org.riversun.WcsContextTest.zip

- Unzip and import this workspace file into your watson conversation environment.
<img src="https://riversun.github.io/wcs/img/wcs_import_workspace.jpg">

<img src="https://riversun.github.io/wcs/img/wcs_example_ws.jpg">

- Check your username,password,workspaceId like below.
<img src="https://riversun.github.io/wcs/img/wcs_check_workspaceid.jpg">

- Put username,password,workspaceId in example code below.
  WATSON_CONVERSATION_USERNAME,WATSON_CONVERSATION_PASSWORD,WATCON_CONVERSATION_WORKSPACE_ID

- Run this code.
  (with maven dependency see below)

**WcsExample01.java**
```java

import org.riversun.wcs.WcsClient;
import com.ibm.watson.developer_cloud.conversation.v1.model.MessageResponse;

public class WcsExample01 {

    // Edit here
    private static final String WATSON_CONVERSATION_USERNAME = "EDIT_ME_USERNAME_HERE";
    private static final String WATSON_CONVERSATION_PASSWORD = "EDIT_ME_PASSWORD_HERE";
    private static final String WATCON_CONVERSATION_WORKSPACE_ID = "EDIT_ME_WORKSPACE_ID_HERE";

    public static void main(String[] args)
    {

        // Set unique id for each user to be able to talk to Watson at the same
        // time
        String wcsClientId = "dummy_user_id";

        // Create client for Watson Conversation Username, password,
        // workspaceId can be confirmed on the workspace screen of Watson
        // Conversation's workspace
        WcsClient watson = new WcsClient(
                WATSON_CONVERSATION_USERNAME,
                WATSON_CONVERSATION_PASSWORD,
                WATCON_CONVERSATION_WORKSPACE_ID);

        // Perform initial access (call welcome node)
        // Call #startConversation for the first access to workspace
        MessageResponse wcsWelcomeRes = watson.startConversation(wcsClientId);

        System.out.println("FROM WATSON:" + wcsWelcomeRes.getTextConcatenated(""));

        // The context variable is sent
        // at the next access and reflected in Watson
        // (wcsClientId,name,value)
        watson.put(wcsClientId, "myRemoteParam", "I need you!");

        // Send text to Watson
        final String myMessage01 = "Hi! Watson";
        MessageResponse wcsRes01 = watson.sendMessage(wcsClientId, myMessage01);
        System.out.println("FROM WATSON:" + wcsRes01.getTextConcatenated(""));

        // Send text and get response as text
        final String myMessage02 = "Hello! Watson";
        String wcsResText = watson.sendMessageForText(wcsClientId, myMessage02);
        System.out.println("FROM WATSON:" + wcsResText);
    }
}

```

### About this example code

```java
Private static final String WCS_USERNAME = "EDIT_ME_USERNAME_HERE" ; // username
Private static final String WCS_PASSWORD = "EDIT_ME_PASSWORD_HERE" ; // password
Private static final String WCS_WORKSPACE_ID = "EDIT_ME_WORKSPACE_ID_HERE" ; // workspaceId
```

First, set authentication information to access Watson.
Edit the following part of the code and replace it with the workspaceId, username, password that we checked in the deploy pane.

```java
 String wcsClientId = "dummy_user_id" ;
```

When using Helper Library for java(https://github.com/riversun/watson-conversation-service-for-java) to operate Watson Conversation,
Create a unique userID (variable named "wcsClientId", note that it is completely different from workspaces username)  for each user to identify the user.
Since the conversation state needs to be held for each user, it is necessary to set a unique ID for each user described as below.

<img src="https://riversun.github.io/wcs/img/qt_04_005.png">

Therefore, as with this example, even one user can access Watson with userID.
(In Watson's internal processing, an ID called conversationId is assigned in units of one conversation session rather than a user unit)

```java
 WcsClient watson = new WcsClient ( username , password , workspaceId );
```

The class of "WcsClient" is the main class and can communicate with Watson easily.

```java
MessageResponse wcsWelcomeRes = watson.startConversation(wcsClientId);
System.out.println("FROM WATSON:" + wcsWelcomeRes.getTextConcatenated(""));
```

In many cases the first node should be a welcome node , use **#startConversation** method for the first call.
After that we receive the response from Watson as MessageResponse .
#getTextConcatenated is for getting a response from Watson as a String.
(Because the actual response from Watson is a JSON String array type.)


```java
 watson.put(wcsClientId, "myRemoteParam", "I need you!");
```

If you use **WcsClient#put (user ID, name of context variabl, value of context variable)**,
you can assign a value to Watson's Context variable from the Java logic.
I set the value " I need you! " to the Context variable named myRemoteParam here.
In fact, the Context variable will be reflected the next time java logic access to Watson.

```java
final String myMessage01 = "Hi! Watson";
 MessageResponse wcsRes01 = watson.sendMessage(wcsClientId, myMessage01);
 System.out.println("FROM WATSON:" + wcsRes01.getTextConcatenated(""));
```

We send a text message to Watson with **#sendMessage(userID, message)**.
At this timing, the Context variable will be reflected on Watson.
Here we send the message **"Hi! Watson"** from Java Logic.
The response of the **Show_Context_node** on Watson Conversation says **You say "<? input.text ?>". The value set by Java Program is "<? context['myRemoteParam']?>"**
Therefore, the response from Watson received by the Java logic is as follows.
```
You say "Hi! Watson". The value set by Java Program is "I need you!"
```

### Execution Result

Then you can get this in console.

```
FROM WATSON:Hi,there!
FROM WATSON:You say "Hi! Watson". The value set by Java Program is "I need you!"
FROM WATSON:You say "Hello! Watson". The value set by Java Program is "I need you!"
```

### Dependency
**Maven**
```xml
<dependency>
<groupId>org.riversun</groupId>
<artifactId>wcs</artifactId>
<version>1.0.2</version>
</dependency>
```

----


## Example:Obtain Watson's Context variable from Java logic

 This example code is obtaining the Context variable set on Watson Conversation.

**WcsExample02.java**
```java
public class WcsExample02 {

    // EDIT_HERE
    private static final String WATSON_CONVERSATION_USERNAME = "EDIT_ME_USERNAME_HERE";
    private static final String WATSON_CONVERSATION_PASSWORD = "EDIT_ME_PASSWORD_HERE";
    private static final String WATCON_CONVERSATION_WORKSPACE_ID = "EDIT_ME_WORKSPACE_ID_HERE";

    public static void main(String[] args)
    {

        String wcsClientId = "dummy_user_id";

        WcsClient watson = new WcsClient(
                WATSON_CONVERSATION_USERNAME,
                WATSON_CONVERSATION_PASSWORD,
                WATCON_CONVERSATION_WORKSPACE_ID);

        watson.startConversation(wcsClientId);

        String myParam01 = watson.getAsString(wcsClientId, "myParam01");
        System.out.println("myParam01=" + myParam01);

        String myParam02 = watson.getAsString(wcsClientId, "myParam02");
        System.out.println("myParam02=" + myParam02);

        Integer myParam03 = watson.getAsInteger(wcsClientId, "myParam03");
        System.out.println("myParam03=" + myParam03);

        Boolean myParam04 = watson.getAsBoolean(wcsClientId, "myParam04");
        System.out.println("myParam04=" + myParam04);

        Map<String, Object> myParam05 = watson.getAsMap(wcsClientId, "myParam05");

        String subParam01 = (String) myParam05.get("subParam01");
        System.out.println("myParam05.subParam01=" + subParam01);

        String subParam02 = (String) myParam05.get("subParam02");
        System.out.println("myParam05.subParam02=" + subParam02);

    }
}

```

### About this example code

The Context variable set in the **Welcome_node** in workspace is as follows.

```JSON
{
  "context": {
    "myParam01": "banana",
    "myParam02": "apple",
    "myParam03": 7777,
    "myParam04": true,
    "myParam05": {
      "subParam01": "orange",
      "subParam02": "lemon"
    }
  }
}
```


For example, to obtain the above Context variable " myParam01 " on the Java side, do as follows.

```Java
  String myParam01 = watson.getAsString(wcsClientId, "myParam01");
````

Also, for complex(nested) object such as the Context variable "myParam 05",
The value is acquired as Map as follows.

```Java
 Map<String, Object> myParam05 = watson.getAsMap(wcsClientId, "myParam05");
```

Method to get Watson Conversation's Context variable from Java logic

| Type of Context Variablees | Definition of the Methos | Type of return |
|-----------|------------|------------|
| String | #getAsString(userId,name of context variables) | String |
| Integer|#getAsInteger(userId,name of context variables)|Integer|
| Double|#getAsDouble(userId,name of context variables)|Double|
| Boolean|#getAsBoolean(userId,name of context variables)|Boolean|
| complex type(nested JSON object)|#getAsMap(userId,name of context variables)|Map|

----


# Example:Chat Bot GUI

This is the example of Java Chat Bot GUI for Watson Conversation  

https://github.com/riversun/watson-examples-java-chatbot

<img src="https://riversun.github.io/wcs/img/wcs_java_chat.jpg">

----

# More Examples

More examples here.

https://github.com/riversun/watson-conversation-java-examples

