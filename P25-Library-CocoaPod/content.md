---
title: "Installing Libraries Using CocoaPods"
slug: libraries-cocoapods
---

Earlier in this project, we mentioned _CocoaPods_ as a great way to manage your external libraries. Now we are going to show you how to find these libraries, and we are going to add one to power the `postTimeLabel` in the header cell.


#Installing a Library

For the feature we want to implement - displaying the amount of time that has passed since a post was created - we want to use the _DateTools_ library.
It will help us to convert time spans into human readable strings.

Now how do you find such a library on your own? **Google!** For example _"Date time since iOS library"_. In many cases your search terms will lead you to a GitHub page like the one shown below:

![DateTools Github readme](finding_library.png)

That GitHub page will typically contain the _CocoaPod_ name of the library, which allows you to add it your _Podfile_. Additionally, instead of using Google, you can search for Cocoapods directly on the [CocoaPods](https://cocoapods.org) website.


> [action]
> Add the _DateTools_ dependency to your _Podfile_ so that it looks like this:
>
	# Uncomment this line to define a global platform for your project
	platform :ios, '9.0'
>	
	target 'Makestagram' do
	  # Comment this line if you're not using Swift and don't want to use dynamic frameworks
	  use_frameworks!
>	
	  pod 'Bond', '4.0.0'
	  pod 'DateTools'
	  pod "ConvenienceKit"
	  pod 'Parse'
	  pod 'ParseFacebookUtilsV4'
	  pod 'ParseUI'
>	
	  target 'MakestagramTests' do
	    inherit! :search_paths
	    # Pods for testing
	  end
>	
	end

> Then **close** your Xcode project, open Terminal, navigate to your project directory, and run:
>
    pod install

Now your dependencies will be download and installed.

> [action]
>  Re-open the **workspace** that _CocoaPods_ has generated:
> ![Makestagram.xcworkspace in Finder](workspace.png)

#Importing the Library

If you have installed a Swift library, you can import it with the regular, well known `import` statement in any of your Swift files, e.g.:

    import MyLibrary

However, many of the Libraries that you will use will be written in Objective-C, Apple's original language for iOS. These libraries need to be imported a little bit differently.

To import Objective-C libraries, you need to add them to a _Bridging Header_. Our template project already comes with such a bridging header, so let's add the _DateTools_ library to it.

> [action]
> Add the _DateTools_ library to _Makestagram's_ bridging header as shown below. Open Makestagram-Bridging-Header.h and add the line `#import <DateTools/DateTools.h>`:

![Adding to Makestagram-Briding-Header.h](add_lib.png)

#Using the Library

Now we can finally use the Library to display how long ago a post was created!
Let's extend the `PostSectionHeaderView` to use the new library.

> [action]
> Extend the `didSet` observer of the `post` property in the `PostSectionHeaderView` as following:
>
    var post: Post? {
        didSet {
            if let post = post {
                usernameLabel.text = post.user?.username
                // 1
                postTimeLabel.text = post.createdAt?.shortTimeAgoSinceDate(NSDate()) ?? ""
            }
        }
    }

1. We are reading the `createdAt` date from the `post`. This is a property that Parse sets automatically on all `PFObjects`. Then we use an extension provided by the _DateTools_ library: `shortTimeAgoSinceDate(_:)`. This method takes a comparison date. By calling `NSDate()` we create a date object with the current time. If the post has been created 4 hours ago, this line of code will generate the string _"4h"_. Since `createdAt?` is an optional, we use the `??` operator to fall back to an empty string, just in case the `createdAt` date is nil.

Awesome! Now it's once again time to test this new feature. When running the app you should now see that the header cells display how long ago a post has been created:

![PostSectionHeaderView with working age label](working.png)

#Conclusion

In this step you have learned how to leverage the power of open source software in your project. There couldn't be a better way to end the _Makestagram_ tutorial!

Now it's time to take everything you have learned and build your very own app! And always remember: some developer might have already solved your problem for you. The answers are at your fingertips!
