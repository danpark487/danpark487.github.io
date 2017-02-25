---
layout: post
title: React Modals -- Scalable? Customizable? Neat Components?
---

### <strong>Why should you care?</strong>

If you haven't tried to implement modals in React, you wouldn't understand the context of this blog. If you had, there is a chance you have wandered into some random novice JS developer's blog (I'm talking about this one) because, quite frankly... there isn't a whole lot of guidance re: modals in react. Of course, this isn't to say that there aren't ANY good resources on the subject out there... I certainly wouldn't have gotten to where I am now without them. However, I found their implementations confusing (for a beginner like myself), conflicting, and/or vague.

BUT! Here are a collection of a few gems that I have found -- where the rest of my blog is based heavily upon:
* <a href="http://stackoverflow.com/questions/35623656/how-can-i-display-a-modal-dialog-in-redux-that-performs-asynchronous-actions/35641680">StackOverflow: Dan Abramov's answer on how to implement modals with redux</a>
* <a href="https://codersmind.com/scalable-modals-react-redux/">Scalable Modals with React and Redux</a>
* <a href="https://medium.com/@david.gilbertson/modals-in-react-f6c3ff9f4701#.rrwqgz9e6">Modals in React</a>
* <a href="https://www.youtube.com/watch?v=WGjv-p9jYf0">Youtube: [React] Modals in React and Redux Apps</a>

I recommend you checking out those links -- but you don't have to.

It is mainly to prove a point... modals in React are a pain to implement... even if you use a library like <a href="https://github.com/reactjs/react-modal">`react-modal`</a> (sometimes there is too much abstraction).

### <strong>Basics - What are modals?</strong>

<div class="message">
  Note: if you already understand what modals are, and want to get to the meat of this post, then scroll down!
</div>

In layman's terms, modals are simply pop-up messages that you often see being used for Login/Registration, TOS, warning, etc. messages.

For example:
> <img src="https://cdn-images-1.medium.com/max/800/1*5s21EnFDMOklJr8T9KIp2Q.png" width="75%" height="50%">

You've definitely seen these before and if you are anything like me, you might've even tried to implement one in a website.

<div class="message">
  For the purposes of this blog post, I will only be speaking about modals in <strong>React</strong>. I would not be surprised if it were easier to do in Angular, or some other framework.
</div>

In short, modals are simply "hacky" HTML/CSS views combined with some event-handlers.

I write "hacky" in quotes because messing around with HTML and CSS seems like something we shouldn't do much of in React. But with modals, it's <strong>unavoidable</strong>. If you <strong>hate</strong> CSS, turn around and forget about modals altogether before it's too late. But remember, you will never have a cool login pop-up suited to your design tastes. :)

<strong>Back on topic:</strong>
Modals are a rendering of some HTML elements with certain CSS classes <em>triggered</em> by some event-handler on a button, link, or whatever. Basically, you click a button, the pop-up is rendered on your screen! The magic is that the website you were just on does not go away! You can still see the page you were just on behind the pop-up! 

