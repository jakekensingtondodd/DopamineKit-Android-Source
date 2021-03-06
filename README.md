# What is DopamineKit?

DopamineKit provides wrappers for accessing the DopamineAPI and expressive UI reinforcements for your app.

Get your free API key at [http://dashboard.usedopamine.com/](http://dashboard.usedopamine.com/)

Learn more at [http://usedopamine.com](http://usedopamine.com)

### Looking for an Android Example App?

A simple "To Do List" Android App is included in the [DopamineKit-Android-HelloWorld repo](https://github.com/DopamineLabs/DopamineKit-Android-HelloWorld) to demonstrate how DopamineKit may be used in your code.

## Set up DopamineKit

  1. First, make sure you have received your API key and other credentials, which are in the configuration file __dopamineproperties.json__ automatically generated from the [Dopamine Developer Dashboard](http://dashboard.usedopamine.com). 

  2. Import the DopamineKit framework by using JCenter or Maven using the following line

  ```
  compile 'com.usedopamine:dopaminekit:3.1.01'
  ```
  , or by [directly downloading](
https://github.com/DopamineLabs/DopamineKit-Android-binary/) the library.

  3. Import the DopamineKit library

  ```java
  import com.usedopamine.dopaminekit.DopamineKit;
  ```
    
  4. Move __dopamineproperties.json__ into the directory _`app/src/main/res/raw`_

    ![Workspace snapshot](readme/TestApp with DopamineKit and dopamineproperties.png)
    *Shown from left to right: 
    `app/src/main/res/rawdopamineproperties.json`, 
    gradle import of DopamineKit 3.1.01, 
    java import of DopamineKit*
  
  5. Start using Dopamine! The main features of DopamineAPI are the `reinforce()` and `track()` functions. These should be added into the response functions of any _action_ to be reinforced or tracked.
  

###### DopamineKit.reinforce()

  -  For example, when a user marks a task as completed in a "To Do List" app or finishes a workout in a "Fitness" app, you should call `reinforce()`.

  ```java

    DopamineKit.reinforce(getBaseContext(), 
                          "some_action", 
                          new DopamineKit.ReinforcementCallback() {

        @Override
        public void onReinforcement(String reinforcement) {
                                        
            // Multiple reinforcements can help increase the surprise factor!
            
            // DopamineKit provides a standard reinforcement View called Candybar
            // but you can also use any UI components you made like
            // this.showInspirationalQuote() or this.showFunnyMeme()

            if(reinforcement.equals("stars")){
                DopamineKit.showCandyBar(getCurrentFocus(), CandyBar.Candy.STARS, "Out of this world!", Color.parseColor("#ffcc00"), CandyBar.LENGTH_SHORT);
            }
            else if(reinforcement.equals("medalStar")){
                DopamineKit.showCandyBar(getCurrentFocus(), CandyBar.Candy.MEDALSTAR, "Great job!", Color.parseColor("#339933"), CandyBar.LENGTH_SHORT);
            }
            else if(reinforcement.equals("thumbsUp")){
                DopamineKit.showCandyBar(getCurrentFocus(), CandyBar.Candy.THUMBSUP, "You go!", Color.parseColor("#336699"), CandyBar.LENGTH_SHORT);
            }
            else {
                // Show nothing! This is called a neutral response, 
                // and builds up the good feelings for the next surprise!
            }
        }
                                        
    });
                      
  ```  

###### DopamineKit.track()

  - The `track()` function is used to track other user actions. Using `track()` calls gives Dopamine a better understanding of user behavior, and will make your optimization and analytics better. 
  - Continuing the example, you could use the `track()` function to record when the user adds new tasks in your AddTaskActivity's `onCreate()` method for the  "To Do List" app, or  record `userCheckedDietHistory()` in the "Fitness" app.

  
  Let's track when a user adds a food item in a "Fitness" app. We will also add the calories for the item in the `metaData` field to gather richer information about user engagement in my app.
  
  ```java
    HashMap<String, String> metaData = new HashMap<String, String>();
    metaData.put("calories", "400");
    DopamineKit.track(getBaseContext(), "foodItemAdded", metaData);
   ```

  
  
## Super Users

There are additional parameters for the `track()` and `reinforce()` functions that are used to gather rich information from your app and better create a user story of better engagement.

========

####Tracking Calls

A tracking call should be used to record and communicate to DopamineAPI that a particular action has been performed by the user, each of these calls will be used to improve the reinforcement model used for the particular user. The tracking call itself is asynchronous and non-blocking. Failed tracking calls will not return errors, but will be noted in the log.

######General syntax

```
Dopamine.track(context, actionID, metaData, secondaryIdentity)
```

######Parameters:

 - `context: Context` - is used to get API credentials from `res/raw/dopamineproperties.json` of the context's package
 
 - `actionID: String` - is a unique name for the action that the user has performed

 - `metaData: Map<String, String>` - (optional) is any additional data to be sent to the API

 - `secondaryIdentity: String` - (optional) is an extra identifier (like login credentials) used to identify a particular user

========

####Reinforcement Calls

A reinforcement call should be used when the user has performed a particular action that you wish to become a 'habit', the reinforcement call will return the name of the feedback function that should be called to inform, delight or congratulate the user. The names of the reinforcement functions, the feedback functions and their respective pairings may be found and configured on the developer dashboard.

######General syntax

```
Dopamine.reinforce(context, actionID, metaData, secondaryIdentity, callback)
```

######Parameters:

 - `context: Context` - is used to get API credentials from `res/raw/dopamineproperties.json` of the context's package
 
 - `actionID: String` - is a unique name for the action that the user has performed

 - `metaData: Map<String, String>` - (optional) is any additional data to be sent to the API

 - `secondaryIdentity: String` - (optional) is an extra identifier (like login credentials) used to identify a particular user across apps

 - `callback: DopamineKit.ReinforcementCallback` - is an object on which `onReinforcement(String reinforcement)` is called when a response is received

The reinforcement call itself takes the actionID as a required parameter, as well as a DopamineKit.ReinforcementCallback object, which is triggered as a callback for the reinforcement response.

========

####dopamineproperties.json

`dopamineproperties.json ` _must_ be contained within the directory _`app/src/main/res/raw`_. This property list contains configuration variables needed to make valid calls to the API, all of which can be found on your developer dashboard:

 - `appID: String` - uniquely identifies your app, get this from your [developer dashboard](http://dev.usedopamine.com).

 - `versionID: String` -  this is a unique identifier that you choose that marks this implementation as unique in our system. This could be something like 'summer2015Implementation' or 'ClinicalTrial4'. Your `versionID` is what we use to keep track of what users are exposed to what reinforcement and how to best optimize that.

 - `inProduction: Bool` - indicates whether app is in production or development mode, when you're happy with how you're integrating Dopamine and ready to launch set this argument to `true`. This will activate optimized reinforcement and start your billing cycle. While set to `false` your app will receive dummy reinforcement, new users will not be registered with our system, and no billing occurs.

 - `productionSecret: String` - secret key for production

 - `developmentSecret: String` - secret key for development
