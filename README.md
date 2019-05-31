﻿# Overview

This is a coding test. The app is a recipe search app, it has 2 screens - search and detail, and uses food2fork API to get data about recipes. It intentionally doesn't have any dependencies as I've been told it's discouraged to have any


# Architecture 

The following sections discuss the architecture of different layers of the app


## Networking

Networking module consists of multiple layers stacked together

The deepest layer is `HTTPClient` which has access to `URLSession` which is used to fire requests. `URLSession` here may be swapped with any other network provider, for example, Alamofire. This class has only one generic function which fires the request (`URLRequest`) and returns `Result` with decoded object or error in a callback

The next layer is `Service` (in this case, only `RecipeService`), which accesses `HTTPClient` and passes there `URLRequest`. This layer is a high-level interface for any networking client in the app (ViewModels, essentially). The service is exposed as protocol so that it can be mocked. It uses `NetworkRoutable` to build an actual URL request

`NetworkRoutable` is the main building block of the URL request. It's a protocol, which defines the interface for endpoint models, and in turn, has a default implementation for `URLRequestConvertible` to provide actual URLRequest. In this case, there's one endpoint model - `RecipeAPIRouter`. It is an enum with cases for all the needed API methods. The enum conforms to `NetworkRoutable` and provides all the data needed to build `URLRequest`

All the above can be illustrated with the following diagram:

![Network Diagram](https://lh3.googleusercontent.com/AqVqwRAb6dxmhNajooHo8eDlywQLrmE5BWwMlxKYMITgpAduhY13QFQ5smbhFmDUfrQCxCLt--AN)


## Navigation

The app uses coordinator pattern for navigation

A coordinator is an entity which controls the navigation flow in the app. Generally speaking, each distinct linear flow should have a distinct coordinator. Coordinators are then arranged in the hierarchical tree-like order with the root in the `AppCoordinator`, which decides the flow when the app starts. In this case, the app has only one coordinator - `AppCoordinator`

Coordinators conform to `Coordinatable` protocol so that they can be stored as dependencies in other coordinators and have a concise interface. Usually, a coordinator has 2 dependencies - factories for building other coordinators and flow-related views, `CoordinatorFactory` and `ModuleFactory` correspondingly

Coordinators don't access UIKit-specific controller and instead use `NavigationRoutable` to perform actual navigation. It's a protocol, which provides an interface for navigation. In this app it has 2 implementations - `NavigationRouter` and `SplitRouter` for navigation using `UINavigationController` and `UISplitViewController` correspondingly.

All the above can be illustrated with the following diagram:

![Navigation](https://lh3.googleusercontent.com/l9Iz7s0Rmy8gB-xK030ikGtfys-bscYKEdFhEoylSDq8dxUoR6_Ynr7pGnScN80QFOGCDxEfQEPP)


## Module

The app uses coordinator pattern for navigation.

A coordinator is an entity which controls the navigation flow in the app. Generally speaking, each distinct linear flow should have a distinct coordinator. Coordinators are then arranged in the hierarchical tree-like order with the root in the `AppCoordinator`, which decides the flow when the app starts. In this case, the app has only one coordinator - `AppCoordinator`

Coordinators conform to `Coordinatable` protocol so that they can be stored as dependencies in other coordinators and have a concise interface. Usually, a coordinator has 2 dependencies - factories for building other coordinators and flow-related views, `CoordinatorFactory` and `ModuleFactory` correspondingly

Coordinators don't access UIKit-specific controller and instead use `NavigationRoutable` to perform actual navigation. It's a protocol, which provides an interface for navigation. In this app it has 2 implementations - `NavigationRouter` and `SplitRouter` for navigation using `UINavigationController` and `UISplitViewController` correspondingly

All the above can be illustrated with the following diagram:

![Navigation](https://lh3.googleusercontent.com/l9Iz7s0Rmy8gB-xK030ikGtfys-bscYKEdFhEoylSDq8dxUoR6_Ynr7pGnScN80QFOGCDxEfQEPP)

## Module

The pattern of choice is MVVM,  so each module consists of a `View` and `ViewModel` which are essentially `UIViewController` and `ViewModel` class

`View` is responsible for layout and presentation of data and passes all user actions down to `ViewModel`, which in turn access `Model` part of the app, converts data to easy-to-use presentation and handles state

`View` is connected to `ViewModel` using callbacks, another approach could be delegates but callbacks are used because of more declarative syntax

`ViewModel` doesn't know anything about its view, so it may be tested in isolation


## Special notes

The app uses coordinator pattern for navigation

A coordinator is an entity which controls the navigation flow in the app. Generally speaking, each distinct linear flow should have a distinct coordinator. Coordinators are then arranged in the hierarchical tree-like order with the root in the `AppCoordinator`, which decides the flow when the app starts. In this case, the app has only one coordinator - `AppCoordinator`

Coordinators conform to `Coordinatable` protocol so that they can be stored as dependencies in other coordinators and have a concise interface. Usually, a coordinator has 2 dependencies - factories for building other coordinators and flow-related views, `CoordinatorFactory` and `ModuleFactory` correspondingly

Coordinators don't access UIKit-specific controller and instead use `NavigationRoutable` to perform actual navigation. It's a protocol, which provides an interface for navigation. In this app it has 2 implementations - `NavigationRouter` and `SplitRouter` for navigation using `UINavigationController` and `UISplitViewController` correspondingly

All the above can be illustrated with the following diagram:

![Navigation](https://lh3.googleusercontent.com/l9Iz7s0Rmy8gB-xK030ikGtfys-bscYKEdFhEoylSDq8dxUoR6_Ynr7pGnScN80QFOGCDxEfQEPP)


## Module

The pattern of choice is MVVM,  so each module consists of a `View` and `ViewModel` which are essentially `UIViewController` and `ViewModel` class

`View` is responsible for layout and presentation of data and passes all user actions down to `ViewModel`, which in turn access `Model` part of the app, converts data to easy-to-use presentation and handles state

`View` is connected to `ViewModel` using callbacks, another approach could be delegates but callbacks are used because of more declarative syntax

`ViewModel` doesn't know anything about its view, so it may be tested in isolation


## Special notes

The architecture used and described above may be seen as overkill for such a small app - there's more boilerplate code than "real" code. However, my assumption is that the code test is used to assess my skills working on a bigger project, where this architecture makes more sense

Food2fork API returns HTML inside of recipe titles and ingredients, so there's a helper function do decode HTML to Swift String. This operation is time-consuming, however, this may be seen as a limitation of the API


## Dependencies

The app doesn't have any dependencies because I've been told not to use any. If I could, I'd use the following:

1. ReactiveSwift - for more declarative binding between `View` and `ViewModel`, as well as for wrapping network requests and other async operations
2. Alamofire - for more extensive networking
3. KingFisher - for image downloading
4. SnapKit - for in-code AutoLayout