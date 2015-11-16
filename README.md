App Engine application for the Udacity training course.

## Products
- [App Engine][1]

## Language
- [Python][2]

## APIs
- [Google Cloud Endpoints][3]

## Setup Instructions
1. Update the value of `application` in `app.yaml` to the app ID you
   have registered in the App Engine admin console and would like to use to host
   your instance of this sample.
1. Update the values at the top of `settings.py` to
   reflect the respective client IDs you have registered in the
   [Developer Console][4].
1. Update the value of CLIENT_ID in `static/js/app.js` to the Web client ID
1. (Optional) Mark the configuration files as unchanged as follows:
   `$ git update-index --assume-unchanged app.yaml settings.py static/js/app.js`
1. Run the app with the devserver using `dev_appserver.py DIR`, and ensure it's running by visiting your local server's address (by default [localhost:8080][5].)
1. (Optional) Generate your client library(ies) with [the endpoints tool][6].
1. Deploy your application.


[1]: https://developers.google.com/appengine
[2]: http://python.org
[3]: https://developers.google.com/appengine/docs/python/endpoints/
[4]: https://console.developers.google.com/
[5]: https://localhost:8080/
[6]: https://developers.google.com/appengine/docs/python/endpoints/endpoints_tool

## Testing

To test the API endpoints methods visit conference-central-1118.appspot.com/_ah/api/explorer


## Task 1 Design choices

1. Sessions are implemented as a child the conference class to link them to the conference in which they take place.
I chose to implement sessions this way because it makes it very simple to link the two together.
When fetching sessions for a given conference I used an ancestor query to just fetch all the children of the parent class conference.
This was the main appeal of making sessions a child of a conference.
In general it just made the whole process of querying sessions much simpler in the long run.

2. Speakers however are not implement as a child of session, nor are they directly linked to a given user.
This was done because I feel like it would get annoying for users to have to be registered on the website to be speaker.
What if for example you get some one like Bill Gates to give a lecture, you would have to either have him sign up, or create an account for him.
That could get cumbersome when creating sessions. 

3. Session types are implemented simply as string for the same reason.
If sessions were an enumeration, or set value of some kind it might be too limiting.
Letting users put what ever they want as a session type really puts the control in their hands as to what kind of session they want to run.

4. duration is stored as a time field because it would make it easier to sort sessions by duration. If it were stored as a string or an int it could get a little messy with formatting. Some users my format it differently, and that would lead to potentially incorect data during the filtering / sorting process if there were queries on the duration field.

5. startTime is stored as a time field for much the same reason as duration. It is especially beneifical for startTime in the getNonWorkshopSessions method. In this method we are making sure start time is before 7pm. Making it useful to have it stored as a time field. Time and date fields could also be useful if I had implemented functionalty to check if a session had already taken place. That was beyond the scope of what I wanted to do with this project though, but it is nice to have the data stored in a way that would allow for that to happen in the future if I wanted to implement it. This the same reason for data and duration being date and time fields respectively. Even though I may not use the functionality of these types fully, I think it is good practice to store them in this manor in case I wanted to in the future.
 
6. Once again Date is stored as a date field to make sure it isn't input in an improper format, and to make it easily queryable as a date to check things like how many days till a conference / session. Storing it as a date field instead of a string also allows for me to use it in more in depth ways in the future had I wanted to much the same as I explained with startTime and duration.




## Task 3 Design choices

1. Two additional queries were added, getSessionsByCity, and getSessionsByTopic.
These two queries were added to allow users to find more appealing conference sessions.
An example of a user wanting something like this could be a conference with a more broad topic and a lot of sub topics.
With getSessionsByTopic the user can easily find which sessions in that conference they would want to attend.
It isn't just limited to one conference though, this query grabs all sessions with that topic across all conferences.

2. getSessionsByCity is maybe a little less useful, but however it offers a different view of events happening in given city.
If for example you want to know more specific information about what's going in Paris you can instead filter which sesssions are happening there.
You could find all the conferences happening there as well, but that may not really give as specific information as you may want.

3. getNonWorkshopSessions making a query to find all sessions after 7pm and who's type isn't workshop was quite tricky actually.
The main problem with implementing this is that you can't have two different inequality filters applied to two differnt fields.
The way I chose to solve this problem is first fetch all sessions before 7pm, then instead of filtering that query again, I loop through the list of results to find the ones whos typ isn't workshop.
Technically making datastore do all the filtering may be a bit more efficient, but applying multiple inequality filters to two different fields in a given query isn't allowed in datastore.
One more problem I ran into when implementing this is that since I allow types of sessions to be any string some people may capitalize the 'w' in workshop or they may not.
To solve this, I convert the typeOfSession string all lower case before checking it against 'workshop'. 
