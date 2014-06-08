# NUCLEUS

## HOW TO GET IT RUNNIN

* clone this obviously 

* Enable package with `apm link --dev /path/to/nucleus` (it will be symlinked in ~/.atom/dev/packages)

* Then run dev version of Atom with `atom --dev`

* Have Atom console always open with `crtl+opt+i`

* Trigger Nucleus from the Nucleus > Enter Nucleus menu option or: `crtl+opt+n`

* You will see a form popup appear. You can submit the form with no options and it will know what to do.

* A key trick is you can refresh the app by typing `command+r` like refreshing a Chrome browser window. But your console has to be focused for the command to work. 

* To evaluate code, make a selection and right click the editor--you will see an "Evaluate in Application!" option.

* Don't forget the Nucleus menu in the top bar! (and the corresponding keymaps you will see there)

## HOW TO HACK IT

First do these things:

* study this: https://atom.io/docs/v0.101.0/creating-a-package
* study the API: https://atom.io/docs/api/v0.101.0/api (particularly workspace, editor; models + views)
* study the Space Pen coffeescript view system: http://atom.github.io/space-pen
* grasp how the commonjs require() + module.exports pattern works
* analyze Firebase API: https://www.firebase.com/docs/javascript/firebase/index.html
* login to my Firebase account and go to the playground: https://faceyspacey.firebaseio.com ...you can observe the data change here in realtime in relation to Nucleus doing its thing!

Where to start in the code:

* https://github.com/faceyspacey/nucleus/blob/master/package.json (main key --> ./lib/nucleus.coffee)
* https://github.com/faceyspacey/nucleus/blob/master/lib/nucleus.coffee (--> ./lib/views/forms/enter)
* https://github.com/faceyspacey/nucleus/blob/master/lib/core/project.js (like a controller --> adapters)


Some Notes on Important Files:

* project.js (Project) is designed to be a thin controller or route map. The idea is to give you an overview of control flow, events, and possible entries for code execution paths. 
* /adapters contains singletons and instantiable objects (File) used by Project. The goal is for all of it to be interchangeable. Some code may seem peculiar in a few places--just know that top priority was perfect explicit interfaces in front of single responsibility objects, not the least amount of code or shortcuts. 
* /views is coffeescript views. The forms extend from an abstract class I made which extend from Space Pen's View class. I try to keep these as dumb and slim as possible. If you have any issues where Atom can't parse the code here, go to the beginning of each line and type backspace until it touches the end of the previous line and then press enter and tab until it's in the right place. Any incorrect spacing and tabs will fuck it up and are hard to see, but perhaps your python skills make this more intuitive for you. 


How Spec Pen Works:

* the `@content()` static method is what's used to generate the DOM nodes each time an instance is instantiated. 
* `outlet` attributes on DOM nodes within the `@content()` method create instance properties to access the DOM node in regular instance methods 
* the `initialize()` method is automatically called each time the view is instantiated
* `toggle()` is also build into the parent View class to attach/detach the view. Extend it to do custom stuff, and keep its format.
* `attach()` and `detach()` are used to do you know what. So like attach/detach child views in these methods.
* use `super` to call the parent method. 

##NOTEWORTHY PARTS OF THE CODE
* the way files are created from editors first opened on the current client vs files created because the files were opened on other clients is very interesting and a lot of work was put into doing it right.
* the entire adapter system goes a long way to make sure that we can truly replace an adapter with a different one. This means in some places code is not what you would typically expect, and goes to great lengths to make sure no code moves out of the principle object responsible for it.
* the route replication system in the `app_adapter.js` is worth analyzing
* just start with `project.js` and drill down from there. 
* we simply change adapters here: https://github.com/faceyspacey/nucleus/blob/master/lib/core/config/adapter_config.js

##TIPS ON ATOM
* all atom API calls are in: https://github.com/faceyspacey/nucleus/blob/master/lib/core/adapters/workspace/atom/workspace_adapter.js
* search that page for `atom.workspace` and `atom.workspaceView` to find them
* views extend from Space Pen's View and they themselves extend from jQuery. So you can use `.remove()` to remove them. 
* there's a concept of `model` vs `view` in Atom. Views are the jquery/space-pen objects and the fat model's do the work
* there's a reason i assigned `window.nucleus = nucleus`. The reason is because atom is configured that way. `atom` is a global var. So are keeping a similar "interface"


##TIPS ON FIREBASE
* open up https://faceyspacey.firebaseio.com and open the console and start practicing firebase calls while looking at the data. Firebase is already included on this page
* you will notice that Firebase is really flexible. Basically u have URI's at our host, i.e. any path, and u can reference it and start adding child data to it. It's infinitely recursive. 
* You don't need a URI to exist to get a reference to it and start adding data to it. So `var reference =  new Firebase('https://faceyspacey.firebaseio.com/india/punjab/chandigarh/people/charan)` will immediately return a reference, and you can add data to it, and if you do, all the parent folders will be created for you (though only holding one node)
* u can use `ref.child('some_path)` to get a child path
* use `ref.one('child_added', function(snapshot) {})` to get ur handler function called every time a child document is added
* the `snapshot` param has methods: `snapshot.val()`, `snapshot.name()` and `snapshot.ref()`. The first retrieves the doc, the second the key, and the last a reference from which you can use like the above reference
* use `ref.once('value', func)` to basically "get" the data at a location, like a `findOne()` call in meteor
* use `.push()` to essentially add docs with a generated key. It doesn't really appear to be an array to me, just more of the same recursive stuff with hash key
* everything related to firebase is hidden behind the db adapter interface, so you can learn all you need by seeing it in action here: https://github.com/faceyspacey/nucleus/blob/master/lib/core/adapters/database/firebase/db_adapter.js

##TO DO:

* test and get all this to work again. Since it's been refactored, it hasnt been forced to work again.
* `App.launch()` should also git clone, and should put the code in ~/Nucleus, and toggle git clone vs git pull. It should also open the Atom Project for ~/Nucleus/project_name if not already opened
* in `nucleus.coffee` the forms after the Enter form need to be tested. The coffeescript will need to be formatted properly (remove hidden tabs)