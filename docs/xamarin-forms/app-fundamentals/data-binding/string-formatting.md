---
title: "Xamarin.Forms String Formatting"
description: "This article explains how to use Xamarin.FOrms data bindings to format and display objects as strings. This is achieved by setting the StringFormat of the Binding to a standard .NET formatting string with a placeholder."
ms.service: xamarin
ms.assetid: 978C85B7-CB58-4483-A131-21B381A865E0
ms.subservice: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
no-loc: [Xamarin.Forms, Xamarin.Essentials]
---

# Xamarin.Forms String Formatting

Sometimes it's convenient to use data bindings to display the string representation of an object or value. For example, you might want to use a `Label` to display the current value of a `Slider`. In this data binding, the `Slider` is the source, and the target is the `Text` property of the `Label`.

When displaying strings in code, the most powerful tool is the static [`String.Format`](xref:System.String.Format(System.String,System.Object)) method. The formatting string includes formatting codes specific to various types of objects, and you can include other text along with the values being formatted. See the [Formatting Types in .NET](/dotnet/standard/base-types/formatting-types/) article for more information on string formatting.

## The StringFormat Property

This facility is carried over into data bindings: You set the [`StringFormat`](xref:Xamarin.Forms.BindingBase.StringFormat) property of `Binding` (or the [`StringFormat`](xref:Xamarin.Forms.Xaml.BindingExtension.StringFormat) property of the `Binding` markup extension) to a standard .NET formatting string with one placeholder:

```xaml
<Slider x:Name="slider" />
<Label Text="{Binding Source={x:Reference slider},
                      Path=Value,
                      StringFormat='The slider value is {0:F2}'}" />
```

Notice that the formatting string is delimited by single-quote (apostrophe) characters to help the XAML parser avoid treating the curly braces as another XAML markup extension. Otherwise, that string without the single-quote character is the same string you'd use to display a floating-point value in a call to `String.Format`. A formatting specification of `F2` causes the value to be displayed with two decimal places.

The `StringFormat` property only makes sense when the target property is of type `string`, and the binding mode is `OneWay` or `TwoWay`. For two-way bindings, the `StringFormat` is only applicable for values passing from the source to the target.

As you'll see in the next article on the [Binding Path](binding-path.md), data bindings can become quite complex and convoluted. When debugging these data bindings, you can add a `Label` into the XAML file with a `StringFormat` to display some intermediate results. Even if you use it only to display an object's type, that can be helpful.

The **String Formatting** page illustrates several uses of the `StringFormat` property:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:sys="clr-namespace:System;assembly=netstandard"
             x:Class="DataBindingDemos.StringFormattingPage"
             Title="String Formatting">

    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Label">
                <Setter Property="HorizontalTextAlignment" Value="Center" />
            </Style>

            <Style TargetType="BoxView">
                <Setter Property="Color" Value="Blue" />
                <Setter Property="HeightRequest" Value="2" />
                <Setter Property="Margin" Value="0, 5" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout Margin="10">
        <Slider x:Name="slider" />
        <Label Text="{Binding Source={x:Reference slider},
                              Path=Value,
                              StringFormat='The slider value is {0:F2}'}" />

        <BoxView />

        <TimePicker x:Name="timePicker" />
        <Label Text="{Binding Source={x:Reference timePicker},
                              Path=Time,
                              StringFormat='The TimeSpan is {0:c}'}" />

        <BoxView />

        <Entry x:Name="entry" />
        <Label Text="{Binding Source={x:Reference entry},
                              Path=Text,
                              StringFormat='The Entry text is &quot;{0}&quot;'}" />

        <BoxView />

        <StackLayout BindingContext="{x:Static sys:DateTime.Now}">
            <Label Text="{Binding}" />
            <Label Text="{Binding Path=Ticks,
                                  StringFormat='{0:N0} ticks since 1/1/1'}" />
            <Label Text="{Binding StringFormat='The {{0:MMMM}} specifier produces {0:MMMM}'}" />
            <Label Text="{Binding StringFormat='The long date is {0:D}'}" />
        </StackLayout>

        <BoxView />

        <StackLayout BindingContext="{x:Static sys:Math.PI}">
            <Label Text="{Binding}" />
            <Label Text="{Binding StringFormat='PI to 4 decimal points = {0:F4}'}" />
            <Label Text="{Binding StringFormat='PI in scientific notation = {0:E7}'}" />
        </StackLayout>
    </StackLayout>
</ContentPage>
```

The bindings on the `Slider` and `TimePicker` show the use of format specifications particular to `double` and `TimeSpan` data types. The `StringFormat` that displays the text from the `Entry` view demonstrates how to specify double quotation marks in the formatting string with the use of the `&quot;` HTML entity.

The next section in the XAML file is a `StackLayout` with a `BindingContext` set to an `x:Static` markup extension that references the static `DateTime.Now` property. The first binding has no properties:

```xaml
<Label Text="{Binding}" />
```

This simply displays the `DateTime` value of the `BindingContext` with default formatting. The second binding displays the `Ticks` property of `DateTime`, while the other two bindings display the `DateTime` itself with specific formatting. Notice this `StringFormat`:

```xaml
<Label Text="{Binding StringFormat='The {{0:MMMM}} specifier produces {0:MMMM}'}" />
```

If you need to display left or right curly braces in your formatting string, simply use a pair of them.

The last section sets the `BindingContext` to the value of `Math.PI` and displays it with default formatting and two different types of numeric formatting.

Here's the program running:

[![String Formatting](string-formatting-images/stringformatting-small.png "String Formatting")](string-formatting-images/stringformatting-large.png#lightbox "String Formatting")

## ViewModels and String Formatting

When you're using `Label` and `StringFormat` to display the value of a view that is also the target of a ViewModel, you can either define the binding from the view to the `Label` or from the ViewModel to the `Label`. In general, the second approach is best because it verifies that the bindings between the View and ViewModel are working.

This approach is shown in the **Better Color Selector** sample, which uses the same ViewModel as the **Simple Color Selector** program shown in the [**Binding Mode**](binding-mode.md) article:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.BetterColorSelectorPage"
             Title="Better Color Selector">

    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Slider">
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            </Style>

            <Style TargetType="Label">
                <Setter Property="HorizontalTextAlignment" Value="Center" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout>
        <StackLayout.BindingContext>
            <local:HslColorViewModel Color="Sienna" />
        </StackLayout.BindingContext>

        <BoxView Color="{Binding Color}"
                 VerticalOptions="FillAndExpand" />

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

There are now three pairs of `Slider` and `Label` elements that are bound to the same source property in the `HslColorViewModel` object. The only difference is that `Label` has a `StringFormat` property to display each `Slider` value.

[![Better Color Selector](string-formatting-images/bettercolorselector-small.png "Better Color Selector")](string-formatting-images/bettercolorselector-large.png#lightbox "Better Color Selector")

You might be wondering how you could display RGB (red, green, blue) values in traditional two-digit hexadecimal format. Those integer values aren't directly available from the `Color` structure. One solution would be to calculate integer values of the color components within the ViewModel and expose them as properties. You could then format them using the `X2` formatting specification.

Another approach is more general: You can write a *binding value converter* as discussed in the later article, [**Binding Value Converters**](converters.md).

The next article, however, explores the [**Binding Path**](binding-path.md) in more detail, and show how you can use it to reference sub-properties and items in collections.

## Related Links

- [Data binding chapter from Xamarin.Forms book](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
