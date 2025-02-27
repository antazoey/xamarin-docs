---
title: "Xamarin.Forms Compiled Bindings"
description: "This article explains how to use compiled bindings to improve data binding performance in Xamarin.Forms applications."
ms.service: xamarin
ms.assetid: ABE6B7F7-875E-4402-A1D2-845CE374402B
ms.subservice: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/18/2019
no-loc: [Xamarin.Forms, Xamarin.Essentials]
---

# Xamarin.Forms Compiled Bindings

_Compiled bindings are resolved more quickly than classic bindings, therefore improving data binding performance in Xamarin.Forms applications._

Data bindings have two main problems:

1. There's no compile-time validation of binding expressions. Instead, bindings are resolved at runtime. Therefore, any invalid bindings aren't detected until runtime when the application doesn't behave as expected or error messages appear.
1. They aren't cost efficient. Bindings are resolved at runtime using general-purpose object inspection (reflection), and the overhead of doing this varies from platform to platform.

Compiled bindings improve data binding performance in Xamarin.Forms applications by resolving binding expressions at compile-time rather than runtime. In addition, this compile-time validation of binding expressions enables a better developer troubleshooting experience because invalid bindings are reported as build errors.

The process for using compiled bindings is to:

1. Enable XAML compilation. For more information about XAML compilation, see [XAML Compilation](~/xamarin-forms/xaml/xamlc.md).
1. Set an `x:DataType` attribute on a [`VisualElement`](xref:Xamarin.Forms.VisualElement) to the type of the object that the `VisualElement` and its children will bind to.

> [!NOTE]
> It's recommended to set the `x:DataType` attribute at the same level in the view hierarchy as the [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) is set. However, this attribute can be re-defined at any location in a view hierarchy.

To use compiled bindings, the `x:DataType` attribute must be set to a string literal, or a type using the `x:Type` markup extension. At XAML compile time, any invalid binding expressions will be reported as build errors. However, the XAML compiler will only report a build error for the first invalid binding expression that it encounters. Any valid binding expressions that are defined on the `VisualElement` or its children will be compiled, regardless of whether the [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) is set in XAML or code. Compiling a binding expression generates compiled code that will get a value from a property on the *source*, and set it on the property on the *target* that's specified in the markup. In addition, depending on the binding expression, the generated code may observe changes in the value of the *source* property and refresh the *target* property, and may push changes from the *target* back to the *source*.

> [!IMPORTANT]
> Compiled bindings are currently disabled for any binding expressions that define the [`Source`](xref:Xamarin.Forms.Binding.Source) property. This is because the `Source` property is always set using the `x:Reference` markup extension, which can't be resolved at compile time.

## Use compiled bindings

The **Compiled Color Selector** page demonstrates using compiled bindings between Xamarin.Forms views and viewmodel properties:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.CompiledColorSelectorPage"
             Title="Compiled Color Selector">
    ...
    <StackLayout x:DataType="local:HslColorViewModel">
        <StackLayout.BindingContext>
            <local:HslColorViewModel Color="Sienna" />
        </StackLayout.BindingContext>
        <BoxView Color="{Binding Color}"
                 ... />
        <StackLayout Margin="10, 0">
            <Label Text="{Binding Name}" />
            <Slider Value="{Binding Hue}" />
            <Label Text="{Binding Hue, StringFormat='Hue = {0:F2}'}" />
            <Slider Value="{Binding Saturation}" />
            <Label Text="{Binding Saturation, StringFormat='Saturation = {0:F2}'}" />
            <Slider Value="{Binding Luminosity}" />
            <Label Text="{Binding Luminosity, StringFormat='Luminosity = {0:F2}'}" />
        </StackLayout>
    </StackLayout>    
