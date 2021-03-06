## Module 9: Adding Offline Support to Web Applications

## Lab: Adding Offline Support to Web Applications

#### Scenario

The conference organizers are concerned that the venue has poor Wi-Fi coverage in some locations, meaning that attendees might not always be able to access the conference website on their tablets and laptops. The Schedule page is especially important because attendees need to know when sessions are running.

You have decided to make parts of the web application available offline by using the offline web application features of HTML5. After an attendee has visited the online website once, their browser will have downloaded and cached the important pages. If a Wi-Fi connection is unavailable, the attendee will still be able to view the website by using the cached information.

#### Objectives

After completing this lab, you will be able to:

- Use the Application Cache API to make web pages available offline.
- Use the Local Storage API to persist user data locally between browser sessions.

#### Lab Setup

Estimated Time: **60 minutes**

### Exercise 1: Caching Offline Data by Using the Application Cache API

#### Scenario

In this exercise, you will make the **Home**, **About**, **Schedule**, and **Location** pages available offline.

First, you will complete the creation of an application manifest file, which lists all of the files that should be cached for offline access. Next, you will reference the manifest file from the **Home**, **About**, **Schedule**, and **Location** pages. Then, you will write JavaScript code that adapts the page navigation, hiding links to pages that are not available offline. Finally, you will run the application and view the **Schedule** page. You will stop the web server and then reload the **Schedule** page to verify that it works offline.

#### Task 1: Configure the application cache manifest.

1.	Configure Microsoft Edge to enable support for application caching:
- Start Microsoft Edge.
- In the Microsoft Edge, press F10 to display the menu bar.
- On the **Tools** menu, click **Internet options**.
- In the **Internet Options** dialog box, click **Settings**.
- In the **Website Data Settings** dialog box, click the **Caches and databases** tab.
- Select the **Allow website caches and databases** check box, and then click **OK**.
- In the **Internet Options** dialog box, click **OK**.
- Close Microsoft Edge.
2.	Start Visual Studio and open the **ContosoConf.sln** solution from the **Allfiles\Mod09\Labfiles\Starter\Exercise 1** folder.
3.	Open the **appcache.manifest** file. This file contains a partial application manifest that lists the resources a web browser must cache for offline access. 
4.	After the comment near the start of the file, insert the following URLs to cache the **Home**, **About**, **Schedule**, and **Location** pages:
    ```javascript
        /index.htm
        /about.htm
        /location.htm
        /schedule.htm
    ```
5.	Add the **manifest="/appcache.manifest"** attribute to the **&lt;html&gt;** element of the **Home**, **About**, **Schedule**, and **Location** pages. These are the pages that will operate offline by using the application cache. 

#### Task 2: Detect offline mode by using JavaScript code.

1.	Open **offline.js** file in the **scripts** folder. This script contains methods that hide or show links depending on whether a page is currently running in online or offline mode. The pages that support offline mode reference this script.
2.	After the comment // TODO: if currently offline, hide navigation links that require online, add JavaScript code to detect if the web browser is currently offline, and call the **hideLinksThatRequireOnline()** function.
- Examine the **navigator.onLine** property to determine whether the browser is online of offline.
3.	After the comment that starts with // TODO: add onoffline and ononline events to document.body, handle the **ononline** and **onoffline** events for the document body; call the **hideLinksThatRequireOnline** and **showLinks** functions as appropriate.
4.	After the comment // TODO: also handle the applicationCache error event to hide links, add an event listener to the **applicationCache error** event, which calls the **hideLinksThatRequireOnline** function.

#### Task 3: Test the application.

1.	Run the application and view the **index.htm** page.
2.	Expand the Windows notification area, right click **IIS Express**, and then click **Exit**. Allow IIS Express to exit when prompted.

>**Note:** When IIS Express first starts running, the IIS Express icon may appear in the Windows task bar rather than the notification area. If this occurs, right-click the **IIS Express** icon and then click **Exit**.

![alt text](./Images/20480B_9_IIS-Express.png "The IIS Express icon in the Windows notification area")

3.	In Microsoft Edge, view the **Schedule** page. Verify that the page loads and displays the schedule information. Also verify that, after a brief delay, the **Register** link in the navigation bar is removed. This occurs because the Register page is not cached (it is not included in the **appcache.manifest** file), and registration requires an active connection to the web site.
4.	View the **About** page and verify that this page displays correctly, and that the **Register** link disappears from the navigation bar.
5.	Close Microsoft Edge.

>**Results:** After completing this exercise, you will have modified the web application and made the **Home**, **About**, **Schedule**, and **Location** pages available offline.

### Exercise 2: Persisting User Data by Using the Local Storage API.

#### Scenario

Currently, the **Schedule** page is able to display sessions when offline, but this information does not include whether the attendee selected the session by using the star icon. This information is stored on a remote server that is inaccessible when the application is running offline. However, attendees also need to see the sessions they have selected. This information should be saved locally on an attendee’s computer, so it is available offline.

