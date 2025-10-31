# Dutch Rose Placeables Toolkit

## Introduction

## Installation

You can use the unity package manager to install the library:

1. Open package manager from Window -> Package Management -> Package Manager
![package manager](assets/placeablestoolkit/packagemanager.png){ width="300",align=right }

2. Choose the Plus Icon -> Install package from git URL...
![package manager](assets/placeablestoolkit/installfromgit.png){ width="300",align=right }

3. Copy the `TODO: git url` and hit install.

## Core Components Breakdown

``` mermaid
classDiagram
  ARLifeCycleManager <-- AbstractPlaceablesManager:Interfaces with
  ARLifeCycleManager <-- LifecycleUIResponder:Subscribes to
  ARLifeCycleManager <-- PlaceablesGlobalConfig:Defines configuration for
  AbstractPlaceablesManager <-- ARPlaceablesManager:Implements
  AbstractPlaceablesManager <-- Placeable:Registers to
  ARImageTargerManager <-- AbstractPlaceablesManager:Leverages
  class LifecycleUIResponder{
    Event based triggers for an AR Lifecycle
  }
  class ARLifeCycleManager{
    Point of entry for an AR scene
    Initiates connection with underlying AR SDK
    Parses a PlaceablesGlobalConfig object.
  }
  class PlaceablesGlobalConfig{
    ScriptableObject 
    allowing developers to configure an AR session
    in a flow-chart like manner from the inspector
  }
  class ARPlaceablesManager{
    ARFoudation specific impl. of a 
    placeables manager
  }
  class AbstractPlaceablesManager{
    Abstract interface 
    allowing `placeables` to register themselves for placement
  }
  class ARImageTargerManager{
    MonoBehaviour 
    managing lifecycle of an underlying ARImageTracking component
  }
  class Placeable{
    Scriptable object
    defining placement behaviour for AR objects
  }
```

### PlaceablesGlobalConfig

![](assets/placeablestoolkit/PlaceablesGlobalConfig.png)

- **Use ARKit Coaching Overlay**: shows the iOS only Coaching Overlay when enabled, its functionality is detailed by Apple on [this url](https://developer.apple.com/documentation/arkit/arcoachingoverlayview)

- **Use Occlusion**: enables depth mask/occlusion techniques, this works better on more modern phones as it can use lidar/depth sensors.

- **DestroyPointCloudWhenTracking**: tells the `lifecyclemanager` to destroy the `point cloud` when tracking is first found.

- **DestroyPointCloudWhenPlanesFound**: tells the `lifecyclemanager` to destroy the `point cloud` when planes are first found.

- **AndroidMockInfinite**: mocks the non-existent `infinite plane` on android systems.

- **ResetARSessionOnDestroy**: destroy an AR session when the containing scene is unloaded.

- **CancelTouchesOnGestureDetection**: used when ‘pinching’ is enabled, prevents a touch on the screen from triggering multiple gestures at the same time.

- **On*Event**: a number a `GameEvents` allowing developers to respond to AR lifecycle events using the `SOArchitecture/Events/GameEvent.cs` Game Event system without having to write code. 

??? info "More about `GameEvents`"

    `GameEvents` is simply a concept to handle events in your application cleaner.
    It's a simple `ScriptableObject` with a listener alongside that can be invoked based on the events you want. OnTrackingFound and the rest of On* events are some use-cases of this pattern.

??? info "More info"
     Every AR scene always contains exactly one ARLifeCycleManager which initializes the AR foundation library when the scene loads.

### Placeables

![](assets/placeablestoolkit/placeable-option-menu.png)

To simplify development in a codeless manner, the `Placeables` package lets developers define AR functionalities in a configurable manner from the inspector using `Placeable ScriptableObjects`. 

A placeable is an asset on the filesystem that can be supplied to an implementation of the `AbstractPlaceableManager` class. 


#### Placeables placement strategies

![](assets/placeablestoolkit/placeable-inspec-placement.png)

- **TapOnPlane**: Physically mark the place to instantiate an object by tapping.

![Tap on Plane](assets/placeablestoolkit/placeable-inspec-placement-tap.png)

- **CenterScreen**: Drag an object around on the center of the screen, to then confirm placement by tapping.

![Center Screen](assets/placeablestoolkit/placeable-inspec-placement-center.png)

- **CenterScreenImmediate**: Instantiates an object using a raycast from the center of the screen. It does not ask the user to confirm and places immediately. 

![Center Screen Imidiate](assets/placeablestoolkit/placeable-inspec-placement-center-imid.png)

- **ImageTarget**: A PNG that will be used for imagetracking. The prefab is then placed when the ImageTargetManager finds said image in the camera view. 

![Image Target](assets/placeablestoolkit/placeable-inspec-placement-imagetarget.png)

- **RelativeTransform**: Place an object at a given distance and rotation from the user (currently used by ByAR) 

![Relative Transform](assets/placeablestoolkit/placeable-inspec-placement-reltrans.png)


## Dependencies

- AR Foundation
- Google ARCore XR Plugin
- Apple ARKit XR Plugin
- XR Plugin Management
- Editor Coroutines
- XR Interaction Toolkit

