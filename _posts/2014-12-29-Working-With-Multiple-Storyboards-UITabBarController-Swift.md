---
layout: post
title: "Working with Multiple Storyboard in a UITabBarController using Swift"
description: "Working with Multiple Storyboard in a UITabBarController using Swift"
tags: [swift, ios, uitabbarcontroller]
---

I am still working with my two friends on an iOS app. Now that the application is starting to get bigger, we realized that working with one main storyboard is not sustainable. We decided to separate the features into several storyboards. Our development became more manageable after doing this.

Our application is using a UITabBarController. We separated each tab to a storyboard and linked these back into the UITabBarController. We are using Swift as the main language for development.

To make this setup work, I had to create a main UITabBarController where I added the storyboards. The UITabBarController implementation looks something like this.

    class MainTabBarController: UITabBarController {

        override func viewDidLoad() {
            super.viewDidLoad()

            var firstStoryboard:UIStoryboard = UIStoryboard(name: "FirstStoryboard", bundle: nil)
            var firstViewController:UIViewController = firstStoryboard.instantiateViewControllerWithIdentifier
              ("FirstViewNavigation") as UIViewController

            var secondStoryboard:UIStoryboard = UIStoryboard(name: "SecondStoryboard", bundle: nil)
            var secondViewController:UIViewController = secondStoryboard.instantiateViewControllerWithIdentifier
              ("SecondNavigation") as UIViewController

            self.viewControllers = [firstViewController, secondStoryboard]
        }

        ...
    }

I had to update the AppDelegate to extend from UITabBarControllerDelegate. Also, I needed to instantiate the UITabBarController I created above and make it the root view controller. The AppDelegate would look something like below

    @UIApplicationMain
    class AppDelegate: UIResponder, UIApplicationDelegate, UITabBarControllerDelegate {

        var window: UIWindow?

        func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions:
          [NSObject: AnyObject]?) -> Bool {
            // Override point for customization after application launch.

            self.window!.rootViewController = MainTabBarController();
            self.window!.makeKeyAndVisible()

            return true
        }

        ...
      }

After doing this, our app is now using multiple storyboards linked to the UITabBarController. It took me a bit of time to get it working as I found it hard to find resources on the internet which did something similar. I hope this helps someone out there looking to make something similar work with Swift, and iOS 8.