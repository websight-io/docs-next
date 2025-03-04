# Components

## Overview

This document describes the components concept. It also shows how to use components in the WebSight CMS authoring interface.

In this documentation we use the follow terms:

- WebSight CMS pages editor: Refers to the UI for authoring page content
- WebSight Resource Browse: Refers to the administration tool that allows users to see the resources (data and application) that are available on the WebSight CMS instance

Both views can be opened from the WebSight UI:

![WebSight - page editing & opening Resource Browser](websight-page-edit-resource-browser.png)

Check the WebSight [project setup documentation](/docs/developers/setup/) to see more details about:

- what WebSight CMS is, and what it gives you
- implementing applications for WebSight CMS Community Edition
- the role of OSGi, OSGi bundles, Sling Resources, JCR and more in WebSight CMS

## What is component

Components are:

- elements that render parts of content according to implemented functionality
- used to assemble content (like pages)
- available in the WebSight CMS authoring interface
- reusable
- configurable by editing properties on created components instances
- implemented by developers
- shipped in OSGi bundles that are deployed to a WebSight CMS instance

Components can:

- include (embed) other components
- extend other components
- deliver initial content
- define dialogs, which are used to edit properties on component instances
- be containers (allowing you to add child components during authoring)
- define allowed child components (in the case of containers)

## Using components

To provide more context on components, let's look at the authoring interface and content data structure.

The WebSight CMS page editor lists all available components that can be used to build pages on a given WebSight instance. Available components depend on the OSGi bundles that delivering components installed on the instance. Those components can be added to the page by dragging them to the page content view at the center of the screen.

<p align="center" width="100%">
    <img src="./page-editor-components.png" alt="Components definitions listed in the WebSight CMS page editor">
    Components definitions listed in the WebSight CMS page editor.
</p>

You can check the page content structure in the page editor content tree tab. This view presents the components tree under page content.
This example page contains just the _Page Section_ component, which is the only component present currently in the page content of this example page.

<p align="center" width="100%">
    <img style="width:350px" src="./page-editor-content-tree.png" alt="Page content tree in the editor"> <br>
    Page content tree in the editor.
</p>

The _Page Section_ component is a container. It allows you to add other components via the authoring interface. The page editor content section displays an empty _Page Section_ component as a box containing the placeholder text "Drag components here."

After dragging and dropping the _Title_ and _Rich Text_ components, the page content tree is updated with new nodes. The nodes visible in the content tree are the instances of components.

_Title_ and _Rich Text_ components are not containers, so other components cannot be added as their child components. While dragging other components from the components list, the new items can be added next to those components as other children on the _Page Section_ component.


![RTE component](RTE-component.png)

After adding a component to the page, the edit action can be used to edit the selected component instance. The dialog that is displayed is a part of the component definition. It is defined by the developer implementing a given component by setting the proper dialog fields (see more in the [Dialogs](../dialogs/) documentation). Properties set via the dialog are saved at edited component instances, and are used by the component to render the appropriate part of content based on the implemented functionality. For example, the _Title_ component renders the `Heading text` field value in the HTML `<h>` tag and allows you to set `<h>` tag level and styling.

<p align="center" width="100%">
    <img src="./title-component-dialog.png" alt="Title component - dialog">
    Components are reusable. Another <em>Title</em> component can be added to the page and configured with its own data.
</p>

After the second title, the _Cards List_ component is added now. This component is a container but implemented to work with specific type of child components: Only _Card Item_ components can be added inside (this behavior is part of the _Cards List_ component definition).

The _Cards List_ component defines initial content that is added to the page when adding a component. The initial content contains 3 child _Card Item_ components (to save work during authoring and present the component in an initialized state).

In the content tree, you can see that the _Card Item_ components are child components of the _Cards List_ component.

![Card List component](card-list-component-rendition.png)

You can continue with assembling and configuring page content based on the available components.

## Component definition

Components are implemented by developers and delivered as resources following a defined structure. The resources should be provided in the OSGi bundle. See the project setup documentation for more details.

In this section we will see how the component definition resources structure should look.

### Simple component definition example
Let's see what a simple component definition looks like.

We see a component named _Say Hello_. It provides dialog with one text field, which can be used in the authoring interface to set the hello text rendered on the page using the `<p>` HTML tag. If the property is not set, `Hello World!` text will be used. 

The example application delivers the resource `/apps/example-site/components/hello`, which is a component definition. It contains just three properties:

- `sling:resourceType` = `ws:Component` which makes this resource the component definition
- `title` = `Say Hello` which is the title of the component visible in the authoring UI
- `group` = `Example group` which is a group name visible in the authoring UI

<p align="center" width="100%">
    <img src="./example-component-definition.png" alt="Example component definition in the WebSight Resource Browser">
    Example component definition in the WebSight Resource Browser.
</p>

The component is visible in the WebSight CMS page editor. After adding it to the page, it renders ‘Hello World!’ text.

![Example component with default text - rendition](example-component-with-default-text-rendition.png)

Because the Say Hello component was added to the edited page, the corresponding node was created in the page content:

