# Healthspaces Demo

Quick demo to show Redux + Firebase + Polymer, specifically:

* Putting Firebase auth state into the Redux store
* Using actions and middleware to trigger data fetching (conditionally)
* Setting selected states into the store for use by selectors & conditional loading
* Using selectors to provide views on the data (filtering + loading spinner)

## Notes

Firebase JS SDK is used directly rather than going through Polymerfire.
Although the databinding elements are great for quick setup, the databinding within
views  doesn't give you much to hook into at an app level and at some point you 
inevitably have to start using the FB objects directly anyway.

Much of the logic of the app should move to the redux store and the actions,
middleware, reducers and selectors which *should* be easier to reason about, debug,
test and re-use. The Chrome Redux dev tool extension for instance makes stepping 
forward and back through state easy.

The views become more UI binding to pass values to the store via actions in
response to user interaction and to show data from the store (accessed via the
selectors which help performance and also insulate the UI from store refactoring).
This also means that the guts of the app logic isn't tied directly to any particular
UI library or framework which can help with future upgrades or switching to use
something else (Vue.js, Polymer 3.x, WebComponents + lit-html etc ...)

NOTE: the structure used is not ideal - you would typically denormalize the data
in the redux store so that you have collections of entities keyed by id and the
various "appointmentsByDate" type lists would just contain the IDs of them and the
selectors would put them together so you have a nice list of items to work with.

The non-obvious benefit to this is that getting a list of data is transformed
into the IDs and entities and if you then navigate to the detail ID it can trigger
a similar 'fetchEntityIfRequired' action - if the user has been to a list previously
then the data is already there and nothing needs to be loaded, if not (they clicked
a link / pressed refresh) then the action causes the single item to be fetched and
added to the store, same fetch and binding path.

The example does a snapshot fetch of data from firebase - for live updates the
middleware would setup a subscriber to then translate updates from elsewhere into
the actions to update the local copy of the store. For performance, this might need
to hook into an active module / view visibility mechanism so that unecessary fetches
weren't slowing down an app just because some other view had once been loaded. i.e.
fetch latest data and start subscription when a view is active, stop it when it is
inactive.

The firebase middleware is intended to demonstrate the concepts without adding too
many new dependencies but it would be well worth looking at existing Redux + Firebase
middleware libs:

* https://github.com/Canner/redux-firebase-middleware
* https://github.com/AndersDJohnson/firedux

This [video](https://www.youtube.com/watch?v=UHJq5NOtNG4) is for React but applies 
equally to Polymer & Polymerfire as well.

## Usage

Firebase config is in the `my-redux-middleware.html` file (could go in `index.html`)

Otherwise it should be a typical `npm` & `bower` install and `polymer serve` it ...
