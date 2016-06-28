# Interactive Application Architecture

by Derek Greer

[Article](http://aspiringcraftsman.com/2007/08/25/interactive-application-architecture/)

--------------------------------------------------------------------------------

_Note: The article also addresses the Presentation-Abstraction-Control Pattern, but notes were not taken._

## 1\. Introduction

The MVC, MVP, patterns are each intended to address the needs of interactive applications by separating the concerns assigned to different components within their respective architectures. While similar, each of these patterns differs slightly in their motivations and applicability to various design goals.

## 2\. The Model–View-Controller Pattern (Smalltalk-80)

The Model-View-Controller pattern is a methodology for separating the concerns of an application's domain, presentation, and user input into specialized components.

![](http://aspiringcraftsman.com/wp-content/uploads/2010/02/MVC1.png)

The **Model** refers to the data and business functionality of the application. This is often represented by a Domain Model where objects are used to model real world entities and processes by representing their properties and behavior.

The **View** is the visual representation of the Model and is comprised of the screens and widgets used within an application.

The **Controller** is a component which responds to user input such as data entry and commands issued from a keyboard or mouse. Its responsibility is to act as a bridge between the human and the application, allowing the user to interact with the screen and data.

Within the MVC pattern, a Model-View-Controller triad exists for each object intended to be manipulated by a user. The Model contains no direct link to the View or Controller, and may be modified by the View, Controller, or other objects with the system. **When notification to the View and Controller are necessary, the Model uses the [Observer Pattern](https://en.wikipedia.org/wiki/Observer_pattern) to send a message notifying observing objects that its data has changed.**

The View and Controller components work together to allow the user to view and interact with the Model. Each View is associated with a single Controller, and each Controller is associated with a single View. Both the View and Controller components maintain a direct link to the Model.

The View's responsibility can be seen as primarily dealing with output while the Controller's responsibility can be seen as primarily dealing with input. It is the shared responsibility of both the View and the Controller to interact with the Model. The Controller interacts with the Model as the result of responding to user input, while the View interacts with the Model as the result of updates to itself. Both may access and modify data within the Model as needed.

As data is entered by the user, the Controller intercepts the user's input and responds appropriately. Some user actions will result in interaction with the Model, such as changing data or invoking methods, while other user actions may result in visual changes to the View, such as the collapsing of menus, the highlighting of scrollbars, etc.

### 2.4 MVC Misconceptions

One common misconception about the relationship between the MVC components is that the purpose of the Controller is to separate the View from the Model. While the MVC pattern does decouple the application's domain layer from presentation concerns, this is achieved through the Observer Pattern, not through the Controller. The Controller was conceived as a mediator between the end user and the application, not between the View and the Model.

### 2.5 Pattern Variations and Derivatives: The Model-View-Controller Pattern for Web Applications

The classic Model-View-Controller pattern is largely no longer used today in its original form, though it has given rise to a number of variations adapted to newer development platforms.

Similar to the original pattern, the Web-based MVC pattern aids in separating the concerns of an application's domain, client-side presentation, and server side input processing into specialized components.

![](http://aspiringcraftsman.com/wp-content/uploads/2010/02/WebMVC.png)

A **Front Controller** is often introduced to handle common infrastructure concerns as well as dispatching requests to individual Controllers.

The **Model** encapsulates the data and business functionality of the application and is typically represented by a Domain Model.

In Web applications, the **View** is the content (generally HTML and associated client-side script) returned to the Web client. Depending on the implementation, Views may be text-based templates which are rendered by a view processor, or they may be objects compiled from templates which encapsulate the content to be rendered.

The **Controller** is a component which responds to user input. What differs is that rather than receiving signals directly from hardware devices such as the keyboard, mouse, etc., Web-based MVC Controllers process delegated HTTP requests (or information derived from the request depending upon the specific implementation).

Upon receiving an HTTP request, the Front Controller executes any common behavior and then uses information derived from the request to locate the concerned Controller. After a Controller is located, the request is delegated for further handling.

Once the Controller receives the specific request, the appropriate operations are performed upon the Model and control is transferred to the View.

Due to the fact that Web applications are stateless, Views are rendered anew upon each request. As such, the Observer Pattern is not used in the process of updating the View. To render the View with the appropriate state, the Controller makes the required state available in the form of a Model, a Presentation Model, or some more rudimentary form such as a bag of name/value pairs.

Depending upon the implementation, the View then renders the output stream or is parsed by a separate processor to render the output stream based upon the view state made available by the Controller.

## 3\. The Model-View-Presenter Pattern

The Model-View-Presenter pattern is a variation on the Model-View-Controller pattern, and similarly separates the concerns of an application's domain, presentation, and user input into specialized components.

The definition and distinctive characteristics of this pattern are not easily summarized due to the fact that there are several patterns commonly accepted under the name "Model-View-Presenter" which do not share the same distinctive qualities over their MVC predecessor. For this reason, this article will discuss the original Model-View-Presenter pattern along with some of its more popular variations and derivatives.

### 3.1 The Taligent Model-View-Presenter Pattern

The following diagram depicts the structure of the Taligent Model-View-Presenter pattern:

![](http://aspiringcraftsman.com/wp-content/uploads/2010/02/TaligentMVP.png)

The **Model** refers to the data and business functionality of the application.

**Selections** are components which specify what portion of the data within the Model is to be operated upon. Examples would be selections which define rows, columns, or individual elements which meet specific criteria.

**Commands** are components which define the operations which can be performed on the data. Examples might be deleting, printing, or saving data within the Model.

The **View** is the visual representation of the Model and is comprised of the screens and widgets used within an application.

**Interactors** are components which address how user events are mapped onto operations performed on the Model, such as mouse movements, keyboard input, and the selection of checkboxes or menu items. (_pmcteixeira: think of Use Cases_)

The **Presenter** is a component which orchestrates the overall interaction of the other components within the application. Its roles include the creation of the appropriate Models, Selections, Commands, Views, and Interactors, and directing the workflow within the application.

The Model refers to the data and business functionality of the application.

Selections are components which specify what portion of the data within the Model is to be operated upon. Examples would be selections which define rows, columns, or individual elements which meet specific criteria.

Commands are components which define the operations which can be performed on the data. Examples might be deleting, printing, or saving data within the Model.

The View is the visual representation of the Model and is comprised of the screens and widgets used within an application.

Interactors are components which address how user events are mapped onto operations performed on the Model, such as mouse movements, keyboard input, and the selection of checkboxes or menu items.

The Presenter is a component which orchestrates the overall interaction of the other components within the application. Its roles include the creation of the appropriate Models, Selections, Commands, Views, and Interactors, and directing the workflow within the application.

The most notable collaborative differences between the Taligent Model-View-Presenter pattern and the Model-View-Controller pattern are found within the Presenters and Interactors.

The Presenter acts as an overall manager for a particular subsystem within an application. It maintains the lifecycle and relationships between the Views, Interactors, Commands, Selections, and Model. The responsibility for intercepting user events is governed by Interactors; therefore Presenters are not needed for each widget on a given View as were Smalltalk-80 Controllers. There is generally a single Presenter per View, though in some cases a Presenter may manage multiple logically related Views.

Interactors are somewhat analogous to Smalltalk-80 Controllers. They are the components which respond to user events and in turn call the appropriate Commands and Selections of the Model.

### 3.2 The Dolphin Smalltalk Model-View-Presenter Pattern

The Dolphin Smalltalk team simplified the Taligent MVP pattern by eliminating Interactors, Commands, and Selections from the pattern's formal description. This in turn simplified the role of the Presenter, changing it from a subsystem controller to a component which mediated updates to the Model on behalf of the View.

![](http://aspiringcraftsman.com/wp-content/uploads/2010/02/MVP.png)

The **Model** refers to the data and business functionality of the application.

The **View** is the visual representation of the Model and is comprised of the screens and widgets used within an application.

The **Presenter** is a component which contains the presentation logic which interacts with the Model.

Within the Dolphin MVP pattern, Views intercept the initial user events generated by the operating system. This choice was primarily the result of development on the Microsoft Windows operating system whose native widgets already handled most controller functionality. In a few cases the View responded to user events by updating the Model directly, but in most cases user events were delegated to the Presenter. **Implied by the Dolphin Smalltalk description is that Views only delegated events when updates to the Model were required, thus leaving all other presentation logic within the View.**

As with the Taligent MVP pattern, there is usually a single Presenter which handles updates to the Model for a specific View.

As with the Model-View-Controller pattern, the View is notified of any changes to the Model using the Observer Pattern and responds by updating the relevant portions of the screen.

#### Dolphin Smalltalk MVP vs. Smalltalk-80 MVC

On the surface, the differences between the Dolphin Smalltalk MVP pattern and the Smalltalk-80 MVC pattern are difficult to discern. Both of the patterns contain a triad. Both of the patterns contain a Model and View which are virtually identical in function. Both the Smalltalk-80 Controller and the Dolphin Smalltalk Presenter are involved in updating the Model. Both of the patterns use the Observer Pattern to update the View when changes occur to the Model. With all these similarities, it is clear why so much confusion surrounds understanding how the Model-View-Presenter pattern sets itself apart from the Model-View-Controller pattern. The key is in understanding the primary functions which Presenters and Controllers play within their respective triads.

**Within the original Model-View-Controller pattern, the primary purpose of the Controller was to intercept user input. The Controller's role of updating the Model was largely a byproduct of this function rather than an inherent part of its purpose.**

**Conversely, within the Dolphin Smalltalk Model-View-Presenter pattern, the primary purpose of the Presenter was to update the Model. The Presenter's role of intercepting events delegated by the View was largely a byproduct of this function rather than an inherent part of its purpose.**

Within the Dolphin Smalltalk MVP pattern, the role of intercepting the user's input was moved to the View. This effectively eliminated the original need for Controllers or Interactors altogether.

So then, while the Dolphin Smalltalk MVP and the Smalltalk-80 MVC patterns may appear similar on the surface, Presenters and Controllers differ in the purposes they were conceived to address.

## 4\. The Fowler Patterns

During his research and preparation of material on presentation layer patterns in 2006 for an upcoming book, Martin Fowler decided that the treatment given to the design intensions behind today's use of the Model-View-Presenter pattern be divided under the names [Supervising Controller](http://martinfowler.com/eaaDev/SupervisingPresenter.html) and [Passive View](http://martinfowler.com/eaaDev/PassiveScreen.html). This distinction was made around the level of responsibility the Presenter/Controller component of the pattern takes on for presentation layer logic.

However, this distinction renders patterns which describe solutions not specific to the Model-View-Presenter pattern. While discussed here within the context of the Model-View-Presenter pattern, these patterns are best understood as facilitating patterns (as with the Observer Pattern) rather than variations of the Model-View-Presenter pattern.

### 4.1 The Supervising Controller Pattern

The Supervising Controller pattern separates an application's concerns of presentation and presentation logic into the specialized components of View and Controller, with the View assigned the responsibility of simple presentation logic and the Controller assigned the responsibilities of responding to user input and handling complex presentation logic.

![](http://aspiringcraftsman.com/wp-content/uploads/2010/02/SupervisingController.png)

_Note: The model is shown with reduced emphasis to denote its peripheral role to the pattern description._

The **View** is the visual components used within an application such as screens and widgets.

The **Controller** is a component which processes user events and the complex presentation logic within an application.

Within the Supervising Controller pattern, Views delegate user events to the Controller which in turn interacts with the business domain of the application.

For simple presentation logic, the View uses data binding techniques and the Observer Pattern to update itself when changes occur within the application.

Complex presentation logic, particularly any logic one desires to unit test, is delegated to the Presenter.

### 4.2 The Passive View Pattern

The Passive View pattern separates an application's concerns of presentation and presentation logic into the specialized components of View and Controller, with the Controller taking on the responsibility for responding to user events and presentation logic.

![](http://aspiringcraftsman.com/wp-content/uploads/2010/02/PassiveView.png)

_Note: The model is shown with reduced emphasis to denote its peripheral role to the pattern description._

The **View** is the visual components used within an application such screens and widgets.

The **Controller** is a component which processes user events and the presentation logic within an application.

Within the Passive View pattern, Views delegate user events to the Controller which in turn interacts with the business domain of the application and/or updates the View. The View maintains no link to the domain layer and relies solely on the Controller for all presentation related logic.

Controllers within this pattern take on a mediating role between the Views and domain logic strategy used. **This formalizes a role often erroneously ascribed to Controllers within the Model-View-Controller pattern.**

## Pattern Comparisons

Pattern                            | Domain                                       | Presentation                           | Control
:--------------------------------- | :------------------------------------------- | :------------------------------------- | :----------------------------------------------------------------------------
Smalltalk-80 Model-View-Controller | Model – domain objects within an application | View - Visual presentation to the user | Controller – human to Model connector; Intercepts user input
Taligent Model-View-Presenter      | Same as above                                | Same as above                          | Presenter – subsystem component connector; manages application subsystems
Dolphin Model-View-Presenter       | Same as above                                | Same as above                          | Presenter – presentation to domain connector; manages access to Model updates
Passive View                       | N/A                                          | Same as above                          | Controller – manages presentation logic
Supervising Controller             | N/A                                          | Same as above                          | Controller – assists with presentation logic

### From other references:

![](http://i.stack.imgur.com/t2kzD.png) ![](http://i.stack.imgur.com/adMfR.png)
