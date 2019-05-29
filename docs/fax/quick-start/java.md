no_breadcrumb:true

# Fax Java Quick Start

Welcome to the RingCentral Platform. RingCentral is the leading unified communications platform. From one system developers can integrate with, or build products around all the ways people communicate today: SMS, voice, fax, chat and meetings.

In this Quick Start, we are going to help you send a fax message using our Fax API, which sends a cover page and a high resolution attachment to a recipient. Let's get started.

## Create an App

The first thing we need to do is create an app in the RingCentral Developer Portal. This can be done quickly by clicking the "Create Fax App" button below. Just click the button, enter a name and description if you choose, and click the "Create" button. If you do not yet have a RingCentral account, you will be prompted to create one.

<a target="_new" href="https://developer.ringcentral.com/new-app?name=Fax+Quick+Start+App&desc=A+simple+app+to+demo+sending+a+Fax+on+RingCentral&public=false&type=ServerOther&carriers=7710,7310,3420&permissions=Faxes&redirectUri=" class="btn btn-primary">Create Fax App</a>
<a class="btn-link btn-collapse" data-toggle="collapse" href="#create-app-instructions" role="button" aria-expanded="false" aria-controls="create-app-instructions">Show detailed instructions</a>

<div class="collapse" id="create-app-instructions">
<ol>
<li><a href="https://developer.ringcentral.com/login.html#/">Login or create an account</a> if you have not done so already.</li>
<li>Go to Console/Apps and click 'Create App' button.</li>
<li>Give your app a name and description, then click Next.</li>
<li>On the second page of the create app wizard enter the following:
  <ul>
  <li>Select 'Private' for Application Type.</li>
  <li>Select 'Server-only (No UI)' for Platform Type.</li>
  </ul>
  </li>
<li>On the third page of the create app wizard, select the following permissions:
  <ul>
    <li>Faxes</li>
  </ul>
  </li>
<li>We are using Password Flow authentication, so leave "OAuth Redirect URI" blank.</li>
</ol>
</div>

When you are done, you will be taken to the app's dashboard. Make note of the Client ID and Client Secret. We will be using those momentarily.

## Send a Fax message

### Create a Java project (using Eclipse IDE)

* Create a new Java project
* Select the Gradle Project wizard
* Enter project name "Send_Fax"
* Open the <tt>build.gradle</tt> file and add the RingCentral Java SDK to the project as shown below:

```json hl_lines="4",linenums="1"
dependencies {
    // ...

    compile 'com.ringcentral:ringcentral:0.6.4'

    // Use JUnit test framework
    testImplementation 'junit:junit:4.12'
}
```

### Create a new Java Class

Select "File -> New -> Class" to create a new Java class named "Send_Fax"

```java
package Send_Fax;

public class Send_Fax {

	public static void main(String[] args) {
		// TODO Auto-generated method stub

	}
}
```

### Edit the file "Send_Fax.java".

Be sure to edit the variables in ALL CAPS with your app and user credentials. Be sure to also set the recipient's phone number.

```java
package Send_Fax;

import java.io.IOException;

import com.ringcentral.*;
import com.ringcentral.definitions.*;
import com.google.gson.Gson;

public class Send_Fax {
    static String RECIPIENT_NUMBER = "<ENTER PHONE NUMBER>";
    static String RINGCENTRAL_CLIENTID = "<ENTER CLIENT ID>";
    static String RINGCENTRAL_CLIENTSECRET = "<ENTER CLIENT SECRET>";

    static String RINGCENTRAL_USERNAME = "<YOUR ACCOUNT PHONE NUMBER>";
    static String RINGCENTRAL_PASSWORD = "<YOUR ACCOUNT PASSWORD>";
    static String RINGCENTRAL_EXTENSION = "<YOUR EXTENSION, PROBABLY ";

  	static RestClient restClient;

  	public static void main(String[] args) {
    		try {
          sendFax();
    		} catch (RestException | IOException e) {
    			e.printStackTrace();
    		}
  	}

  	public static void sendFax() throws RestException, IOException{
        restClient = new RestClient(RINGCENTRAL_CLIENTID, RINGCENTRAL_CLIENTSECRET, RINGCENTRAL_SERVER);
        restClient.authorize(RINGCENTRAL_USERNAME, RINGCENTRAL_EXTENSION, RINGCENTRAL_PASSWORD);
                
        String body = "{"
        	    		+ "\"to\": [{ \"phoneNumber\": \"" + RECIPIENT_NUMBER + "\"}],"
        	    		+ "\"faxResolution\": \"High\","
        	    		+ "\"coverPageText\": \"This is a demo Fax page from Java\"}";

        RequestBody requestBody = new MultipartBody.Builder().setType(MultipartBody.MIXED)
        	 .addPart(RequestBody.create(MediaType.parse("application/json"), body))
        	 .addFormDataPart("attachment", "test.jpg", RequestBody.create(MediaType.parse("image/jpeg"), Files.readAllBytes(Paths.get("./src/test/resources/test.jpg"))))
        	 .build();
        ResponseBody response = restClient.restApi().account().extension().fax().post(requestBody);
        FaxResponse resp = new Gson().fromJson(response.string(), FaxResponse.class);
        System.out.println("SMS sent. Message status: " + response.messageStatus);
    }
}
```

### Run Your App

You are almost done. Now run your app from Eclipse.

## Graduate Your App

Congratulations on creating your first RingCentral application. The last step is to graduate your application. We recommend [going through this process](../../../basics/production) for your first application so you can understand the steps to take in the future, but also to come to appreciate the care taken by RingCentral to ensure that only high-quality apps are allowed into our production environment.