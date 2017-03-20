---

layout: post
title: Bloc Chat
thumbnail-path: 
short-description:  Bloc Chat is an application for instant messaging.

---

Bloc Chat, stage 1:  Today I defined the Angular module for the application and included ui.router and firebase as dependencies.  This project will be using AngularFire.  
This will provide 3 way data binding between the HTML, Javascript and the Firebase database in my application.  

{:.center}
![]({{ site.baseurl }}/img/)  //to display images

The code below defines the module for the application.  I also have defined a state named 'home'.  This state will provide the view for the index.  When a use first navigates
to the site, the ui-router will reference the home as the first state.  

{% highlight js %}                     

(function() {
    config($stateProvider, $locationProvider) {
        $locationProvider
            .html5Mode({
                enabled: true,
                requireBase: false
        });
        
        .stateProvider
            .state('home', {
            url:'/',
            templateUrl:'/templates/home.html'
        });
            
    }
        


    angular
        .module('bloc-chat', ['ui.router', 'Firebase']) 
        .config(config);

})();
    };
});
{% endhighlight %}

Firebase is being used as a database for the chat rooms and the messages.  The $firebaseArray will be used to store the chat rooms and messages.  A factory is used as a service to provide acces
to the array.  This factory service "Room" is injected into the controller "HomeCtrl" to add functionality to the homepage template.  I use the ngRepeat directive to iterate through the collection of rooms
in the home template.  This produces a list of room names from the firebase database.  

See the Room Service Below:

{% highlight js %}     

(function() {
  function Room($firebaseArray) {
    var ref = firebase.database().ref().child("rooms");
    var rooms = $firebaseArray(ref);

    return {
      all: rooms
    };
  }

  angular
    .module('blocChat')
    .factory('Room', ['$firebaseArray', Room]);
})();

{% endhighlight %}

How to create Room objects with AngularFire?

In order to add Rooms to the Bloc-Chat one must use the $add() method on the $firebase array.  The $add() method can be used to change the structure of the array.  This method must 
be added to the factory recipe.  


Making a modal

To make a modal, which is essentially a pop-up window, you need a few things.  For starters there needs to be a button that, once clicked, will open your modal.  In the Bloc Chat application, 
I want users to be able to create and name their own chat rooms.  The modal will be used to house this functionality. 

{% highlight js %}

