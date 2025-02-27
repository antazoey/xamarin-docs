---
title: "Choose a Xamarin.Forms Layout"
description: "Xamarin.Forms layout classes allow you to arrange and group UI controls in your application."
ms.service: xamarin
ms.assetid: 05A39752-A174-447E-A30D-3CC9EF98CB96
ms.subservice: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/21/2018
no-loc: [Xamarin.Forms, Xamarin.Essentials]
---

# Choose a Xamarin.Forms Layout

Xamarin.Forms layout classes allow you to arrange and group UI controls in your application. Choosing a layout class requires knowledge of how the layout positions its child elements, and how the layout sizes its child elements. In addition, it may be necessary to nest layouts to create your desired layout.

The following image shows typical layouts that can be achieved with the main Xamarin.Forms layout classes:

[![The main layout classes in Xamarin.Forms](images/layouts.png "Xamarin.Forms layout classes")](images/layouts-large.png#lightbox "Xamarin.Forms layout classes")

## StackLayout

A [`StackLayout`](xref:Xamarin.Forms.StackLayout) organizes elements in a one-dimensional stack, either horizontally or vertically. The [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) property specifies the direction of the elements, and the default orientation is [`Vertical`](xref:Xamarin.Forms.StackOrientation). `StackLayout` is typically used to arrange a subsection of the UI on a page.

The following XAML shows how to create a vertical [`StackLayout`](xref:Xamarin.Forms.StackLayout) containing three [`Label`](xref:Xamarin.Forms.Label) objects:

```xaml
<StackLayout Margin="20,35,20,25">
    <Label Text="The StackLayout has its Margin property set, to control the rendering position of the StackLayout." />
    <Label Text="The Padding property can be set to specify the distance between the StackLayout and its children." />
    <Label Text="The Spacing property can be set to specify the distance between views in the StackLayout." />
</StackLayout>
```

In a [`StackLayout`](xref:Xamarin.Forms.StackLayout), if an element's size is not explicitly set, it expands to fill the available width, or height if the [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) property is set to [`Horizontal`](xref:Xamarin.Forms.StackOrientation).

A [`StackLayout`](xref:Xamarin.Forms.StackLayout) is often used as a parent layout, which contains other child layouts. However, a `StackLayout` should not be used to reproduce a [`Grid`](xref:Xamarin.Forms.Grid) layout by using a combination of `StackLayout` objects. The following code shows an example of this bad practice:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Details.HomePage"
             Padding="0,20,0,0">
    <StackLayout>
        <StackLayout Orientation="Horizontal">
            <Label Text="Name:" />
            <Entry Placeholder="Enter your name" />
        </StackLayout>
        <StackLayout Orientation="Horizontal">
            <Label Text="Age:" />
            <Entry Placeholder="Enter your age" />
        </StackLayout>
        <StackLayout Orientation="Horizontal">
            <Label Text="Occupation:" />
            <Entry Placeholder="Enter your occupation" />
        </StackLayout>
        <StackLayout Orientation="Horizontal">
            <Label Text="Address:" />
            <Entry Placeholder="Enter your address" />
        </StackLayout>
    </StackLayout>
</ContentPage>
```

This is wasteful because unnecessary layout calculations are performed. Instead, the desired layout can be better achieved by using a [`Grid`](xref:Xamarin.Forms.Grid).

> [!TIP]
> When using a [`StackLayout`](xref:Xamarin.Forms.StackLayout), ensure that only one child element is set to [`LayoutOptions.Expands`](xref:Xamarin.Forms.LayoutOptions.Expands). This property ensures that the specified child will occupy the largest space that the `StackLayout` can give to it, and it is wasteful to perform these calculations more than once.

For more information, see [Xamarin.Forms StackLayout](stacklayout.md).

## Grid

A [`Grid`](xref:Xamarin.Forms.Grid) is used for displaying elements in rows and columns, which can have proportional or absolute sizes. A grid's rows and columns are specified with the [`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions) and [`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions) properties.

To position elements in specific [`Grid`](xref:Xamarin.Forms.Grid) cells, use the [`Grid.Column`](xref:Xamarin.Forms.Grid.ColumnProperty) and [`Grid.Row`](xref:Xamarin.Forms.Grid.RowProperty) attached properties. To make elements span across multiple rows and columns, use the [`Grid.RowSpan`](xref:Xamarin.Forms.Grid.RowSpanProperty) and [`Grid.ColumnSpan`](xref:Xamarin.Forms.Grid.ColumnSpanProperty) attached properties.

> [!NOTE]
> A [`Grid`](xref:Xamarin.Forms.Grid) layout should not be confused with tables, and is not intended to present tabular data. Unlike HTML tables, a `Grid` is intended for laying out content. For displaying tabular data, consider using a [ListView](~/xamarin-forms/user-interface/listview/index.md), [CollectionView](~/xamarin-forms/user-interface/collectionview/index.md), or [TableView](~/xamarin-forms/user-interface/tableview.md).

The following XAML shows how to create a [`Grid`](xref:Xamarin.Forms.Grid) with two rows and two columns:

```xaml
<Grid>
    <Grid.RowDefinitions>
        <RowDefinition Height="50" />
        <RowDefinition Height="50" />
    </Grid.RowDefinitions>
    <Grid.ColumnDefinitions>
        <ColumnDefinition Width="Auto" />
        <ColumnDefinition />
    </Grid.ColumnDefinitions>    
    <Label Text="Column 0, Row 0"
           WidthRequest="200" />
    <Label Grid.Column="1"
           Text="Column 1, Row 0" />
    <Label Grid.Row="1"
           Text="Column 0, Row 1" />
    <Label Grid.Column="1"
           Grid.Row="1"
           Text="Column 1, Row 1" />
</Grid>
```

In this example, sizing works as follows:

- Each row has an explicit height of 50 device-independent units.
- The width of the first column is set to [`Auto`](xref:Xamarin.Forms.GridLength.Auto), and is therefore as wide as required for its children. In this case, it's 200 device-independent units wide to accommodate the width of the first [`Label`](xref:Xamarin.Forms.Label).

Space can be distributed within a column or row by using auto sizing, which lets columns and rows size to fit their content. This is achieved by setting the height of a [`RowDefinition`](xref:Xamarin.Forms.RowDefinition), or the width of a [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition), to [`Auto`](xref:Xamarin.Forms.GridLength.Auto). Proportional sizing can also be used to distribute available space among the rows and columns of the grid by weighted proportions. This is achieved by setting the height of a `RowDefinition`, or the width of a `ColumnDefinition`, to a value that uses the `*` operator.

> [!CAUTION]
> Try to ensure that as few rows and columns as possible are set to [`Auto`](xref:Xamarin.Forms.GridLength.Auto) size. Each auto-sized row or column will cause the layout engine to perform additional layout calculations. Instead, use fixed size rows and columns if possible. Alternatively, set rows and columns to occupy a proportional amount of space with the [`GridUnitType.Star`](xref:Xamarin.Forms.GridUnitType.Star) enumeration value.

For more information, see [Xamarin.Forms Grid](grid.md).

## FlexLayout

A [`FlexLayout`](xref:Xamarin.Forms.FlexLayout) is similar to a [`StackLayout`](xref:Xamarin.Forms.StackLayout) in that it displays child elements either horizontally or vertically in a stack. However, a `FlexLayout` can also wrap its children if there are too many to fit in a single row or column, and also enables more granular control of the size, orientation, and alignment of its child elements.

The following XAML shows how to create a [`FlexLayout`](xref:Xamarin.Forms.FlexLayout) that displays its views in a single column:

```xaml
<FlexLayout Direction="Column"
            AlignItems="Center"
            JustifyContent="SpaceEvenly">
    <Label Text="FlexLayout in Action" />
    <Button Text="Button" />
    <Label Text="Another Label" />
</FlexLayout>
```

In this example, layout works as follows:

- The [`Direction`](xref:Xamarin.Forms.FlexLayout.Direction) property is set to `Column`, which causes the children of the `FlexLayout` to be arranged in a single column of items.
- The [`AlignItems`](xref:Xamarin.Forms.FlexLayout.AlignItems) property is set to `Center`, which causes each item to be horizontally centered.
- The [`JustifyContent`](xref:Xamarin.Forms.FlexLayout.JustifyContent) property is set to `SpaceEvenly`, which allocates all leftover vertical space equally between all the items, and above the first item, and below the last item.

For more information, see [Xamarin.Forms FlexLayout](flex-layout.md).

## RelativeLayout

A [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) is used to position and size elements relative to properties of the layout or sibling elements. By default, an element is positioned in the upper left corner of the layout. A `RelativeLayout` can be used to create UIs that scale proportionally across device sizes.

Within a [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout), positions and sizes are specified as constraints. Constraints have [`Factor`](xref:Xamarin.Forms.ConstraintExpression.Factor) and [`Constant`](xref:Xamarin.Forms.ConstraintExpression.Constant) properties, which can be used to define positions and sizes as multiples (or fractions) of properties of other objects, plus a constant. In addition, constants can be negative.

> [!NOTE]
> A [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) supports positioning elements outside of its own bounds.

The following XAML shows how to arrange elements in a [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout):

```xaml
<RelativeLayout>
    <BoxView Color="Blue"
             HeightRequest="50"
             WidthRequest="50"
             RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0}"
             RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0}" />
    <BoxView Color="Red"
             HeightRequest="50"
             WidthRequest="50"
             RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=.85}"
             RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0}" />
    <BoxView x:Name="pole"
             Color="Gray"
             WidthRequest="15"
             RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=.75}"
             RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=.45}"
             RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=.25}" />
    <BoxView Color="Green"
             RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=.10, Constant=10}"
             RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=.2, Constant=20}"
             RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToView, ElementName=pole, Property=X, Constant=15}"
             RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToView, ElementName=pole, Property=Y, Constant=0}" />
