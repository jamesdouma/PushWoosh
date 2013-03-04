Cordova Android Pushwoosh Push Notifications plugin
===================================================

cross-platform PushWoosh for Cordova / PhoneGap

follows the Cordova Plugin spec https://github.com/alunny/cordova-plugin-spec

so that it works with Pluginstall https://github.com/alunny/pluginstall

## LICENSE

	The MIT License
	
	Copyright (c) 2012 Adobe Systems, inc.
	portions Copyright (c) 2012 Olivier Louvignes
	
	Permission is hereby granted, free of charge, to any person obtaining a copy
	of this software and associated documentation files (the "Software"), to deal
	in the Software without restriction, including without limitation the rights
	to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
	copies of the Software, and to permit persons to whom the Software is
	furnished to do so, subject to the following conditions:
	
	The above copyright notice and this permission notice shall be included in
	all copies or substantial portions of the Software.
	
	THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
	IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
	FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
	AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
	LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
	OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
	THE SOFTWARE.

## Manual Installation for Android

## Manual Installation for iOS

Copy the following files to your project's Plugins folder:

	HtmlWebViewController.h 
	HtmlWebViewController.m 
	PWGetNearestZoneRequest.h 
	PWGetNearestZoneRequest.m 
	PWPushStatRequest.h 
	PWPushStatRequest.m 
	PWRegisterDeviceRequest.h 
	PWRegisterDeviceRequest.m 
	PWRequest.h 
	PWRequest.m 
	PWRequestManager.h 
	PWRequestManager.m 
	PWSendBadgeRequest.h 
	PWSendBadgeRequest.m 
	PWSetTagsRequest.h 
	PWSetTagsRequest.m 
	PushNotificationManager.h 
	PushNotificationManager.m

Add a reference for this plugin to the plugins dictionary in **Cordova.plist**:

	<key>PushNotification</key>
	<string>PushNotification</string>

Add the **PushNotification.js** script to your assets/www folder (or within a javascripts folder within your www folder) and reference it in your main index.html file.

    <script type="text/javascript" charset="utf-8" src="PushNotification.js"></script>

## Automatic Installation
This plugin is based on [pluginstall](https://github.com/alunny/pluginstall). to install it to your app,
simply execute pluginstall as follows;

	pluginstall [PLATFORM] [TARGET-PATH] [PLUGIN-PATH]
	
	where
		[PLATFORM] = ios or android
		[TARGET-PATH] = path to folder containing your phonegap project
		[PLUGIN-PATH] = path to folder containing this plugin

For additional info, take a look at the [Cordova Pluginstall Specification](https://github.com/alunny/cordova-plugin-spec)


## Plugin API
In the Examples folder you will find a sample implementation showing how to interact with the PushPlugin. Modify it to suit your needs.

First create the plugin instance variable.

	var pushNotification;
	
When deviceReady fires, get the plugin reference

	pushNotification = window.plugins.pushNotification;


#### register
This should be called as soon as the device becomes ready. On success, you will get a call to tokenHandler (iOS), or  onNotificationGCM (Android), allowing you to obtain the device token or registration ID, respectively. Those values will typically get posted to your intermediary push server so it knows who it can send notifications to.

	if (device.platform == 'android' || device.platform == 'Android') {

	} else {
        pushNotification.registerDevice({ alert:true, badge:true, sound:true,  appname: "...your app name...", pw_appid : "CDAPP-00000" },
                                        function(status) {
                                            var pushToken = status;
                                            showStatusMsg('push token: ' + JSON.stringify(pushToken));
                                        },
                                        function(status) {
                                            showStatusMsg(JSON.stringify(['failed to register', status]));
                                        });

	}
	
**ecb** - event callback that gets called when your device receives a notification

   document.addEventListener('push-notification', function(event) {
                              var notification = event.notification;
                              pushNotification.setApplicationIconBadgeNumber(0);
                              var title = notification.title;
                              var userData = notification.userdata;
                              navigator.notification.alert(notification.aps.alert);
                              
                              if(typeof(userData) != "undefined") {
                              showStatusMsg('user data: ' + JSON.stringify(userData));
                              }
                              });
    

## Test Environment
The notification system consists of several interdependent components.

	1) The client application which runs on a device and receives notifications.
	2) The notification service provider (APNS for Apple, GCM for Google)
	3) Intermediary servers that collect device IDs from clients and push notifications through APNS and/or GCM. (PushWoosh.com)
	
This plugin and its target Cordova application comprise the client application which will communicate with PushWoosh which, in turn, communicates with APNS and GCM.

**Prerequisites**.

- Ruby gems is installed and working.

- You have successfully built a client with this plugin, on both iOS and Android and have installed them on a device.


#### 1) Register for a PushWoosh account.
	
	a) Set up profile for your app with the Pushwoosh admin interface
	
#### 2) (iOS) [Follow this tutorial](http://www.raywenderlich.com/3443/apple-push-notification-services-tutorial-part-12) to create a file called ck.pem.
Start at the section entitled "Generating the Certificate Signing Request (CSR)", and substitute your own Bundle Identifier, and Description.
	
	a) go the this plugin's Example folder and open pushAPNS.rb in the text editor of your choice.
	b) set the APNS.pem variable to the path of the ck.pem file you just created
	c) set APNS.pass to the password associated with the certificate you just created. (warning this is cleartext, so don't share this file)
	d) set device_token to the token for the device you want to send a push to. (you can run the Cordova app / plugin in Xcode and extract the token from the log messages)
	e) save your changes.
	
#### 3) (Android) [Follow these steps](http://developer.android.com/guide/google/gcm/gs.html) to generate a project ID and a server based API key.

	a) go the this plugin's Example folder and open pushGCM.rb in the text editor of your choice.
	b) set the GCM.key variable to the API key you just generated.
	c) set the destination variable to the Registration ID of the device. (you can run the Cordova app / plugin in on a device via Eclipse and extract the regID from the log messages)
	
#### 4) Push a notification

	a) Go to the Push form for your app within the Pushwoosh admin interface.
	b) Enter message and press click "Woosh!"

If all went well, you should see a notification show up on each device. If not, make sure you are not being blocked by a firewall, and that you have internet access. Check and recheck the token id, the registration ID and the certificate generating process.

In a production environment, your app, upon registration, would send the device id (iOS) or the registration id (Android), to your intermediary push server. For iOS, the push certificate would also be stored there, and would be used to authenticate push requests to the APNS server. When a push request is processed, this information is then used to target specific apps running on individual devices.


## Notes

## Additional Resources

## Acknowledgments
