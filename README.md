# Mastering Iced.rs Paper

This paper centralizes all knowledge you need to create a desktop (and web-app) application with Iced, Rust, tokio and Postgres.

<!-- mtoc-start -->

* [The Why](#the-why)
* [How is this Paper different?](#how-is-this-paper-different)
* [How to learn?](#how-to-learn)
* [What is Iced.rs?](#what-is-icedrs)
  * [Why choose Iced.rs?](#why-choose-icedrs)
  * [Backdraws of Iced? (not really)](#backdraws-of-iced-not-really)
  * [Anatomy of an Iced Application](#anatomy-of-an-iced-application)
  * [Pattern to build an Iced application](#pattern-to-build-an-iced-application)
* [The State](#the-state)
  * [What you need to know](#what-you-need-to-know)
  * [Splitting the State accros multiple structs](#splitting-the-state-accros-multiple-structs)
* [The Message](#the-message)
  * [Communication Between the UI Elements and the Application Logic are done through messages](#communication-between-the-ui-elements-and-the-application-logic-are-done-through-messages)
    * [Defining the Message struct](#defining-the-message-struct)
* [The Logic or Message Handling](#the-logic-or-message-handling)
  * [Defining the Message Handler](#defining-the-message-handler)
    * [Launching functions inside the Message handlers](#launching-functions-inside-the-message-handlers)
    * [When there are no tasks](#when-there-are-no-tasks)
    * [Launching async functions](#launching-async-functions)
      * [Task::perform](#taskperform)
      * [Task::batch](#taskbatch)
  * [The User Interface](#the-user-interface)
    * [text](#text)
    * [text_input](#text_input)
    * [button](#button)
    * [stack!](#stack)
    * [opaque](#opaque)
    * [mouse_over](#mouse_over)
  * [Themes](#themes)
* [Multiple Screen Apps](#multiple-screen-apps)
  * [When to use?](#when-to-use)
  * [Pattern](#pattern)
  * [Need to master before](#need-to-master-before)
    * [The Into Trait](#the-into-trait)
    * [The Clone Trait](#the-clone-trait)
    * [The Element (Iced)](#the-element-iced)
    * [impl trait](#impl-trait)
    * [impl Into<Element<'a + Message>>](#impl-intoelementa--message)

<!-- mtoc-end -->

# The Why

This guide is written (mostly for myself) to centralize all knowledge about iced.rs in one place. Iced is an amazing tool, and it deserves
high-quality and complete learning resource to empower more engineers to master it.

Although the classic counter, todo and other examples are good as a showcase, they are far from good enough to really harness the Iced Rust power.

Developing a desktop application, which at the same time can be served on the web, is a very powerful and useful proposition.

# How is this Paper different?

This is not just a paper on Iced, but also roadmap to learning and understanding important parts of Rust you will need. Examples often use patterns, idioms and idiomatic Rust, without explaing the reasoning or workings behind the code.
Although you can just copy-paste, you won't really understand what is happening. When you 'learn' to use Iced this way, you are not really learning, but assembling mindlessly code-bits.
The aim of this paper is to really give you an understanding of Iced and its inner workings.

# How to learn?

The best way our brain can learn is by building something you need. During this paper I will share the process of building a Nutricional Macro Calculator.
I needed this myself, since I did a keto-carnivore diet. You don't have to copy my application. You should instead use use the knowledge and building blocks to build something you want or need.

I will start with an overview, and then start the process.

You need an understanding of Rust. Although this Paper will introduce you to some parts of Rust, this is far from enough to mastering Rust.
The books I used to get an understanding of Rust are:

- The Rust Book
- Rust in Action, Tim McNamarra
- Rust for Rustaceans, Jon Gjengset
- Idiomatic Rust, Tim McNamarra
- Refactoring Rust

By now, there are many interesting and high-quality books. And I strongly advise you to first read, instead of watching video tutorials.

# What is Iced.rs?

## Why choose Iced.rs?

- Easy to learn.
- Fully in Rust.
- If needed, you can write your own components, and expand Iced as much as you want.
- Availability of many very useful components (markdown, text_editor, keyboard listener, ...)
- No limitations on features of apps developed

## Backdraws of Iced? (not really)

**These backdraws are not really backdraws.** These are just temporary backdraws for me, because I am in the 'learning phase' of building ui's with Rust.

Althought there are many official examples and applications. Allf of the current examples are built as one page, even if the applications have multiple screens (modals) or windows.

Coming from a JS, ReactJS, NodeJS toolkit, I am used to separate logic, views and helpers accross multiple files and folders.

This is not an issue code-quality-wise, but applications grow over time, and a best practice for sepating code into more managable blocks would be welcomed.

**TODO: Need to find a 'best practice' to divide the application into logic, views and data. But that is for later.**

## Anatomy of an Iced Application

This is just a birdside view of an iced app. Obviously, for a real application, you will need to import all the needed iced widgets, modules and connect everything together.

This is just to give a first look at Ice.

1. The State of the app will be put into a struct. In the example below this will be the `struct AppName`.
2. All update of the State is handled by the update function `fn update`.
3. The user interface of the app is defined inside the view function `fn view`.
4. There is also a `fn theme` for setting and managing the theme, and a `fn subscribe` for handling subscriptions to for example listen to key-strokes.

```rust
fn main() -> iced::Result {
    iced::application("Name of App", AppName::update, AppName::view)
        .theme(AppName::theme)
        // .theme(|_| Theme::TokyoNight)
        .run();
}

struct AppName {
    email: String,
    // ..
}

enum Message {
    OnChangeEmail(String),
    //
}

impl AppName {
    fn subscribe() -> Task<Message> {
        //
    }

    fn update(&mut self, message: Message) -> Task<Message> {
        match message {
            Message::OnChangeEmail(email) => {
                self.email = email;
                Task::none()
            }
            //
        }
    }

    fn view(&self) -> Element<Message> {
        let email_field = text_input("", &self.email).on_press(Message::OnChangeEmail);
        column![
            //
            email_field,
            //
        ].into()
    }

    fn theme(&self) -> Theme<Messages> {
        Theme::Dark
    }
}
```

## Pattern to build an Iced application

1. Define the state in a struct.
2. Define the messages in an enum.
3. Define an impl to hold `fn new`, `fn subscribe`, `fn update`, `fn view` and `fn theme`.
4. Initialize the initial state of the app in `fn new`
5. Handle all the messages with a match inside the `fn update`.
6. Create the ui inside the the `fn view`.
7. Start the `iced::application` inside the main.

That's all pretty straightforward.

Now, let's now go into detail.

# The State

## What you need to know

- What is a struct
- Clone trait
- Debug trait
- Default trait
- How to derive traits
- How to create a struct
- How to create methods for a struct

Every application you will build has a State, which is defined inside a struct. Usually you name the state with a short name of the application.
When the data in the State is small you can put all the fields into one struct.

```rust
#[derive(Debug, Clone)]
struct AppNname {
    //..
    email: String,
    password: String,
    loginform_opened: bool,
    //..
}
```

## Splitting the State accros multiple structs

But as your application grows and you will have more and more data to handle, you can split the data up.

```rust
#[derive(Debug, Clone)]
struct AppName {
    //..
    user_data: Option<User>,
    macro_list: Vec<MacroFood>,
    user_logged_in: bool,
    login_form_opened: bool,
    //..
}

#[derive(Debug, Clone, Default)]
struct User {
    email: String,
    password: String,
}
//..
```

Once you've defined the State, you have to define the messages that can update the State.

# The Message

## Communication Between the UI Elements and the Application Logic are done through messages

Iced applicationss separate the UI elements, and the Application logic. Messages are the glue between these two parts.

When a user interacts with the application through the user interface's elements, actions will be triggered, which in turn will send messages to the update logic.

Messages are defined in the `enum Message`.

In most cases messages will hold data, and just like with variables in function, you need to annotate all the types of the variables the message can hold.

### Defining the Message struct

```rust
enum Message {
    //..
    OpenForm,
    CloseForm,
    FormClosed(Result<String, Error>),
    EmailOnChange(String),
    PasswordOnChange(String),
    //..
}
```

# The Logic or Message Handling

### Defining the Message Handler

The handler for all the messages, triggered by the ui-elements is the `fn update` function, which is defined inside the `impl AppName` block.

```rust
impl App {
//..
fn update(&mut self, message: Message) -> Task<Message> {
    match message {
        // ...
            Message::OpenForm => {
                self.loginform_opened = true;
                Task::none()
            }
    }
}
}
```

The update method mutates the state, and this in turn will automatically trigger changes in the ui, which is handled by the view method.

### Launching functions inside the Message handlers

### When there are no tasks

The `fn update` returns a `Task<Message>`. When you don't have any additional tasks, you can just return a `Task::none()`;

```rust
Task::none()
```

_Creates a Task that does nothing._

### Launching async functions

#### Task::perform

You can launch one async function, defined outside the impl ApplicationName, by using the Task::perform function.

```rust
pub fn perform<A>(
    future: impl Future<Output = A> + MaybeSend + 'static,
    f: impl Fn(A) -> T + MaybeSend + 'static,
) -> Task<T>
where
    T: MaybeSend + 'static,
    A: MaybeSend + 'static ,
)
```

Creates a new Task that runs the given Future to completion and maps its output with the given closure.

#### Task::batch

```rust
pub fn batch(tasks: impl IntoIterator<Item = Task<T>>) -> Task<T>
where
    T: 'static,

```

Combines the given tasks and produces a single Task that will run all of the in parallel.

## The User Interface

The user interface is defined inside the `fn view` function of the `impl AppName`. When there are actions, like a button-press, or the input changes, you trigger callbacks via messages.

```rust
impl AppName {
    //
    fn view(&self) -> Element<Message> {
        //
    }
}

```

Let's look at some of the components you can use to compose a user interface.

### text

If you want to show text.

```rust
use iced::widget::text;
use iced::color;

fn view(&self) -> Element<Message> {
    text(&self.name); // you can show the contents of a &String variable
    text("Some text").size(20).color(color!(0xffffff))); // you can just type a &str
    //
}
```

### text_input

The text_input can be used to build forms where input fields are needed. The drawback of this widget is that it can only handle Strings.
So if you need numbers or ranges, you need to handle the input yourself.

```rust
use iced::widget::{input_text, text};

impl AppName {
    fn view(&self) -> Element<Message> {
        let email_input = input_text("place holder text", &self.email).on_press(Message::OnChangeEmail).on_submit(Message::OnChangeEmail);
    }
}
```

Note: if you want the Enter to move to the next field, you can create a FocusMessage which handles this with the `on_submit`-method.

### button

```rust
button(text("Click me")).on_press(Message::HandleButton);
```

### stack!

When you need a pop-up or modal to appear on top of your application, for example if you have a form that has to appear you need
to use a stack macro. This will enable you to stack modals on top of each other.

### opaque

### mouse_over

## Themes

Iced has built-in themes, which you can set either hard-coded in your application, or let the user select one.

If you want to set the theme add a theme method where you either set the theme hard-coded or add
a choice of theming via messages. You also need to add the `.theme(Editor::theme)` to the
`iced::application`.

```rust
use iced::Theme;

impl AppName {
    //...
    fn theme(&self) -> Theme {
        Theme::Dark
    }
}
fn main() -> iced::Result {
    iced::application("Title of app", AppName::update, AppName::view)
        .theme(AppName::theme)
        .run()
}
```

You can also just define a hard-coded theme inside the `.theme(|_| Theme::Dark)`, if you don't need the user to change the themes.

# Multiple Screen Apps

Most, if not all, applications have multiple screens. Inside Iced, a screen, which stacks on top of the main application is called a modal. Check the [modal example](https://github.com/iced-rs/iced/tree/master/examples/modal), which showcases how to set this up.

## When to use?

- Sign-up and registration forms
- Forms for adding, and updating content.
- More information content.

## Pattern

To achieve this you will need to use the `iced::widget::stack`, which allows to stack childeren (modals) on top of the parent (main content).

```rust
pub struct Stack<'a, Message, Theme = Theme, Renderer<Renderer, Renderer>> {
    // * private fields
}
```

1. Add a `is_modal_open:bool` variable in the State.
2. In the initialization `fn new` set the `is_modal_open : false` so it's not shown.
3. Create a message `OpenModal` in the Message enum.
4. Add logic to the `fn update` to handle the message `OpenModal`.
5. Repeat steps 3 and 4 for `CloseModal` so you can close the modal when needed.
6. Inside the `fn view` check if the `is_modal_open == true` to show the modal.
7. Use the `stack!` macro to stack the modal on top of the main content. In the stack you first have to place to main content, and then the modal you want to show.

## Need to master before

Before we dive into the code, you need to go a little deeper under the hood and understand some more Rust. If you know all this, feel free to skip.

- The Into Trait
- Lifetimes
- The Clone Trait
- The Element Type
- impl Trait inside variables of functions

  A good starting point is the [Generic Types, Traits, and Lifetimes of the Rust book](https://doc.rust-lang.org/book/ch10-00-generics.html).

### The Into Trait

As with all things you first come accross, the first thing you should do is to go to the [Rust documentation](https://doc.rust-lang.org/std/convert/trait.Into.html), and just read.

```rust
pub trait Into<T>: Sized {
    //
    fn into(self) -> T;
}
```

By simply looking at the code, you see that the Into trait, has a function `into`, that consumes the input value `self`, and returns the value as type `T`.
Into is the opposite of the From trait.

In the case of Into, if you need to implement Into for your type, you should instead implement the From trait, because that automatically implements the Into trait.

Now you should understand that `Into<T>` means that it should be convertable into type T. That means that any variable you provide has to comply to that contract, and has to be convertable to type T.

```rust
struct Wrapper<T>(Vec<T>);

impl<T> From<Wrapper<T>> for Vec<T> {
    fn from(w: Wrapper<T>) -> Vec<T> {
        w.0
    }
}
```

### The Clone Trait

### The Element (Iced)

### impl trait

The impl trait syntax is syntactic sugar for the traditional way to add trait bounds to variables in functions.

```rust
fn handle_modal<'a + Message>(parent_creen: Element<'a + Message>, child_screen: Element<'a + Message>') -> Element<'a + Message>
```

or

```rust
fn handle_modal(parent_screen: impl Into<Element<'a + Message>, child_screen: impl Into<Element<'a + Message>,callback: 'a Message)
where
Message : 'a + Clone,
-> Element<'a + Message>
```

### impl Into<Element<'a + Message>>

And finally `impl Into<Element<'a + Message>` means that the variable has to be able to be converted into an Element<Message> type. In the Iced environment, that
simply means that any variable you provide needs to be an Iced widget, since they all can be converted into Element<Message>.
