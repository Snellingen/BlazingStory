﻿# ![](https://raw.githubusercontent.com/jsakamoto/BlazingStory/main/assets/icon.min.64x64.svg) Blazing Story [![NuGet Package](https://img.shields.io/nuget/v/BlazingStory.svg)](https://www.nuget.org/packages/BlazingStory/)

## Summary

The clone of ["Storybook"](https://storybook.js.org/) for Blazor, a frontend workshop for building UI components and pages in isolation.

![](https://raw.githubusercontent.com/jsakamoto/BlazingStory/main/assets/social-preview.png)

The "Blazing Story" is built on **almost 100% Blazor native** (except only a few JavaScript helper codes), so you don't have to care about `npm`, `package.json`, `webpack`, and any JavaScript/TypeScript code. You can create a UI catalog application **on the Blazor way!**

## Getting Started

### Example scenario

For the example scenario, you already have a Razor Class Library project, "MyRazorClassLib", that includes the "Button" component.

```
📂 (working directory)
    + 📄 MyRazorClassLib.sln
    + 📂 MyRazorClassLib
        + 📄 MyRazorClassLib.csproj
        + ...
        + 📂 Components
            + 📄 Button.razor
            + ...
```

### Preparation

Close all Visual Studio IDE instances (if you use Visual Studio IDE), and install the "Blazing Story" project template with the following command. (This installation instruction is enough to execute once in your development environment.)

```shell
dotnet new install BlazingStory.ProjectTemplates
```

### Creating a Blazing Story app and stories

#### Step 1 - Create a new Blazing Story app project

Open the solution file (.sln) with Visual Studio, and add a new "Blazing Story" project from the project templates. (In this example scenario, we named it "MyRazorClassLib.Stories")

![](https://raw.githubusercontent.com/jsakamoto/BlazingStory/main/assets/readme-images/add-a-new-project.png)

If you are working on dotnet CLI, you can do that with the following commands in a terminal.

```shell
# Create a new Blazing Story app
dotnet new blazingstory -n MyRazorClassLib.Stories
# Add the Blazing Story app project to the solution
dotnet sln add ./MyRazorClassLib.Stories/
```

The file layout will be the following tree.

```
📂 (working directory)
    + 📄 MyRazorClassLib.sln
    + 📂 MyRazorClassLib
        + ...
    + 📂 MyRazorClassLib.Stories
        + 📄 MyRazorClassLib.Stories.csproj✨ 👈 Add this
```

#### Step 2 - Add a project reference of the class lib to the Blazing Story project

Next, add a project reference in the Blazing Story App project "MyRazorClassLib.Stories" that refers to the Razor Class Library "MyRazorClassLib".

![](https://raw.githubusercontent.com/jsakamoto/BlazingStory/main/assets/readme-images/add-a-project-reference.png)

If you are working on dotnet CLI, you can do that with the following commands in a terminal.

```shell
dotnet add ./MyRazorClassLib.Stories reference ./MyRazorClassLib
```

```
📂 (working directory)
    + 📄 MyRazorClassLib.sln
    + 📂 MyRazorClassLib <--- refers --+
        + ...                          |
    + 📂 MyRazorClassLib.Stories ------+
        + ...
```

#### Step 3 - Add a "story" file

Add a new "story" file to the Blazing Story App project "MyRazorClassLib.Stories".

A "story" file is a normal Razor Component file (.razor), but it is annotated with the `[Stories]` attribute and includes a markup of the `<Stories>` component. There is no restriction on file layout and naming of "story" files, but usually, we place it in the "Stories" subfolder and add the ".story" suffix to its file name.

In this example scenario, we are going to write a "story" for the `Button` component lived in the "MyRazorClassLib" project, so we would add a new story file named "Button.story.razor" in the "Stories" subfolder where is under the "MyRazorClassLib.Stories" project.

```
📂 (working directory)
    + 📄 MyRazorClassLib.sln
    + 📂 MyRazorClassLib
        + ...
    + 📂 MyRazorClassLib.Stories
        + 📄 MyRazorClassLib.Stories.csproj
        + 📂 Stories
            + 📄 Button.story.razor✨ 👈 Add this
```

### Step 4 - Implement the "story"

Implement a story.

The "Button.story.razor" would be like the below.

```html
@using MyRazorClassLib.Components
@attribute [Stories("Components/Button")]

<Stories TComponent="Button">

    <Story Name="Primary">
        <Template>
            <Button Label="Button" Primary="true" @attributes="context.Args" />
        </Template>
    </Story>

</Stories>
```

### Step 5 - Run it!

If you are working on Visual Studio, right-click the "MyRazorClassLib.Stories" project in the solution explorer to show the context menu, click the "Set as Startup Project" menu item, and hit the `Ctrl` + `F5` key.

If you are working on dotnet CLI, you can do that with the following commands in a terminal.

```shell
dotnet run --project ./MyRazorClassLib.Stories
```

Then you will see the clone of the "Storybook" built on Blazor! 🎉

![](https://raw.githubusercontent.com/jsakamoto/BlazingStory/main/assets/readme-images/first-run-of-blazingstory.gif)

## Structure of "story"

- The `[Stories]` attribute on a Razor component indicates to the Blazing Story runtime that the Razor component will include stories.
- The slash-separated title string of the parameter of the `[Stories]` attribute configures the navigation tree and represents the title of the stories container (we called it "Component").
- The `<Stories>` component indicates to the Blazing Story runtime what is the target component of the story with its `TComponent` type parameter. The "Controls" panel will be built from this component-type information.
- The `<Stories>` component can include one or more `<Story>` components. The `<Story>` component has the `Name` parameter, which will be shown in the sidebar navigation tree to identify each story.
- The `<Template>` render fragment parameter inside the `<Story>` component will be rendered in the canvas area when that story is selected in the sidebar navigation tree.
- The Blazing Story runtime passes the parameters users inputted from the "Control" panel through the `context.Args` inside the `<Template>` render fragment, so you should apply it to the component by using the `@attributes=...`,  [attribute splatting syntax](https://learn.microsoft.com/aspnet/core/blazor/components/#attribute-splatting-and-arbitrary-parameters).

![](https://raw.githubusercontent.com/jsakamoto/BlazingStory/main/assets/readme-images/structure-of-story.png)

## Configure arguments 

### Configure the control type

You can configure the type of control for the component parameters via the `<ArgType>` component inside the `<Stories>` component. The `For` lambda expression parameter of the `<ArgType>` component indicates which parameters to be applied the control type configuration. And the `Control` parameter of the `<ArgType>` component is used for specifying which type of control uses in the "Control" panel to input parameter value.

For example, if you apply the `ControlType.Color` control type to the `BackgroundColor` parameter like this, 

```html
<Stories TComponent="Button">
    <ArgType For="_ => _!.BackgroundColor" Control="ControlType.Color" />
    ...
```

you will be able to specify the component's background color with the color picker, like in the following picture.

![](https://raw.githubusercontent.com/jsakamoto/BlazingStory/main/assets/readme-images/control-type-colorpicker.png)

For another example, you can make the "Control" panel use dropdown list control to choose one of the values of the `enum` type instead of the radio button group control, with the following code.

```html
<Stories TComponent="Button">
    ....
    <ArgType For="_ => _!.Size" Control="ControlType.Select" />
    ...
```

Then, you will get the result in the following picture.

![](https://raw.githubusercontent.com/jsakamoto/BlazingStory/main/assets/readme-images/control-type-dropdownlist.png)

### Configure the initial value of the component parameter

You can specify the initial value of the component parameter by using the `<Arg>` component inside the `<Arguments>` render fragment parameter of the `<Story>` component like this:

```html
    ...
    <Story Name="Secondary">
        <Arguments>
            <Arg For="_ => _!.Primary" Value="false" />
        </Arguments>
        ...
```

![](https://raw.githubusercontent.com/jsakamoto/BlazingStory/main/assets/readme-images/configure-arguments-arg.png)

## System Requirements

.NET SDK ver.7 or later

## Disclaimer

The "Blazing Story" is just my hobby work and a personal technical exhibition, so it is not an officially derived product from the "Storybook". I'd like to keep improving this product for now, but it might be abandoned if I cannot spend enough time on this product in the future. I welcome that somebody forks this product and organizes the maintainers for it.

From another perspective, the "Blazing Story" is an almost 100% Blazor-native application that re-implemented  Storybook's look, behavior, and functionality to mimic it. However, this means that **none of the Storybook community's contributions, numerous add-ins, and related services are available in the "Blazing Story"**.

Therefore, the combination of Blazor's WebComponents custom element and Storybook might be more promising for the future of UI component catalog solutions for Blazor application development than the "Blazing Story".

However, on the "Blazing Story" side, Blazor application developers can get a Storybook-like UI component catalog solution under the familiar .NET ecosystem without being annoyed by a complex JavaScript front-end development toolchain. This is the most important point I wanted to illustrate through the development of Blazing Story.


## Frequently Asked Questions

**Q1:** Hot reloading and `dotnet watch` doesn't work.  
**A1:** Sorry for that. I want to manage to be available for hot reloading eventually.

**Q2:** Can I add a project reference of a Blazor application project, not a Razor Class Library project, to a Blazing Story app project?  
**A2:** Currently, you can't. I'm considering making it be able to do it in the future.

**Q3:** Where are "Docs" nodes?  
**A3:** The "Docs" feature is not implemented yet. I'll implement it in the future version.

**Q4:** How can I write or configure addons?  
**A4:** You can't do that for now because the addon architecture is not completed yet. I'll finish it in the future version.


## License

[Mozilla Public License Version 2.0](https://raw.githubusercontent.com/jsakamoto/BlazingStory/main/LICENSE)

The third party notice is [here](https://raw.githubusercontent.com/jsakamoto/BlazingStory/main/THIRD-PARTY-NOTICES.txt).
