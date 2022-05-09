DAY 25

What you learned

So far we have covered:


* Building scrolling forms that mix text with controls such as Picker, which SwiftUI turns into a beautiful table-based layout where new screens slide in with fresh choices.
* Creating a NavigationView and giving it a title. This not only allows us to push new views onto the screen, but also lets us set a title and avoid problems with content going under the clock.
* How to use @State to store changing data, and why it’s needed. Remember, all our SwiftUI views are structs, which means they can’t be changed without something like @State.
* Creating two-way bindings for user interface controls such as TextField and Picker, learning how using $variable lets us both read and write values.
* Using ForEach to create views in a loop, which allows us to make lots of views all at once.
* Building complex layouts using VStack, HStack, and ZStack, as well as combining them together to make grids.
* How to use colors and gradients as views, including how to give them specific frames so you can control their size.
* How to create buttons by providing some text or an image, along with a closure that should be executed when the button is tapped.
* Creating alerts by defining the conditions under which the alert should be shown, then toggling that state from elsewhere.
* How (and why!) SwiftUI uses opaque result types (some View) some extensively, and why this is so closely linked to modifier order being important.
* How to use the ternary conditional operator to create conditional modifiers that apply different results depending on your program state.
* How to break up your code into small parts using view composition and custom view modifiers, which in turn allows us to build more complex programs without getting lost in code.

One thing I want you to think about is what it means to be a “view” in SwiftUI. Before you started this course you might well have thought that Color.red couldn’t possibly be a view, but it is. You’ve also seen how LinearGradient is a view, which means it’s easy to use inside our layouts.

But what about VStack? Or Group? Or ForEach? Are those views?

Yes! Those absolutely are all views in SwiftUI, which is what makes the framework so remarkably composable – we can put a ForEach inside a ForEach inside a Group inside a VStack, and it all works.

Remember, all something needs to do in order to conform to the View protocol is to have a single computed property called body that returns some View.

Previously we looked very closely at protocols, protocol extensions, and protocol-oriented programming in Swift, and you might have wondered why this was all so important. Well, now I hope you can see: the View protocol is the centerpiece of SwiftUI – anything can conform to it and start taking part in layouts, in just a few simple lines of code.

In other user interface frameworks (including Apple’s own UIKit), classes were used for this work. This meant that if you had some existing types and you wanted to use them for layout, you needed to make them inherit from UIView - which in turn meant getting over 200 properties and methods that you probably didn’t need, as well as a raft of other functionality used behind the scenes.

In SwiftUI none of that happens: we just add a single View conformance. This is the power of protocols and protocol extensions, and this is what makes protocol-oriented programming so important – if we add a single body property to our type, SwiftUI knows how to use that to do layout and rendering. 

Now, if you were paying attention you might have noticed a curiosity: when we make any sort of SwiftUI view we need to make it return some View – we make a view, and it returns one or more other views. Those views have their own body properties that in turn return views, and those views have their own body properties that… well, you get the point.
This seems like SwiftUI has built an infinite loop for itself: if all views are made up of other views, where does it actually end?
Obviously it does end otherwise none of our SwiftUI code would actually work. And the trick lies in something Apple calls primitive views – the absolute bare building blocks of SwiftUI, which conform to View but return some fixed content rather than rendering some other kind of view instead.

There are quite a few of these building blocks, and they won’t come as much of a surprise – Text is one, for example, as is Image, Color, Spacer, and more. Ultimately all the UI we build is created on top of these building blocks, which is what breaks the seemingly infinite loop.