There are three major HTML elements rendered when the modal pops up:
1. An <strong>Overlay</strong> - typically a `<div>` element with a class that creates a "shadow" effect over the document while the modal is onscreen.
2. A <strong>Content Box</strong> - another `<div>` element with a class that provides the position context for the dialog/modal box (#3).
3. A <strong>Dialog/Modal Box</strong> - yet another `<div>` element with a class that resizes itself to the content of the modal.
> Just think of these as three layers, one on top of another.

That's it! That's the skeleton of a basic modal. However, the real challenge is how to make a re-usable modal with customizable design inside your React application.

### <strong>Why Something Seemingly Simple Becomes Hard With React</strong>

<div class="message">
    Important: The rest of this post will assume you have a solid grasp of how components are often structured in react with redux (and react-redux).
</div>

If you checked out the suggested links at the top of this post, you may be feeling what I did when I first tried to understand what they were talking about. Huh? As a beginner, it only raised a host of questions like, 'So where do I put the ModalRoot in my routes?', 'How should I connect it with my redux store?', 'Can I customize the default template modals?', and so on and so forth...

Here is what I want my modal to do (and what you probably want as well!):
1. <strong>Reusable</strong> - <em>I do not want to create the HTML/CSS for every modal I put into my app. Who does?</em>
2. <strong>Scalable</strong> - <em>I want my modals to be rendered from a centralized location for organization purposes.</em>
3. <strong>Redux-ified</strong> - <em>I want to connect my modal to my redux store because I may want to pass props down.</em>
4. <strong>Customizable</strong> - <em>I don't want all my modals to look the same.</em>

We will take these one-by-one below. Whee!

### <strong>1. Reusable - make a default Modal component!</strong>

You could import in a nice package like <a href="https://github.com/reactjs/react-modal">`react-modal`</a> to do this for you, but you won't have as much control over its customization and/or its connection to the redux store. But it's up to you!

> There is a lot of boilerplate that you can just copy and paste, but I will explain why the boilerplate is important!

* You will need to create a class for your default Modal component.
> <img src="http://i.imgur.com/FOYy6D2.png" width="100%" height="70%">

The rendered portion of the default Modal component is what I mentioned earlier, namely the three HTML elements (Overlay, Content Box, Dialog Box) and their CSS classes that render the Modal. 

> You can ignore the methods and style props being passed through the `<div>` elements for now.

* You will need to give default CSS classes to the elements.
The corresponding CSS is more boilerplate as well (presumably inserted into your custom css file):

> <img src="http://i.imgur.com/7ts1rmq.png" width="50%" height="65%">

The <strong>Overlay</strong> class has a high z-index that places itself over the current page. It covers the entirety of the page and its color is set to black with the opacity set to .65. Obviously, you could change that if you wish.

The <strong>Content Box</strong> class has an even higher z-index placed on top of the overlay. It also covers the page and it provides positioning context for its child Dialog Box.

The <strong>Dialog Box</strong> class, as a child to the <strong>Content Box</strong>, has its position relative to the Content Box. Anything that goes inside the Dialog Box (your actual modal content), will resize the Dialog Box accordingly.

* Last but not least, the Modal methods! (more boilerplate)

> <img src="http://i.imgur.com/uhgDlGI.png" width="100%" height="75%">

These methods allow user input to create expected default actions. 

* <strong>listenKeyboard</strong> "listens" to the user keyboard to see if they have pressed "ESC" while the modal is open. If so, the modal closes.
* <strong>onOverlayClick</strong> means that if you click on the overlay (outside the Modal Dialog Box), it will close the modal (an expected outcome).
* <strong>onDialogClick</strong> simply prevents the closing of the modal when you click within the Dialog Box (because it will close when you click the Overlay that covers the whole page at a lower z-index). If this confuses you, try disabling it and testing the modal -- you will see why this is necessary!

<div class="message">
    <strong>Very Important!</strong> If you note, these methods take in an <strong>onClose</strong> method to cause the Modal to close! In other words, if an <strong>onClose</strong> method is NOT passed into the Modal component as a prop, your modal will not close as expected! 

    But we see how that's done below :)
</div>

These methods are passed through the respective `<div>`s to give you the... Full Modal Experience. We still haven't touched on the customized styles (which we will touch upon last).

Now, you have created your default Modal component! You can now import this modal anywhere in your App for use!

### <strong>2. Scalable - create a ModalRoot container</strong>

If your website anticipates more than a single kind of modal (e.g., Login Modal, Warning Modal, Registration Modal, Terms of Services Modal, etc.), it may be wise to separate the presentational modal component from a container that handles which presentational component ought to be rendered. 

The following code will do just that!
> <img src="http://i.imgur.com/tKKeH4N.png" width="100%" height="75%">

At first glance, this may be a bit confusing, but we will take it step by step. It is also difficult to read and understand this snippet without the proper context (e.g., what is being passed in, where are all these things located).

Things to note:
1. We are importing all the unique modal components into this container! (e.g., LoginModal, WarningModal, etc.)
2. We have constants imported in for all the different modal types because this Modal Container is connected to the store and will be receiving a prop as `props.modalType` (via `mapStateToProps`) to figure out which modal to render.
3. The MODAL_COMPONENTS object maps each modal component to their respective modalType (from the redux store).
4. Lastly, the ModalContainer will receive `props` and checks if the store has a specific modalType in mind to be rendered; if no modalType is set in the store, nothing is rendered from the container. However, if there IS a modalType set on the store, then the ModalContainer will check the MODAL_COMPONENTS object, grab the corresponding modal and render that out. 

This way, you have a predictable location (container) from which all your different modals will be rendered!

<div class="message">
    It is understandable to be confused at this point, because we have not covered the redux portion yet, but since that's up next, we will perhaps get a better sense of how this all comes together!
</div>

### <strong>3. Redux-ified - connect it to the store as a child of the App component</strong>

First things first, we need to somehow connect this ModalContainer to someplace so that it will be rendered on our HTML! Right now, the ModalContainer and its soon-to-be-rendered Modal Components are out in limbo. Where should we anchor it?

There is a whole debate on where best to do this. But I'll cut to the chase -- it is best here.

> <img src="http://i.imgur.com/llaZgqz.png" width="80%" height="80%">

Why? Because it avoids the messyness of having to pull in <em>another</em> `Provider` from `react-redux` and `ReactDom.render` to connect to the store and attaching a new element directly to the `index.html` file. Don't understand that? It really doesn't matter because you don't need to!

Anyways, if you look at the image above, the App component is the parent to the ModalContainer. The App component is the parent component to all other components, as seen below:

> <img src="http://i.imgur.com/BpHMG9P.png">

I am using `react-router` here... and ignore my other routes. The main point is that the App component is the parent component to everything else!

Next, all the routes are connected to the redux store via the `Provider` (react-redux) as follows:

> <img src="http://i.imgur.com/1CQPpc6.png" width="50%" height="50%">

This way, you should not have to modify your routes in any way, you do not have to touch the `ReactDOM.render` in your index.js (or wherever you are connecting your app to the redux store). You only have to add a single line in your parent App component!

Lastly, we have to create some actions and a reducer to handle those actions in the store!

There are only <strong>two</strong> must-have actions you have to set up for your modals:

> <img src="http://i.imgur.com/muN4EVu.png" width="80%" height="80%">

> I am using thunk middleware for my redux store (but you don't need it, or have to know anything about it... I'm just using it for my app).

1. <strong>SHOW MODAL</strong> - takes in a modalType to set the modalType in the store.

2. <strong>HIDE MODAL</strong> - simply an action that will set the modalType to `null` in the store.

> <img src="http://i.imgur.com/vxXcAPG.png" width="80%" height="80%">

Here we see the reducer for the modals.

Initially, the store's modalType is set to `null`. However, once an <strong>showModal</strong> action is dispatched from wherever in your app (with the desired modalType), it will set the store's modalType to the desired modal. This will then trigger the `mapStateToProps` in our ModalContainer (connected to the redux store via the `Provider` as a child of the App component) and now we have `props.modalType` set to the desired modal component. As a result, ModalContainer will render that desired Modal component!

The <strong>hideModal</strong> is also dispatched from your app and will set the store's modalType back to `null` (hiding the modal again).

Almost done!

### <strong>Bringing it all together... almost!</strong>

Bringing this together involves two things (1) creating your presentational modal component and (2) triggering the <strong>showModal</strong> action somewhere in our app (think: a Login button)!

1. Basic Login Modal Component

> <img src="http://i.imgur.com/5DladXJ.png" width="100%" height="70%">

There a few important things to note here:
* First, we are importing in the <strong>default Modal component</strong> we created in Step 1 of this blog. Just like any other React component, it can be reused anywhere! We will be rendering the Modal component and within it, the specific content of the modal - here, the login form!
* Second, we have an <strong>onClose</strong> method defined here. Remember earlier I said this has to be passed in as a prop to the default Modal component for it to trigger the different methods to exit out of the modal (e.g., <strong>onOverlayClick</strong>). It is inside this onClose method that we will pass in our <strong>hideModal</strong> action-creator to set the store's modalType to `null` (which triggers the ModalContainer to render out no presentational component -- `if (!props.modalType) return null`).

2. Login Button!

<div class="message">
    If you have separated your presentational components from your container components react-redux style, then what follows below will make sense. If you are unfamiliar with the presentation-container divide, the presentational component is merely receiving props from the container and handle all rendering.
</div>

* NavBar Presentational Component
> <img src="http://i.imgur.com/HdPjD2k.png">

Here we see a very basic navigation bar for our app. It only contains a single LOGIN button. But as you can see, the button has a <strong>showLoginMenu</strong> method (passed down from the container) that will be triggered once the button is clicked.

* NavBar Container Component 
> <img src="http://i.imgur.com/XLxVcGi.png">

Here, we see how the presentational component receives the <strong>showLoginMenu</strong> method. We imported the <strong>loadModal</strong> action-creator and will invoke it within the <strong>showLoginMenu</strong> method inside the NavBar Container <strong>with</strong> the LOGIN_MODAL modalType! 

This is very important. We are saying that once the button is clicked in our presentational component, it will dispatch the <strong>loadModal</strong> action to the store with the LOGIN_MODAL modalType. It will set the store's modalType to `LOGIN_MODAL` and will trigger the ModalContainer to render the LoginModal via `const SpecificModal = MODAL_COMPONENTS[props.modalType]`!

Now, if you click on that Login button, you will get your Login Modal popping up on your screen! If you click outside the modal or press 'ESC' it exits the modal too! Cool!

But what if you want to make some changes to the default Modal? What if you want it positioned differently, or with a different background?

### <strong>4. Customizable - pass in custom styles to the default Modal component to overwrite the default CSS</strong>

This is a simple solution but is my way of handling customization for an otherwise boring default Modal.

It harnesses React's principles of allowing you to pass down almost anything as a prop to the component and the `style` property inside a component that can overwrite any default CSS.

All we have to do is add a few lines to our default Modal component:

> <img src="http://i.imgur.com/nfk7MH4.png" width="80%" height="80%">

Those three lines check if an <strong>overlayStyle</strong>, <strong>contentStyle</strong>, <strong>dialogStyle</strong> prop has been sent to the default Modal component. If so, it will overwrite any conflicting style in the corresponding `div` element.

For example:

> <img src="http://i.imgur.com/HFUoB0M.png" width="80%" height="80%">

Now we can first SET <strong>dialogStyle</strong> in our LoginModal component; here, setting our default Modal Component's Dialog Box element's `backgroundColor: black`. 

Then, we can pass <strong>dialogStyle</strong> down as a prop to the default Modal Component! The Modal Component will receive the prop, and overwrite the default `backgroundColor` of our Dialog Box!

Hence, we get customization without losing reusability! You don't need to create a whole new Modal Component with its default methods like <strong>onOverlayClick</strong>. Instead, only deal with the CSS.

<strong>Sidenote:</strong> There may be detractors from this way of customizing our modals. It feels very hacky to set our custom styles this way. However, as I said above, react modals feel hacky altogether. It involves a lot of CSS manipulation to get what you want. Because React allows its developers to pass style in as a prop (which it handles and renders), it seems like a good compromise between what would otherwise be a boring one-style modal (scalable, but not customizable) and a highly-customizable modal (customizable, but not very scalable).

### In Conclusion

Modals are very fun to use and makes websites feel seamless. However, as we have explored above, the implementation isn't quite so seamless. However, this is primarily because we were concerned with making our modals in our app scalable and customizable using redux! There is so little guidance on this specific topic that I felt the need to air out my findings and my solutions to a rather complex problem. 

I must give credit to the links I referenced above -- particularly Mike Vercoelen's post: <a href="https://codersmind.com/scalable-modals-react-redux/">Scalable Modals with React and Redux</a>. Much of my code is copy-and-pasted from his (apart from how we handled the CSS). Hopefully my detailed overview was as helpful as it has been verbose. 

Good luck!