</RelativeLayout>
```

In this example, layout works as follows:

- The blue [`BoxView`](xref:Xamarin.Forms.BoxView) is given an explicit size of 50x50 device-independent units. It's placed in the upper left corner of the layout, which is the default position.
- The red [`BoxView`](xref:Xamarin.Forms.BoxView) is given an explicit size of 50x50 device-independent units. It's placed in the upper right corner of the layout.
- The gray [`BoxView`](xref:Xamarin.Forms.BoxView) is given an explicit width of 15 device-independent units, and it's height is set to be 75% of the height of its parent.
- The green [`BoxView`](xref:Xamarin.Forms.BoxView) isn't given an explicit size. Its position is set relative to the `BoxView` named `pole`.

> [!WARNING]
> Avoid using a `RelativeLayout` whenever possible. It will result in the CPU having to perform significantly more work.

For more information, see [Xamarin.Forms RelativeLayout](relativelayout.md).

## AbsoluteLayout

An [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) is used to position and size elements using explicit values, or values relative to the size of the layout. The position is specified by the upper-left corner of the child relative to the upper-left corner of the `AbsoluteLayout`.

An [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) should be regarded as a special-purpose layout to be used only when you can impose a size on children, or when the element's size doesn't affect the positioning of other children. A standard use of this layout is to create an overlay, which covers the page with other controls, perhaps to protect the user from interacting with the normal controls on the page.

> [!IMPORTANT]
> The `HorizontalOptions` and `VerticalOptions` properties have no effect on children of an `AbsoluteLayout`.

Within an [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout), the [`AbsoluteLayout.LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) attached property is used to specify the horizontal position, vertical position, width and height of an element. In addition, the [`AbsoluteLayout.LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) attached property specifies how the layout bounds will be interpreted.

The following XAML shows how to arrange elements in an [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout):

```xaml
<AbsoluteLayout Margin="40">
    <BoxView Color="Red"
             AbsoluteLayout.LayoutFlags="PositionProportional"
             AbsoluteLayout.LayoutBounds="0.5, 0, 100, 100"
             Rotation="30" />
    <BoxView Color="Green"
             AbsoluteLayout.LayoutFlags="PositionProportional"
             AbsoluteLayout.LayoutBounds="0.5, 0, 100, 100"
             Rotation="60" />
    <BoxView Color="Blue"
             AbsoluteLayout.LayoutFlags="PositionProportional"
             AbsoluteLayout.LayoutBounds="0.5, 0, 100, 100" />
</AbsoluteLayout>
```

In this example, layout works as follows:

- Each [`BoxView`](xref:Xamarin.Forms.BoxView) is given an explicit size of 100x100, and is displayed in the same position, horizontally centered.
- The red [`BoxView`](xref:Xamarin.Forms.BoxView) is rotated 30 degrees, and the green `BoxView` is rotated 60 degrees.
- On each [`BoxView`](xref:Xamarin.Forms.BoxView), the [`AbsoluteLayout.LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) attached property is set to `PositionProportional`, indicating that the position is proportional to the remaining space after width and height are accounted for.

