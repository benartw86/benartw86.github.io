---

layout: post
title: Bloc Chat
thumbnail-path: 
short-description:  Bloc Chat is an application for instant messaging. 

---

Bloc Chat, stage 1:  Today I defined the Angular module for the application and included ui.router and firebase as dependencies.  This project will be using AngularFire.  
This will provide 3 way data binding between the HTML, Javascript and the Firebase database in my application.  

{:.center}
![]({{ site.baseurl }}/img/blocflix.png)  //to display images

The code below defines the module for the application.  I also have defined a state named 'home'.  This state will provide the view for the index.  When a use first navigates
to the site, the ui-router will reference the home as the first state.  

{% highlight javascript %}                     

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

{% highlight javascript %}     

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
