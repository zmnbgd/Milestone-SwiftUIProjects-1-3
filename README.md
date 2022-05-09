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


Key points


There are three important points that are worth going over in more detail. This will partly be reviewing what we learned – going over it again with different examples to help make sure they are clear – but I also want to take this chance to answer some questions that are likely to have cropped up so far.


Structs vs classes
First, something that should hopefully be fairly fresh in your memory: structs and classes. These are both ways of letting us build complex data types with properties and methods, but the way they work – more specifically, how they differ – matters.


If you remember, there are five key differences between structs and classes:
1. Classes don’t come with a memberwise initializer; structs get these by default.
2. Classes can use inheritance to build up functionality; structs cannot.
3. If you copy a class, both copies point to the same data; copies of structs are always unique.
4. Classes can have deinitializers; structs cannot.
5. You can change variable properties inside constant classes; properties inside constant structs are fixed regardless of whether the properties are constants or variables.

In Apple’s original programming language, Objective-C, we used classes for pretty much everything – we didn’t have a choice, because it was really baked into the way things worked.

In Swift we do have a choice, and that choice ought to be based upon those factors above. I say “ought to be” because it’s not uncommon to see folks who don’t care about these differences, and so use either class or struct all the time without considering the ramifications of that choice.


Whether you choose structs or classes is down to you and the specific problems you’re solving. When someone reads your code, is your intent clear to them?

Tip: One of the fascinating details of SwiftUI is how it completely inverts how we use structs and classes. In UIKit we would use structs for data and classes for UI, but in SwiftUI it’s completely the opposite – a good reminder of the importance of learning things, even if you think they aren’t immediately useful. 

Working with ForEach

The second thing I’d like to discuss is ForEach, which we have used with code like this:

ForEach(0 ..< 100) { number in
    Text("Row \(number)")
}

ForEach is a view just like most other things in SwiftUI, but it allows us to create other views inside a loop. In doing so, it also allows us to bypass the ten-child limit that SwiftUI imposes – the ForEach itself becomes one of the 10, rather than the things inside it.
Now consider a string array like this:

let agents = ["Cyril", "Lana", "Pam", "Sterling"]

How could we loop over those and make text views?
One option is to use the same construction we already have:

VStack {
    ForEach(0..<agents.count) {
        Text(agents[$0])
    }
}

But SwiftUI offers us a second alternative: we can loop over the array directly. This takes a little more thinking, because SwiftUI wants to know how it can identify each item in the array.

Think about it: if we loop over a four-item array we will create four views, but if body gets re-invoked and our array now contains five items, SwiftUI needs to know which view is new so it can be shown in the UI. The last thing SwiftUI wants to do is throw away its whole layout and start from scratch every time a small change is made. Instead, it wants to do the least amount of work possible: it wants to leave the existing four views in place and add only the fifth.

So, we come back to how Swift can identify values in our array. When we were using a range such as 0..<5 or 0..<agents.count, Swift knew for sure that each item was unique because it would use the numbers from the range – each number was used only once in the loop, so it would definitely be unique.

In our array of strings that’s no longer possible, but we can clearly see that each value is unique: the values in ["Cyril", "Lana", "Pam", "Sterling"] don’t repeat. So, what we can do is tell SwiftUI that the strings themselves – “Cyril”, “Lana”, etc – are what can be used to identify each view in the loop uniquely.

In code, we’d write this:

VStack {
    ForEach(agents, id: \.self) {
        Text($0)
    }
}

So rather than loop over integers and use that to read into the array, we’re now reading items in the array directly – just like a for loop would do.

As you progress with SwiftUI we’ll look at a third way of identifying views using the Identifiable protocol, but that will come in time.

Working with bindings

When we use controls such as Picker and TextField we create two-way bindings for them to some sort of @State property using $propertyName. This works great for simple properties, but sometimes – only sometimes, hopefully! – you might want something more advanced: what if you want to run some logic to calculate the current value? Or what if you want to do more than just stash away a value when it’s written?

If we want to react to changes in a binding, we might try to leverage Swift’s didSet property observer, but you’d be disappointed. This is where custom bindings come in: they can be used just like @State bindings, except we get complete control over how they work.

Bindings aren’t magic: @State takes away some boring boilerplate code for us, but it’s perfectly possible to create and manage bindings by hand if you want to. Again, I’m not showing you this because it’s common, because it’s really not; my hope is that you’ll need to do this rarely. Instead, I’m showing it to you because I want to eliminate the idea that SwiftUI is doing some sort of magic on your behalf.

Everything that SwiftUI does for us can be done by hand, and although it’s nearly always better to rely on the automatic solution it can be really helpful to take a peek behind the scenes so you understand what it’s doing on your behalf.
First let’s look at the simplest form of custom binding, which just stores the value away in another @State property and reads that back:

struct ContentView: View {
    @State private var selection = 0

    var body: some View {
        let binding = Binding(
            get: { selection },
            set: { selection = $0 }
        )

        return VStack {
            Picker("Select a number", selection: binding) {
                ForEach(0..<3) {
                    Text("Item \($0)")
                }
            }
            .pickerStyle(.segmented)
        }
    }
}

So, that binding is effectively just acting as a passthrough – it doesn’t store or calculate any data itself, but just acts as a shim between our UI and the underlying state value that is being manipulated.

However, notice that the picker is now made using selection: binding – no dollar sign required. We don’t need to explicitly ask for the two-way binding here because it already is one.

If we wanted to, we could create a more advanced binding that does more than just pass through a single value. For example, imagine we had a form with three toggle switches: does the user agree to the terms and conditions, agree to the privacy policy, and agree to get emails about shipping.

We might represent that as three Boolean @State properties:

@State var agreedToTerms = false
@State var agreedToPrivacyPolicy = false
@State var agreedToEmails = false

Although the user could just toggle them all by hand, we could use a custom binding to do them all at once. This binding would be true if all three of those Booleans were true, but if it got changed then it would update them all, like this:

let agreedToAll = Binding(
    get: {
        agreedToTerms && agreedToPrivacyPolicy && agreedToEmails
    },
    set: {
        agreedToTerms = $0
        agreedToPrivacyPolicy = $0
        agreedToEmails = $0
    }
)

So now we can create four toggle switches: one each for the individual Booleans, and one control switch that agrees or disagrees to all three at once:

struct ContentView: View {
    @State private var agreedToTerms = false
    @State private var agreedToPrivacyPolicy = false
    @State private var agreedToEmails = false

    var body: some View {
        let agreedToAll = Binding<Bool>(
            get: {
                agreedToTerms && agreedToPrivacyPolicy && agreedToEmails
            },
            set: {
                agreedToTerms = $0
                agreedToPrivacyPolicy = $0
                agreedToEmails = $0
            }
        )

        return VStack {
            Toggle("Agree to terms", isOn: $agreedToTerms)
            Toggle("Agree to privacy policy", isOn: $agreedToPrivacyPolicy)
            Toggle("Agree to receive shipping emails", isOn: $agreedToEmails)
            Toggle("Agree to all", isOn: agreedToAll)
        }
    }
}

Again, custom bindings aren’t something you’ll want that often, but it’s so important to take the time to look behind the curtain and understand what’s going on. Even though it’s incredibly smart, SwiftUI is just a tool, not magic!