</ContentPage>
```

The root [`StackLayout`](xref:Xamarin.Forms.StackLayout) instantiates the `HslColorViewModel` and initializes the `Color` property within property element tags for the [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) property. This root `StackLayout` also defines the `x:DataType` attribute as the viewmodel type, indicating that any binding expressions in the root `StackLayout` view hierarchy will be compiled. This can be verified by changing any of the binding expressions to bind to a non-existent viewmodel property, which will result in a build error. While this example sets the `x:DataType` attribute to a string literal, it can also be set to a type with the `x:Type` markup extension. For more information about the `x:Type` markup extension, see [x:Type Markup Extension](~/xamarin-forms/xaml/markup-extensions/consuming.md#xtype-markup-extension).

> [!IMPORTANT]
> The `x:DataType` attribute can be re-defined at any point in a view hierarchy.

The [`BoxView`](xref:Xamarin.Forms.BoxView), [`Label`](xref:Xamarin.Forms.Label) elements, and [`Slider`](xref:Xamarin.Forms.Slider) views inherit the binding context from the [`StackLayout`](xref:Xamarin.Forms.StackLayout). These views are all binding targets that reference source properties in the viewmodel. For the [`BoxView.Color`](xref:Xamarin.Forms.BoxView.Color) property, and the [`Label.Text`](xref:Xamarin.Forms.Label.Text) property, the data bindings are `OneWay` – the properties in the view are set from the properties in the viewmodel. However, the [`Slider.Value`](xref:Xamarin.Forms.Slider.Value) property uses a `TwoWay` binding. This allows each `Slider` to be set from the viewmodel, and also for the viewmodel to be set from each `Slider`.

When the application is first run, the [`BoxView`](xref:Xamarin.Forms.BoxView), [`Label`](xref:Xamarin.Forms.Label) elements, and [`Slider`](xref:Xamarin.Forms.Slider) elements are all set from the viewmodel based on the initial `Color` property set when the viewmodel was instantiated. This is shown in the following screenshots:

[![Compiled Color Selector](compiled-bindings-images/compiledcolorselector-small.png "Compiled Color Selector")](compiled-bindings-images/compiledcolorselector-large.png#lightbox "Compiled Color Selector")

As the sliders are manipulated, the [`BoxView`](xref:Xamarin.Forms.BoxView) and [`Label`](xref:Xamarin.Forms.Label) elements are updated accordingly.

For more information about this color selector, see [ViewModels and Property-Change Notifications](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md#viewmodels-and-property-change-notifications).

## Use compiled bindings in a DataTemplate

Bindings in a [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) are interpreted in the context of the object being templated. Therefore, when using compiled bindings in a `DataTemplate`, the `DataTemplate` needs to declare the type of its data object using the `x:DataType` attribute.

The **Compiled Color List** page demonstrates using compiled bindings in a [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.CompiledColorListPage"
             Title="Compiled Color List">
    <Grid>
        ...
        <ListView x:Name="colorListView"
                  ItemsSource="{x:Static local:NamedColor.All}"
                  ... >
            <ListView.ItemTemplate>
                <DataTemplate x:DataType="local:NamedColor">
                    <ViewCell>
                        <StackLayout Orientation="Horizontal">
                            <BoxView Color="{Binding Color}"
                                     ... />
                            <Label Text="{Binding FriendlyName}"
                                   ... />
                        </StackLayout>
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
        <!-- The BoxView doesn't use compiled bindings -->
        <BoxView Color="{Binding Source={x:Reference colorListView}, Path=SelectedItem.Color}"
                 ... />
    </Grid>
</ContentPage>
```

The [`ListView.ItemsSource`](xref:Xamarin.Forms.ListView) property is set to the static `NamedColor.All` property. The `NamedColor` class uses .NET reflection to enumerate all the static public fields in the [`Color`](xref:Xamarin.Forms.Color) structure, and to store them with their names in a collection that is accessible from the static `All` property. Therefore, the `ListView` is filled with all of the `NamedColor` instances. For each item in the `ListView`, the binding context for the item is set to a `NamedColor` object. The [`BoxView`](xref:Xamarin.Forms.BoxView) and [`Label`](xref:Xamarin.Forms.Label) elements in the [`ViewCell`](xref:Xamarin.Forms.ViewCell) are bound to `NamedColor` properties.