In this exercise, you will update the JavaScript code for the Schedule page to record an attendee's selected sessions. You will create an object that wraps the Local Storage API. Then you will use this wrapper to save information locally about starred sessions. Finally, you will run the application, view the Schedule page, and verify that the starred sessions are persisted even when the web server is not available.

>**Note:** The Local Storage API is very simple. It can only save and load string key-value pairs. Persisting more complex data requires serialization. In this exercise, you will use a wrapper around the Local Storage API.

#### Task 1: Observe the current behavior of the Schedule page.

1.	In Visual Studio, open the **ContosoConf.sln** solution in the **Allfiles\Mod09\Labfiles\Starter\Exercise 2** folder.
2.	Run the application and view the **schedule.htm** page.
3.	Close IIS Express by using the icon in the Windows notification area.
4.	On the Schedule page, click some of the stars, and then refresh the page.
5.	Notice that information about which sessions are starred is not saved when the application is running offline (the yellow stars turn white again).
6.	Close Microsoft Edge.

#### Task 2: Save information about starred session to local storage.

1.	In the **scripts** folder, open the **LocalStarStorage.js** file. The code in this file wraps the Local Storage API to provide a higher-level interface used by the application code.

>**Note:** The Schedule page will use the **addStar**, **removeStar**, **isStarred**, and **initialize** functions defined in this file. These functions in turn call the **save** and **load** methods that you will implement in this task.

2.	Implement the **LocalStarStorage save** method.
- The IDs of the starred sessions to be saved are held in the array **this.sessions**.
- Convert the array to a string by using the **JSON.stringify** function, and save this string to local storage with the key **stars**.

#### Task 3: Load information about starred session from local storage.

1.	In the **LocalStarStorage.js** file, implement the **LocalStarStorage load** method.
- Get the **stars** item from local storage.
- Parse the JSON string into an array and assign it to **this.sessions**.
- Handle the cases when the local storage returns no data or invalid JSON. In these situations, set **this.sessions** to be an empty array.

#### Task 4: Use the local storage wrapper to save and load data in the Schedule page.

1.	In the **scripts** folder, open the **ScheduleItem.js** page.
2.	In the **initialize** method, find the following comment:
    ```javascript
        // TODO: Check if item is starred
    ```
3.	Add JavaScript code after this comment to:
- Detect whether the session identified by **this.id** is starred (use the **isStarred** method of the **localStarStorage** object).
- If the session is starred, tag the element with the **starred** CSS class so that it is rendered correctly (use the statement this.element.classList.add(this.starredClass);).
4.	In the **unsetStar** method, find the following comment:
    ```javascript
        // TODO: remove the star from the item
    ```
5.	After this comment, add JavaScript code to remove the star from the session identified by **this.id**. Use the **removeStar** method of the **localStarStorage** object.
6.	In the **setStar** method, find the following comment:
    ```javascript
        // TODO: add a star to the item
    ```
7.	After this comment, add JavaScript code to add a star to the session identified by **this.id**. Use the **addStar** method of the **localStarStorage** object.

#### Task 5: Test the application.

1.	Open the appcache.manifest file and insert a version comment near the top of the file. As follows:
    ```javascript
        CACHE MANIFEST
        # version 2

        ...
    ```

>**Note:** The JavaScript files are listed in **appcache.manifest**, and they will have been cached by the web browser. Making a change to the **appcache.manifest** file forces the browser to download the updated scripts.

2.	Run the application and view the **schedule.htm** page.
3.	In Microsoft Edge, refresh the page to ensure the updated scripts are used.
4.	Close IIS Express by using the icon in the Windows notification area.
5.	On the **Schedule** page, click some of the stars, and then refresh the page.
6.	Verify that the same sessions are still starred.
7.	Close Microsoft Edge.

#### Task 6: Reset Microsoft Edge caching.

1.	Configure Microsoft Edge to disable support for application caching:
- Start Microsoft Edge.
- In the Microsoft Edge, press F10 to display the menu bar.
- On the **Tools** menu, click **Internet options**.
- In the **Internet Options** dialog box, click **Settings**.
- In the **Website Data Settings** dialog box, click the **Caches and databases** tab.
- Clear the **Allow website caches and databases** check box, and then click **OK**.
- In the **Internet Options** dialog box, click **OK**.
- Close Microsoft Edge.

>**Result:** After completing this exercise, you will have updated the Schedule page to locally record starred sessions.

©2018 Microsoft Corporation. All rights reserved.

The text in this document is available under the  [Creative Commons Attribution 3.0 License](https://creativecommons.org/licenses/by/3.0/legalcode), additional terms may apply. All other content contained in this document (including, without limitation, trademarks, logos, images, etc.) are  **not**  included within the Creative Commons license grant. This document does not provide you with any legal rights to any intellectual property in any Microsoft product. You may copy and use this document for your internal, reference purposes.

This document is provided &quot;as-is.&quot; Information and views expressed in this document, including URL and other Internet Web site references, may change without notice. You bear the risk of using it. Some examples are for illustration only and are fictitious. No real association is intended or inferred. Microsoft makes no warranties, express or implied, with respect to the information provided here.