> [!CAUTION]
> Avoid using the [`AbsoluteLayout.AutoSize`](xref:Xamarin.Forms.AbsoluteLayout.AutoSize) property whenever possible, as it will cause the layout engine to perform additional layout calculations.

For more information, see [Xamarin.Forms AbsoluteLayout](absolutelayout.md).

## Input transparency

Each visual element has an [`InputTransparent`](xref:Xamarin.Forms.VisualElement.InputTransparent) property that's used to define whether the element receives input. Its default value is `false`, ensuring that the element receives input.

When this property is set on a layout class, its value transfers to child elements. Therefore, setting the [`InputTransparent`](xref:Xamarin.Forms.VisualElement.InputTransparent) property to `true` on a layout class will result in all elements within the layout not receiving input.

## Layout performance

To obtain the best possible layout performance, follow the guidelines at [Optimize layout performance](~/xamarin-forms/deploy-test/performance.md#optimize-layout-performance).

In addition, page rendering performance can also be improved by using layout compression, which removes specified layouts from the visual tree. For more information, see [Layout compression](layout-compression.md).

## Related links

- [Xamarin.Forms Layouts (video)](https://youtu.be/4HlLjTZQzjM)
- [Xamarin.Forms StackLayout](stacklayout.md)
- [Xamarin.Forms Grid](grid.md)
- [Xamarin.Forms FlexLayout](flex-layout.md)
- [Xamarin.Forms AbsoluteLayout](absolutelayout.md)
- [Xamarin.Forms RelativeLayout](relativelayout.md)
- [Optimize layout performance](~/xamarin-forms/deploy-test/performance.md#optimize-layout-performance)
- [Layout compression](layout-compression.md)
