<!-- Put the name of the project after the # -->
<!-- the # means h1  -->
# Group Project (Lyric-Quiz)

<!-- Put a description of what the project is -->
We utilized our skillsets in HTML5, CSS3, Javascript, JQuery, Bootstrap, Firebase 
and AJAX to make a lyrical quiz. The website allows friends to test their knowledge
of lyrics from the current top 100 songs. 

## Link to deployed site
<!-- make a link to the deployed site --> 
<!-- [What the user will see](the link to the deployed site) -->
[Lyric-Quiz](https://jsutliff.github.io/LyriQuiz/)

<!-- # Images
<!-- take a picture of the image and add it into the readme  -->
<!-- ![image title](path or link to image) -->
<!-- [screen shot of completed assignment](assets/images/screenShot.png) --> 
## Images
![Lyric-Quiz-Home](images/bubbles-home.png)
![Lyric-Quiz-SignIn](images/google-signin.png)
![Lyric-Quiz](images/lyriquiz.png)
![Lyric-Quiz-Score](images/scores.png)

## Technologies used
<!-- make a list of technology used -->
<!-- what you used for this web app, like html css -->
1. HTML5
2. CSS3/ Bootstrap/ Bubble.JS
  * grid layout
3. jQuery
  * Event handling
  * Dynamically create new elements
  * Appending and removing newly created elements to other html elements
  * Providing attributes to the newly dynamically created elements
4. AJAX API Calls
  * Building querystrings for the AJAX call to APIs
  * Utilizing the response received from the API
5. Firebase
  * Keeping data persistent
  * Configuring and initializing firebase 
  * Event handlers(child_added, value)
  * Saving and retreiving data from the application to the database
6. Github
  * version control working with collaborators
  * Branching from master branch
  * Merging back to master branch

<!-- 
1. First ordered list item
2. Another item
⋅⋅* Unordered sub-list. 
1. Actual numbers don't matter, just that it's a number
⋅⋅1. Ordered sub-list
4. And another item. 
-->


## code snippets
<!-- put snippets of code inside ``` ``` so it will look like code -->
<!-- if you want to put blockquotes use a > -->
<!------------JASON--------------------------------->
```javascript
    //api call to get lyrics of a random song from chart top 100 tracks 
    var apiKey = "a731b06ce37dbb83ac69163abef82fef";
    queryURL = "http://api.musixmatch.com/ws/1.1/chart.tracks.get?page_size=100&f_has_lyrics=1&apikey=" + apiKey;
    $.ajax({
      url: queryURL,
      method: "GET",
    }).then(function(response){

      var trackList = JSON.parse(response).message.body.track_list;
                    
      var cleanLyricsList = trackList.filter(function(elem) {
        return elem.track.explicit === 0;
      });

      var limit = cleanLyricsList.length;
      var randomIndex = Math.floor(Math.random() * limit);
      
      var trackId = cleanLyricsList[randomIndex].track.track_id;
      var artist = cleanLyricsList[randomIndex].track.artist_name;

```
## Explanation of code
The above shown code snippet is for making an API call to get back the top chart 100 songs and then we use a filter to remove the explicit content from the response. Once we get a list of tracks with clean lyrics, we choose a random track from the response.
<!----------------------------Jason-------------------------------------------->

<!------------ajita--------------------------------->
```javascript

  //CREATE QUESTION
  //api call to get the lyrics of the selected track
  queryURL = "http://api.musixmatch.com/ws/1.1/track.lyrics.get?track_id=" + trackId + "&apikey=" + apiKey;
  $.ajax({
    url: queryURL,
    method: "GET",
  }).then(function(response){
    //get the track lyrics in str
    var str = JSON.parse(response).message.body.lyrics.lyrics_body;

    //find start index of (***COPYRIGHT**) at the end of the lyrics and slice the lyrics
    str = str.slice(0,str.indexOf("*"));

    //split the lyrics on space (" ") and store it in strArray
    var strArray = str.split(" ");
    //find length of the  strArray
    var limit = strArray.length;
    //find a random index between 0 and limit-30 (because we are picking 30 words from 
    //the lyrics and (limit-30) makes sure that we are not picking any index from last 30 words)
    var randomNum = Math.floor(Math.random()* (limit-30));

    //grab 30 words starting from the random index found
    strArray = strArray.slice(randomNum, randomNum+30);
  
    //find a random index to remove a word from the question
    randomIndex = Math.floor(Math.random()*30);
    //get the word using the random index
    missingWord = strArray[randomIndex];
  
    //checks
    //1. words length should be >=4 
    //2. word should not contain any newline character
    //3. It should contain only alphabets(no "'",",","." etc)
    //if any of these conditions are dissatisfied, find a new random word from the question
    while(missingWord.length<4 || missingWord.split("\n").length !== 1 || !onlyAlphabets(missingWord)){
      randomIndex = Math.floor(Math.random()*30);
      missingWord = strArray[randomIndex];
    }

    console.log(missingWord);
    //once we found the random word we are going to remove from the question,
    //replace that word with blank ("______")
    strArray[randomIndex] = "________";
  
    //make the question with the blank into a string
    strOutput="";
    for(var i = 0;i<strArray.length;i++){
      strOutput = strOutput + strArray[i] + " ";
    }
    //append the question to the question area on the page
    $("#card-quiz-area .question").html(artist+"<br>"+strOutput);


    //CREATE OPTIONS
    //api call to find similar sounding words as our missing word to create options for quiz
    apiKey = "a731b06ce37dbb83ac69163abef82fef"
    var word = missingWord;
    var queryURL = "http://api.datamuse.com/words/?rel_rhy=" + word + "&max=10";
    $.ajax({
      url: queryURL,
      method: "GET",
    }).then(function(response){

      //create an array of options and push the missingword we found in this array
      var options = [missingWord];
      //for other 3 options, find the first 3 similar sounding words from the api response
      //and push it in the options array
      //we now have 4 options in the options array
      for(var i =0;i<3 ;i++){
        options.push(response[i].word);
      }

      //But, the first element in the options array will always be the correct answer
      //so we shuffle the options array
      var options = shuffle(options);


      //DISPLAY OPTIONS ON SCREEN
      //for all the 4 options create radio buttons and append to the options are on page
      for(var j =0; j<options.length ;j++){

        //create a radio button with id=customRadio1, customRadio2
        var optionRadio = $("<input type='radio'>");
        optionRadio.attr("id","customRadio"+parseInt(j+1));
        //give the radio button name of customRadio
        optionRadio.attr("name","customRadio");
        //give it the value from the option array
        optionRadio.val(options[j]);

        //craete a label and give it text from the option array
        var label = $("<label for=customRadio"+parseInt(j+1)+">"); 
        label.text(options[j]);

        //create a new div and append radio button to it and give it the label created earlier
        var optionDiv = $("<div class ='custom-control custom-radio'>");
        optionDiv.append(optionRadio);
        optionDiv.append(label);
        
        //append the new div to the options area on the page
        $("#card-quiz-area .answer").append(optionDiv);
      }

      //TIMER
      //clear the timer
      clearInterval(timer);
      //set start time to 20 seconds
      var startTime = 20;
      //set the timer
      timer = setInterval(function(){
        //update the time on screen
        $("#time-left").html(startTime+" seconds <img src='images/timer_2.gif'>");
        //decrement time
        startTime--;
        //if timer has reached 0 seconds
        if(startTime < 0){
          //stop the timer
          clearInterval(timer);
          //show next question
          showNextQuestion();
        }
      },1000);
    });
  });
```
## Explanation of code
This function makes an API call to get the chart top 100 tracks from musixmatch API. From the response, it gets a random track. It then picks a random porition of length 30 from its lyrics and makes that the question. Before showing the question, it removes a random word from the line and puts a blank in there. 
It then passes the removed word to another API Datamuse to find similar sounding words.
It picks the first 3 rhyming word from the response and then makes the 4 options(including the 3 from the response and one will be removed word). It shuffles it and shows the options with dynamically created radiobuttons. As soon as the options appear, a timer for 20 seconds starts. We are also keeping a count of the number of questions and as soon as it reaches 10, we update the score in the database and end the quiz.
<!----------------------------ajita-------------------------------------------->

<!------------Andrew--------------------------------->
```javascript
    //on redirect from sign in page
    firebase.auth().getRedirectResult().then(function(result) {
        if (result.credential) {
            //get the google user info and hide the sign in button
          var user = result.user;
          $("#google-logIn").hide();
          //add their name to the welcome message
          $("#name").text(user.displayName);
          //get their uid
          uid = user.uid;

          userRef.child(user.uid).once('value', function(snap){
              //if they don't have an alias show input box
              if(snap.val() == null){
                $("#username-input").show();
              }
              else{
                  //if they have an alias hide the sign in box and show/start the game, and get their last score
                $("#signIn").hide();
                  fbScore = snap.val().score;
                  $("#re-arrange").show();
                  $("#scores").show();
                  showNextQuestion();                  
              }
          })

        }

    //listener to get the top 3 scores
    userRef.orderByChild("score").limitToLast(3).on("value", function(snap){
        var ranked = [];
        snap.forEach(function(childSnap){
            ranked.push([childSnap.val().username, childSnap.val().score]);
        })
        //firebase doesn't return the list in order so sort it
        ranked.sort(function(a, b) {
            return b[1] - a[1];
        })

        //update the highscore display
        $("#top-champions").html(ranked[0][0] + " " + ranked[0][1] + "<br>"
                                +ranked[1][0] + " " + ranked[1][1] + "<br>"
                                +ranked[2][0] + " " + ranked[2][1])
    })

    //firebase listener to get the exsisting usernames
    userRef.on("value", function(snap){
        snap.forEach(function(childSnap){
            var childData = childSnap.val();
            if(childData.username){
                exsistingNames.push(childData.username.toLowerCase());
            }
        })
    })

```
## Explanation of code
The first snippet of this code handles the redirection from Google's login page. We get the user's name from their google account to welcome them and associated uid to store their information with in our firebase database.

The second snippet handles updating the high scores display. Firebase's orderByChild sorts the users by score, and limitToLast(3) limits this to our top 3 scorers. This returned object isn't sorted by score however so we save them to an array and sort them before placing them onto the page.

The last snippet is a listener for our users in our firebase database. We store the usernames in an array to use later in our checks for new users creating a username. Along with a length of 4 alphabet characters, we make sure usernames our unique.

<!----------------------------Andrew-------------------------------------------->

## Learning points
<!-- Learning points where you would write what you thought was helpful -->
1. Working with Github with collaborators was a new learning experience
  * Branching and Merging with master branch
  * Communicating when changes made to master
2. Agile approach of application development
  * Communicationg within group members
  * Updating members every day about the progress
  * Making changes as they came
3. Working with multiple .js files and calling functions from one another

## Author 
<!-- make a link to the deployed site and have your name as the link -->
* [Ajita Srivastava](https://ajitas.github.io/Portfolio/)
* [Andrew M.](https://andypants152.github.io/Responsive-Portfolio/)
* [Jason P. Sutliff](https://jsutliff.github.io/Basic-Portfolio/)
* [Nasibeh N.](https://nasibnia.github.io/Bootstrap-Portfolio/)
