# Introduction

User consent is not limited to the web, but applies to all platforms that collect user data. This includes mobile devices.

# Current Release : AxeptioSDK @0.3.6

## Author

Axeptio

## License

AxeptioSDK is available under the MIT license. See the LICENSE file for more information.

## Requirements

Minimum iOS version: **12.x**

Better with xCode **14.x.x**

## Improvments

#### **0.3.1**
- iOS 11
- xCode 11

####  **0.3.3**
- iOS 12 
- xCode 12
- fixes 
	- [AXE-665] fix crash when one or more H, S B values are missing in the paintT ransfrom JSON Item
	
####  **0.3.4**
 - iOS 12
 - xCode 13
 - fixes
 	- [UX Improvements] - reduce left and right horizontal insets to provide a better width
	- [fixes AXE-1601] - in cookie, vendor, the domain turns out to be optional and not mandatory

####  **0.3.6**
 - iOS 12
 - xCode 14
 - fixes
 	 - remove unnecessary files that prevented publishing something on the App Store
	the signing process failed to sign them.
	these files were added by mistake 
	

# Installation

### 1. If you haven’t already, install the latest version of [CocoaPods](https://guides.cocoapods.org/using/getting-started.html#installation).

### 2. If you don’t have an existing Podfile, create a new one by running the command:

```bash
pod Init
```

### 3. Add `pod 'AxeptioSDK'` to your Podfile:

```ruby
source 'https://github.com/CocoaPods/Specs.git'
platform :ios, '12.0'
use_frameworks!

target 'MyApp' do
  pod 'AxeptioSDK'
end

post_install do |installer|
	installer.pods_project.targets.each do |target|
		target.build_configurations.each do |config|
			config.build_settings['BUILD_LIBRARY_FOR_DISTRIBUTION'] = 'YES'
		end
	end
end
```

### 4. Run the following command:

```bash
pod install
```

### 5. In the future, to update the SDK to its latest version, run the command:

```bash
pod update AxeptioSDK
```

```bash
pod update
```

### 6. Cleaning up your project from everything related to CocoaPods
```bash
pod deintegrate
```

##### Remark
- by simply deleting the files (Podfile, Podfile.lock, xcworkspace file (package folder in fact) and Pods Folder, you do not delete the change that was made in your xCode project file settings

# Getting started

## Swift

In the main controller of your applmication, import the `AxeptioSDK` module, initialize the SDK by calling the `initialize` method providing a `clientId` and a `version`. Once initialization is complete, you can make the widget appear by calling the `showConsentController` method.

```swift
import UIKit
import AxeptioSDK

class ViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()

        // Set a custom token
        // Axeptio.shared.token = "auto-generated-token-xxx"

        Axeptio.shared.initialize(clientId: "<Client ID>", version: "<Version>") { error in
	    // Handle error
	    // You could try to initialize again after some delay for example
            Axeptio.shared.showConsentController(in: self) { error in
                // User has made his choices
		// We can now enable or disable the collection of metrics of the analytics library
                if Axeptio.shared.getUserConsent(forVendor: "<Vendor name>") ?? false {
                    // Disable collection
                } else {
                    // Enable collection
                }
            }
        }
    }
}
```

If your application supports multiple languages, you have probably created a different version for each of them in the Axeptio [administration web page] (https://admin.axeptio.eu). In this case you can store the version for each language in the `Localizable.strings` file and use `NSLocalizedString` to get the appropriate version for the user.

## Objective-C

```objective-c
#import "ViewController.h"
#import <AxeptioSDK/AxeptioSDK-Swift.h>

@interface ViewController ()

@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];

    // Set a custom token
    // [Axeptio setToken:@"auto-generated-token-xxx"];

    [Axeptio initializeWithClientId:@"<Client ID>" version:@"<Version>" completionHandler:^(NSError *error) {
        // Handle error
        // You could try to initialize again after some delay for example
        [Axeptio showConsentControllerWithInitialStepIndex:0 onlyFirstTime:TRUE in:self animated:YES completionHandler:^(NSError *error) {
            // User has made his choices
            // We can now enable or disable the collection of metrics of the analytics library
            if ([Axeptio getUserConsentForVendor:@"<Vendor name>"]) {
                // Disable collection
            } else {
                // Enable collection
            }
        }];
    }];
}


@end
```

## API Reference

### Apple Tracking User Data permission
- https://developer.apple.com/app-store/user-privacy-and-data-use/


### Properties

#### token

The `token` property can be used to set a custom token. By default, a random identifier is set.

This property is particularly useful for apps using webviews. By opening the webview while passing the token in the `axeptio_token` querystring parameter, the consent previously given in the app will be reused on the website if it uses the web SDK.

### Methods

#### initialize

The `initialize` function initializes the SDK by fetching the configuration and calling the completion handler when done. If it fails (because of network for example) it is OK to call the `initialize` function again.

```swift
func initialize(clientId: String, version: String, completionHandler: @escaping (Error?) -> Void)
```

#### showConsentController

The `showConsentController` function shows Axeptio's widget to the user in a given view controller and calls the completion handler when the user has made his choices. If `onlyFirstTime` is true and the user has already made his choices in a previous call the widget is not shown and the completion is called immediately. However if the configuration includes new vendors then the widget is shown again. You can specify an `initialStepIndex` greater than 0 to show a different step directly.

```swift
func showConsentController(initialStepIndex: Int = 0, onlyFirstTime: Bool = true, in viewController: UIViewController, animated: Bool = true, completionHandler: @escaping (Error?) -> Void) -> (() -> Void)?
```

If the widget is displayed, the function returns a reject handler that you can call to hide the widget if necessary. Otherwise, it returns nil.

#### getUserConsent

The `getUserConsent` function returns an optional boolean indicating if the user has made his choice for given vendor and whether or not he gave his consent. If the returned value is `nil` it either means the vendor was not present in the configuration or the widget has not been presented to the user yet.

```swift
func getUserConsent(forVendor name: String) -> Bool?
```

#### setUserConsentToDisagreeWithAll

The `setUserConsentToDisagreeWithAll` function sets consent for all vendors to false and saves the preference. This function is useful when using application tracking transparency. If a user refuses the tracking permission request, call this function to have the CMP not displayed and the user's consent saved in the Axeptio consent log.


# References

- CocoaPods
  - https://cocoapods.org
- All CocoaPods' Guide
  - https://guides.cocoapods.org
- Installing CocoaPods with Homebrew
  - https://formulae.brew.sh/formula/cocoapods
- Getting started
  - https://guides.cocoapods.org/using/getting-started.html
- Using CocoaPods
  - https://guides.cocoapods.org/using/using-cocoapods.html
- pod install vs pod update
  - https://guides.cocoapods.org/using/pod-install-vs-update.html
- CocoaPods Command Line reference
  - https://guides.cocoapods.org/terminal/commands.html
- What is a Podfile ?
  - https://guides.cocoapods.org/using/the-podfile.html
- Podfile syntax reference
  - https://guides.cocoapods.org/syntax/podfile.html
- How to remove CocoaPods from Xcode Project ?
  - https://medium.com/app-makers/how-to-remove-cocoapods-from-xcode-project-5166c19152
