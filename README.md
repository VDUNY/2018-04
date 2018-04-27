---
theme: league
---

# April 2018

## Developing Cross Platform .NET Applications

- by Joel "Jaykul" Bennett
- long time .NET developer
- 10 year PowerShell MVP
- recently: DevOps Engineer

--

# April 2018

## AKA: Developing with

### .NET Standard

--

# April 2018

## AKA: The troubles I' seen

### Writing a kernel for jupyter

---

## Motivation

Let's talk for a moment about ...

_why_ we want to write cross-platform apps

- Increase target audience
  - Happy users!
- Broaden potential devices
- Lower hosting costs
- Write mobile apps
- ...
- Profit!?

--

### More desktops

Moving from Windows to Linux and OS X is not very exciting, and not that many people actually do it, but many people want to _be able to_ do it.

### More servers

Again, this is about Linux. The reality is that this can lower costs, depending on how you're doing it. Is anyone paying licenses for Linux containers?

--

### Mobile apps

See Also: Xamarin. This may be _the_ core profit driver these days, as less and less people are _buying_ desktop software (other than games), and huge volume on small mobile apps is driving a lot of money.

### IoT and Devices

Remember my [May presentation](https://github.com/VDUNY/2017-05/) from last year? Some devices run Windows IoT, but the same tools and skills you need apply more broadly: .NET runs on a lot of hardware where Windows doesn't.

--

## Let's talk DevOps for a sec

- Agile development isn't enough
- High quality requires feedback
- Continuous Delivery?
- Measureable metrics:
  - Deploy frequency, Change volume, Lead time, Failed deployment rate, Mean time to recover, Customer ticket volume, Change in user volume, Availability, Load / Performance

--

## All of this because

Working in devops made me appreciate new things in software development:

- Choice in politics
- Choice for cost
- Console and web apps

Jupyter is all of those things rolled into one

---

## Introducing Jupyter

I wanted a notebook that could record PowerShell commands along with their output, and my musings about them. There are lot's of reasons I need this.

The key is that I want to host PowerShell, and have it run _everywhere_ that Jupyter and PowerShell run.

--

## A Jupyter Kernel

Jupyter's kernels are small services that implement a particular language and communicate via [ZeroMQ](http://zeromq.org/) with a graphical client (Jupyter Notebook or Lab, Spyder IDE, Atom editor, nteract desktop app, etc)

They're expected to implement a REPL with state. They can also do light intellisense, keep a history of execution, have side-channel communication, etc.

--

## The simple protocol

It's basically an async REPL:

- Client: execute_request: guid, some code
- Server: status: busy
- Server: execute_input: guid, some code
- (( actually run the code ))
- Server: execute_reply: guid, it worked!
- Server: output: guid, some output
- Server: status: idle

---

## .NET STANDARD

### It's just a specification

The set of APIs that are required for compliance

Think of it as a spec for the BCL

- .NET Core
- .NET 4.6.2
- Universal Windows Platform (UWP)
- MONO
- Xamarin (iOS, Mac, Android)

<span class="fragment">But it's just for libraries</span>

--

## Why you waited so long

Remember the [table](https://docs.microsoft.com/en-us/dotnet/standard/net-standard)

- .xproj -> Project.json -> .csproj
- Language Support (VB.NET, C# 7.1)
- Surface Area (20,000 [more APIs](https://github.com/dotnet/standard/tree/master/docs/versions))
- Use Full Framework Libraries

<span class="fragment">See also [what's new](https://docs.microsoft.com/en-us/dotnet/core/whats-new/)</span>

Notes:

The total number of APIs on .NET Core 2.0 is more than double that of .NET Core 1.1

Live Unit Testing works with .NET Core in Visual Studio now

Side-by-side versions of .NET Core SDKs works in Visual Studio now

--

## .NET CORE

How will you ship?

- Framework hosting (Self Contained)
- Framework dependent

I chose wrong!

NOTE: What you can choose depends on your targets and your customers, but it's important to realize you can easily change your mind later.
 In my case, I did it "the old way" but later found out I needed to follow the example of the PowerShell Core project and ship self-contained.

---

## New project

The new new: `dotnet new`

- You can use File -> New Project

I won't get distracted and talk about templates here, but the `dotnet new` templating system is very simple and easy to use -- you should make your own project templates if you find yourself creating many projects.

--

## New project file

- Very simple
- Obsoletes AssemblyInfo.cs
- You can still target .Net 2
- Uses _PackageReference_
- (Show [one](https://github.com/Jaykul/Jupyter-PowerShell/blob/master/Source/Jupyter/Jupyter.csproj))

--

## Non-obvious features

- You can add whatever you used to add
- Includes support wildcard globbing
  - Recursive patterns like `**\*.cs`
- Common-sense defaults
- Multiple TargetFrameworks
- RuntimeFrameworkVersion

--

## The Wonderful Stuff

- Building Cross-Platform<br/>
  `dotnet build` (msbuild)
- Developing Cross-Platform<br/>
  - [Visual Studio](https://www.visualstudio.com/vs/features/debugging-and-diagnostics/) [Code](https://code.visualstudio.com), [Mac](https://visualstudio.com/vs/mac/)
- Debugging into containers
  - And emulators
  - Even remote hardware
- Visual Studio still Rocks
    - Live Unit Testing
    - Live Share
    - Code Lens
    - Resharper

--

## Just Write Your App

Basically, this is true.

As long as you don't run into problems.

---

## A few problems

- Naming & Documentation
- Missing & Moved Pieces
- Library Support
- _Platform Specific Things_

--

## Documentation

### .NET Core | Standard

- Breeds related confusion
- Used interchangably
- Un-dated articles (just run away)

--

### Missing and Moving Pieces

- There's still a lot missing
  - System.Drawing, DataTable, DataSet
  - Web API
  - Resource Strings
- FileStream in System.IO.FileSystem
- StreamReader ctor (requires FileStream)

Notes:

A lot of this will still show up eventually, but some of it you may have to count on opens source projects to pick up the slack (e.g. it doesn't look like System.Drawing is coming back)

--

### Can we upgrade Asp.NET?

- You can never just upgrade ASP.NET
- This version breaks more than usual
- New Config System
- Built in Dependency Injection
- New [middleware](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/middleware/)
  - HttpHandlers & HttpModules are gone
- Newtonsoft camelCase
- OWin, Kestrel
- RIP Web Forms & Web Pages (See Razor)

--

### Porting process

- Deal with [dependencies](https://github.com/jpsingleton/ANCLAFS)
- Retarget to 4.6.2
- Use Assembly Portability analyzer
- Port Tests First!
- Good luck!

---

# Jupyter Kernel

If we have time, let's look at some code and tools