Note that the [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) defines the `x:DataType` attribute to be the `NamedColor` type, indicating that any binding expressions in the `DataTemplate` view hierarchy will be compiled. This can be verified by changing any of the binding expressions to bind to a non-existent `NamedColor` property, which will result in a build error.  While this example sets the `x:DataType` attribute to a string literal, it can also be set to a type with the `x:Type` markup extension. For more information about the `x:Type` markup extension, see [x:Type Markup Extension](~/xamarin-forms/xaml/markup-extensions/consuming.md#xtype-markup-extension).

When the application is first run, the [`ListView`](xref:Xamarin.Forms.ListView) is populated with `NamedColor` instances. When an item in the `ListView` is selected, the [`BoxView.Color`](xref:Xamarin.Forms.BoxView.Color) property is set to the color of the selected item in the `ListView`:

[![Compiled Color List](compiled-bindings-images/compiledcolorlist-small.png "Compiled Color List]")](compiled-bindings-images/compiledcolorlist-large.png#lightbox "Compiled Color List")

Selecting other items in the [`ListView`](xref:Xamarin.Forms.BoxView) updates the color of the [`BoxView`](xref:Xamarin.Forms.BoxView).

## Combine compiled bindings with classic bindings

Binding expressions are only compiled for the view hierarchy that the `x:DataType` attribute is defined on. Conversely, any views in a hierarchy on which the `x:DataType` attribute is not defined will use classic bindings. It's therefore possible to combine compiled bindings and classic bindings on a page. For example, in the previous section the views within the [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) use compiled bindings, while the [`BoxView`](xref:Xamarin.Forms.BoxView) that's set to the color selected in the [`ListView`](xref:Xamarin.Forms.ListView) does not.

Careful structuring of `x:DataType` attributes can therefore lead to a page using compiled and classic bindings. Alternatively, the `x:DataType` attribute can be re-defined at any point in a view hierarchy to `null` using the `x:Null` markup extension. Doing this indicates that any binding expressions within the view hierarchy will use classic bindings. The *Mixed Bindings* page demonstrates this approach:

```xaml
<StackLayout x:DataType="local:HslColorViewModel">
    <StackLayout.BindingContext>
        <local:HslColorViewModel Color="Sienna" />
    </StackLayout.BindingContext>
    <BoxView Color="{Binding Color}"
             VerticalOptions="FillAndExpand" />
    <StackLayout x:DataType="{x:Null}"
                 Margin="10, 0">
        <Label Text="{Binding Name}" />
        <Slider Value="{Binding Hue}" />
        <Label Text="{Binding Hue, StringFormat='Hue = {0:F2}'}" />
        <Slider Value="{Binding Saturation}" />
        <Label Text="{Binding Saturation, StringFormat='Saturation = {0:F2}'}" />
        <Slider Value="{Binding Luminosity}" />
        <Label Text="{Binding Luminosity, StringFormat='Luminosity = {0:F2}'}" />
    </StackLayout>
</StackLayout>   
```

The root [`StackLayout`](xref:Xamarin.Forms.StackLayout) sets the `x:DataType` attribute to be the `HslColorViewModel` type, indicating that any binding expression in the root `StackLayout` view hierarchy will be compiled. However, the inner `StackLayout` redefines the `x:DataType` attribute to `null` with the `x:Null` markup expression. Therefore, the binding expressions within the inner `StackLayout` use classic bindings. Only the [`BoxView`](xref:Xamarin.Forms.BoxView), within the root `StackLayout` view hierarchy, uses compiled bindings.

For more information about the `x:Null` markup expression, see [x:Null Markup Extension](~/xamarin-forms/xaml/markup-extensions/consuming.md#xnull-markup-extension).

## Performance

Compiled bindings improve data binding performance, with the performance benefit varying. Unit testing reveals that:

- A compiled binding that uses property-change notification (i.e. a `OneWay`, `OneWayToSource`, or `TwoWay` binding) is resolved approximately 8 times quicker than a classic binding.
- A compiled binding that doesn't use property-change notification (i.e. a `OneTime` binding) is resolved approximately 20 times quicker than a classic binding.
- Setting the [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) on a compiled binding that uses property change notification (i.e. a `OneWay`, `OneWayToSource`, or `TwoWay` binding) is approximately 5 times quicker than setting the `BindingContext` on a classic binding.
- Setting the [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) on a compiled binding that doesn't use property change notification (i.e. a `OneTime` binding) is approximately 7 times quicker than setting the `BindingContext` on a classic binding.

These performance differences can be magnified on mobile devices, dependent upon the platform being used, the version of the operating system being used, and the device on which the application is running.