![Sample component content in the WebSight Resource Browser](example-component-used-on-page.png)

The `Hello World!` text is rendered in the view because of the `hello.html` resource defined under the component definition resource that contains the script used to render the content node. The script renders the `helloText` property value -- or `Hello World!`, if the property value is empty (it's empty for now, so we see the fallback text).

<p align="center" width="100%">
    <img src="./example-component-renderer.png" alt="Example component renderer in the WebSight Resource Browser">
    Example component renderer in the WebSight Resource Browser.
</p>

The second resource under the component definition is the `dialog` resource. This is the dialog definition resource. It contains a`sling:resourceType` = `wcm/dialogs/dialog` property, which defines how the dialog should be displayed (see more details in below) and it has sub-node called `helloText` that defines the text field (`sling:resourceType` = `wcm/dialogs/textfield`) input with the label `Hello Text`.

<p align="center" width="100%">
    <img src="./example-component-dialog-definition.png" alt="Example component dialog resources in the WebSight Resource Browser">
    <img src="./example-component-dialog-text-field-definition.png" alt="Example component - text field definition for dialog">
    Example component dialog resources in the WebSight Resource Browser.
</p>

Because the dialog is defined, the edit action in the editor allows you to open the dialog as a set value of the text field.

![Say hello component with Hello text - dialog](example-component-with-hello-text-dialog.png)

The property value is set on the content node of this _Say Hello_ component instance at the `helloText` property (according to the `name` property value at the text field node in the dialog definition):

![Say Hello component with Hello text - resource browser](example-component-with-hello-text-resource-browser.png)

And now the component renderer script will display the property value instead of fallback text:
![Say Hello component with Hello text - rendition](example-component-with-hello-text-rendition.png)

Let's explore all the options and details of the component definition.

### Component definition properties

The component definition is a Sling Resource with resource type `ws:Component`. 
Use the following resource properties to define the component:

<table>
    <tr>
        <th>Component property</th>
        <th>Required</th>
        <th>Value</th>
        <th>Description</th>
   </tr>
   <tr>
        <td>sling:resourceType</td>
        <td>Yes</td>
        <td>ws:Component</td>
        <td>Defines that this resource is a component definition.</td>
    </tr>
    <tr>
        <td>sling:resourceSuperType</td>
        <td>No</td>
        <td>super resource type</td>
        <td>Set this property to make your component extend another component. That way, the extended component elements can be reused in the extending components. <br> <br> Example value: <code>wcm/core/components/page</code></td>
    </tr>
    <tr>
        <td>title</td>
        <td>Yes</td>
        <td>string</td>
        <td>Component name visible in UI. <br> <br> Example value: <code>Title</code></td>
    </tr>
    <tr>
        <td>group</td>
        <td>Yes</td>
        <td>string</td>
        <td>Component group name. Allows us to present related components in the UI. Used while defining allowedComponents. Use the value <code>.hidden</code> to make the component definition not visible in the UI. <br> <br> Example value: <code>General</code> </td>
    </tr>
    <tr>
        <td>isContainer</td>
        <td>No</td>
        <td>boolean, false by default</td>
        <td>Defines the component as a container. Containers are components that contain child components that can be added, removed and reordered using the authoring interface. If true, the <code>allowedComponents</code> property is required too.</td>
    </tr>
    <tr>        
        <td>allowedComponents</td>
        <td>No*</td>
        <td>array of strings. Values are absolute component paths and group names. No default value; by default nothing is allowed.</td>
        <td>
            Array listing allowed child component absolute paths or groups that can be added to the container. 
            <br> <br>
            *Required if <code>isContainer=”true”</code>.
            <br> <br>
            Example value: <code>[ "/apps/my-app/components/component1", "/apps/my-app/components/component2", "Content", "Blog" ]</code> 
        </td>
    </tr>
    <tr>
        <td>isLayout</td>
        <td>No</td>
        <td>boolean, false by default</td>
        <td>Defines the component as a layout component.
            Layout components are displayed in a separate section in the WebSight CMS pages editor from components that are not layout components. <br> <br>

            It’s up to the component developer to set <code>isLayout=true</code>, but the guideline is to use it for components that: <br> 
            - are used to define page layout <br>
            - must contain other components to be usable <br> 
            - contain no logic (other than logic related to layout calculation) <br> 
            - interact only on page resize (no other “user actions” allowed) <br> 
            - have no visible UI elements (like texts)<br> <br>

            Examples of Layouts: <br>
            - 2-column container <br>
            - 1-column container <br>
            - Section with title, image, text and CTA (container including components) <br> <br>

            Example of components that are not Layouts: <br>
            - image, title or CTA, all of which have visible UI elements (like texts) <br>
            - logo cloud, which contain very specific CSS logic and should not be used to create generic page layouts) <br>
            - Accordion, which contains logic not related to layout calculation <br>
            - Image gallery, which contains logic not related to layout calculation
        </td>
   </tr>
   <tr>
        <td>instanceResourceType</td>
        <td>No</td>
        <td>resource type that will be set on created component instance </td>
        <td>If this property is not set, the resource type of every created component instance node will be set to a value resolved from the component definition resource path. If set, then this property value will be used instead. <br> <br>

        This allows multiple components definitions, which will result with the same resource type used for created component instances. It could be used to deliver components (or layout components) with different initial content. <br> <br>

        For example: <br>

        The <em>Section</em> layout component contains an embedded <em>Title</em> component and is a container that renders child components below the <em>Title</em>. If the authoring section with four columns needs to be created, then the <em>Section</em> component needs to be added with four containers inside that are configured to be displayed as columns. <br><br>

        If this page layout is commonly used during content authoring then a new layout component could be implemented to simplify the process.<br><br>

        A <em>Section with four columns</em> layout component could be added. The <code>instanceResourceType</code> property should be set to the <em>Section</em> component resource type and initial content should provide the same structure as was created manually before (a section plus four configured child containers). This way, after dropping <em>Section with four columns</em> on the page, the commonly used content structure will be ready to use immediately.<br><br>

        Example value: <code>my-app/components/section</code>

   </td>