<div class="chat-side-bar">
    <h1>Bloc Chat</h1>  
    <div></div>
    
    <ul>
        <li id="chat-room-list" ng-repeat="room in home.rooms">{{room.name}}</li>
    </ul>
    
     <button ng-click="home.open()" type="button" class="btn btn-primary">Open New Chat</button> 
    
    </div>
    
 {% endhighlight %}
    
    The button used to open the modal utilizes the ngClick directive.  To bind a scope to the button, I create a controller, HomeCtrl.js.  With the service "Room" injected,
    along with the "$uibModal" service, the controller lets the user see the array of rooms and open a the modal:
    
    {% highlight js %}
    
    (function() {
    function HomeCtrl(Room, $uibModal) {  //inject the room service in order to assign the array of objects retrieved by "all" method
        this.rooms = Room.all; 
        
        this.open = function() {
        var modalInstance = $uibModal.open({  //uib modal creates a object on to which you can add functionality.  Imagine the uib modal as the container that opens up when you click the modal button
            ariaLabelledBy: 'modal-header',
            templateUrl: 'templates/modal.html',
            controller: 'CreateRoomCtrl',
            controllerAs: '$ctrl'
        });
     
    }; 
        
  }
{% endhighlight %}

How do I create Room objects with AngularFire?*  

How can I initiate room creation in the app's interface?*

Include the UI Bootstrap library via a <script> tag on index.html
Inject the module into your Angular app's dependency array
Create a separate controller for the modal
Inject the proper dependencies for using the modal (see the UI Bootstrap documentation)
Add methods to open, close and submit data to Firebase from the modal

How can I display an individual room's content?*  create a 2 way binding between the template and the controller.  When a user clicks the chat room, the view updates the model.  In HomeCtrl.js the name of the chat room will be stored in an object.  

Create a global object "this.room" in HomeCrtl that stores the currently active chat room.  Set that object to null, making it empty.  In the view (home.html), add directive to chat room list.  This directive will store the room as an object "room.name".  The click will set off the function "home.storeRoomName" which is a method to store the room clicked on as "room.name".  The room.name is two way binded using curly braces and passed as an argument to the home.storeRoomName method.  This method makes the object available globally.  The object can then be called in a new div using curly braces.  To reference the object we use "home.room.name".


{% highlight js %}

this.room = null;

  this.storeRoomName = function(room) {    //store a room from this.rooms in as scope value
        this.room = room;
    }


<ul>
        <li id="chat-room-list" ng-repeat="room in home.rooms" ng-click="home.storeRoomName(room)">{{room.name}}</li> <!--click to store current room in a scope object -->
    </ul>
    
</div>

<div id="chat-room-container" class="col-sm-8">
    
    <h1>{{home.room.name}}</h1>  <!--show the name clicked in the div-->
    
</div>  

{% endhighlight %}

How can I query messages for an active room?*
Using the child() method on the $firebaseArray service again, query messages instead of rooms this time. To get the messages for a given room, you need to chain the child() method with Firebase's orderByChild()1 method, targeting the roomId child.

Recall that roomId is a nested property of each message object. A nested property in Firebase is equivalent to a child, hence its compatibility with the  orderByChild() method.
Messages depend on the ID of a room, you will need to pass an argument into the  getByRoomId method that contains the roomId associated with a rooms message. With the roomId, use Firebase's equalTo() method to find all messages whose  roomId property is equal to the roomId in the argument:

How can I efficiently store a username?*
A username is a string identifying a user. A common way to store a string in your browser is to use cookies. Angular has an external module for including the services and methods associated with cookies. To integrate the module I include the Angular cookies module via a <script> tag in index.html and inject the ngCookies module into my Angular app's dependency array.


I need to require new users to enter a username before they can access the chat application.  Because of this I include a ".run()" method that runs code when the app instance is created. Using a .run() block I make sure that a username is set at the time the app is initialized.  The $cookies service is injected into the run block's dependencies to check for the presence of the cookie holding the username.  Upon arriving, the application will open a modal, if there is no current user.  I used the $cookies.get() method to check if the cookie exists for a user.  

{% highlight js %}

(function() {
  function BlocChatCookies($cookies, $uibModal) {
    var currentUser = $cookies.get('blocChatCurrentUser');
      if (!currentUser || currentUser === '') {
        var modalInstance = $uibModal.open ({
          ariaLabelledBy: 'modal-title',
          templateUrl: 'templates/setusermodal.html',
          controller: 'SetUsernameCtrl',
          controllerAs: '$ctrl',  
          backdrop: 'static',
          keyboard: false    
        })   
      }
    }

  angular
    .module('blocChat')
    .run(['$cookies', '$uibModal', BlocChatCookies]);
})();

{% endhighlight %}


To add functionality to the modal, I created a controller to house two methods.  The first is used to set a new username.  When the user enters text into the input field of the modal, the text will become the cookie for the blocChatCurrentUser.  Another method is used to close the modal.  The modal will only be dismissed if a current user exists.  The "if" statment in "this.ok" checks with a boolean to see if the cookie exists.   

{% highlight js %}

(function() {
    function SetUsernameCtrl($cookies, $uibModalInstance) { 
        this.setUsername = function(newUser) {  //function to add user to cookie
            $cookies.put('blocChatCurrentUser', newUser);  //replace current key with new one
            if ($cookies.get('blocChatCurrentUser')) {
                alert('Welcome!  Now click done! ' + newUser);   
                } else {
                alert('Enter a username!');
                }   
            }
        
        this.ok = function() {
          if ($cookies.get('blocChatCurrentUser')) {  //if there is a current user, allow dismissal of modal
              $uibModalInstance.dismiss('Done');
        }    
      }
    }
        

angular
  .module('blocChat')
  .controller('SetUsernameCtrl', ['$cookies' , '$uibModalInstance', SetUsernameCtrl]);
})();

{% endhighlight %}    

The controller gives us the modal its interactivity.  Using the ngClick directive, the buttons "Add" and "ok" enable the user to close the modal and add a username.  The text entered into the placeholder is passed to the "setUsername" method to check if the cookie exists.  

{% highlight js %}

div class="modal-content">
  <div class="modal-header">
        <h3 class="modal-title">Create Your Own Username!</h3>
    </div>
        <div class="modal-body">
            <form>
                <h4>Choose a Username:</h4><input id="newUsername" type="text" ng-model="newUsername" placeholder="What's it gonna be?">
            </form><button class="btn" ng-click="$ctrl.setUsername(newUsername)"><i class="icon-plus"></i>Add</button>
        </div>
    <div class="modal-footer">
        <a ng-click="$ctrl.ok()" class="btn btn-primary done" data-dismiss="modal">Done</a>
    </div> 
</div>
    
{% endhighlight %}    


How can I send messages?*
Add a method to your Message factory called send, that takes a message object as an argument and submits it to your Firebase server:


How can I make sure that the messages that a user sends are associated with their username?*



