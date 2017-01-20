layout: post
title: Bloc Chat
thumbnail-path: 
short-description:  Bloc Chat is an application for instant messaging.  

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