</table>

### Dialog

Dialog is the form with inputs that allows you to set data on component instances in content.
The form layout, fields and behavior are described by resources starting at a resource named `dialog` defined under the component definition resource. If the `dialog` resource is not defined, then the edit component properties action is not available in the editor.

This is what the dialog definition structure looks like with the same dialog displayed in the editor:

<p align="center" width="100%">
    <img src="./title-component-dialog-definition.png" alt="Title component - dialog definition">
    Component dialog resources structure in Resource Browser tool.
</p>

<p align="center" width="100%">
    <img src="./title-component-dialog.png" alt="Component dialog displayed after edit component action in pages editor">
    Component dialog displayed after edit component action in pages editor.
</p>

Dialog for editing component properties is opened in `edit:dialog` _context_ - the _context_
can be used to disable some fields depending on the dialog usage. Read more about _context_ and other
aspects of the dialogs in the [Dialogs documentation](/docs/developers/dialogs/).

### Rendering script

To render components, the rendering script file resource is needed. The file resource name should be the same as the component name with the `.html` extension (the recommended and supported scripting language in the WebSight CMS is the HTML Templating Language, or HTL). See the HTL specification [here](https://github.com/adobe/htl-spec/blob/master/SPECIFICATION.md)

If Java support is needed in the HTL, the recommended solution is to use the Sling Models and the HTL Use-API. See details:

- [_use_ block statement](https://github.com/adobe/htl-spec/blob/master/SPECIFICATION.md#221-use)
- [Use-API](https://github.com/adobe/htl-spec/blob/master/SPECIFICATION.md#4-use-api)
- [Sling Models](https://sling.apache.org/documentation/bundles/models.html)

<p align="center" width="100%">
    <img src="./example-component-renderer-script.png" alt="Component renderer script in the WebSight Resource Browser">
    Component renderer script in the WebSight Resource Browser.
</p>

### Template

Component template is a resource that gets copied when creating a new component instance and adding it to a page. In other words, it is the initial content of the component. It is used to set default values or prepare necessary content structure. It could also be used to save work during content authoring.

The component template resource is a component definition sub-node with the name `template`.
The template resource is optional. If it's not present, a new empty node will be created.


<p align="center" width="100%">
    <img src="./component-template-resources-structure.png" alt="Component template resources structure">
    Component template resources structure (view from the WebSight Resource Browser tool).
</p>

<p align="center" width="100%">
    <img src="./component-from-template-on-page.png" alt="Component instance added to the page content initialized from template">
    Component instance added to the page content initialized from template <BR> (view from the WebSight Resource Browser tool).
</p>

### Edit configuration

A component may provide an optional resource named `editConfig` providing configuration customizing component behavior in the [Page editor](../page-editor/).

#### Reload on update

Edit config property named `reloadOnUpdate` defines what should be refreshed on the component update, for example after editing. Options:

- `self` - default behaviour - only updated component is refreshed
- `parent` - parent component is refreshed
- `page` - edited page is refreshed
- `browser` - reload of the Editor in the browser

```json title="Example 'editConfig' with 'reloadOnUpdate' property"
{
  "reloadOnUpdate": "page"
}
```

#### Hide actions

Edit config property named `hideActions` can provide an array of _Page editor_ actions names
(see list of the component actions with names in the [Page editor](../page-editor/#component-actions))
that should not be available for the component.

```json title="Example 'editConfig' with 'hideActions' property"
{
  "hideActions": [
    "edit-properties", "delete"
  ]
}
```

#### Default action

Edit config property named `defaultAction` can provide a name of a _Page editor_ action
(see list of the component actions with names in the [Page editor](../page-editor/#component-actions))
that should be executed if the author *double-clicks* on the component.

If not provided, the `inline-editing` action (see [Inline editing](../inline-editing)) is executed
if the component is configured to support it, otherwise the `edit-properties` action is executed
if the component has a dialog configuration (see [Dialogs](../dialogs)).

```json title="Example 'editConfig' with 'defaultAction' property"
{
  "defaultAction": "delete"
}
```

