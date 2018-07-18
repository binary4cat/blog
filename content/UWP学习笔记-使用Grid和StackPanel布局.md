title: 'UWP学习笔记:使用Grid和StackPanel布局'
date: 2016-05-08T21:40:48+08:00
tags: UWP学习笔记
categories: UWP
permalink: UWP-study-notes-use-Grid-and-StackPanel-layout
description: UWP学习笔记:使用Grid和StackPanel布局
---
　　第一篇关于UWP的笔记，学了几天的XAML布局，赶紧总结一下，免得健忘症发作。

# [Grid](https://msdn.microsoft.com/zh-cn/library/windows/apps/windows.ui.xaml.controls.grid.aspx)
　　Grid顾名思义就是对页面进行网格布局的标签，可以将页面分成行(Row)和列(Column)交错的网格形式。
### 设置行
　　`<Grid.RowDefintions></Grid.RowDefintions>`标签可以设置行：
```
<Grid.RowDefintions>
    <RowDefinition Height="Auto"/>
    <RowDefinition Height="*"/>
    <RowDefinition Height="2*"/>
    <RowDefinition Height="100"/>
    ...
</Grid.RowDefintions>
```
<!--more-->
   `<RowDefinition/>`标签可以设置每一行的样式，其属性`Height`可以设置该行的高度，设置的值有三种形式：
   　　1. `Auto`：表示高度自动，以该行中高度最大的那个元素的高为准。
   　　2. `*`：星号表示百分比，比如上面的示例代码中，一个总共加起来有三个星，那么就是把`Height="Auto"`和`Height="100"`这两个所占用的除去，剩余的分成三份，第一个占用一份，第二个占用两份 。
   　　3. 绝对值：设置绝对值，单位是像素。(在UWP中不建议使用绝对值，因为UWP针对多种分辨率的，设置成百分比或者Auto会有更好的适应度。)

### 设置列
　　　`<Grid.ColumnDefinitions></Grid.ColumnDefinitions>`标签可以设置列：
```
<Grid.ColumnDefinitons>
    <ColumnDefinition Width="Auto"/>
    <ColumnDefinition Width="*"/>
    <ColumnDefinition Width="2*"/>
    <ColumnDefinition Width="100"/>
    ...
</Grid.ColumnDefinitions>
```
　　`<ColumnDefinition/>`标签可以设置每一列的样式，使用方式与设置行的方式一样。

### 怎么在Grid划分的网格中填充内容
　　使用附加属性可以实现，摘录一段[MSDN](https://msdn.microsoft.com/zh-cn/library/windows/apps/mt185579.aspx)的解释：
> 附加属性是一种 XAML 概念。它在概念上类似于可在 XAML 中的任何对象元素上设置的全局属性。附加属性通常定义为一种专门形式的依赖属性，在所有者类型的对象模型中没有传统的属性包装器。

```
<TextBlock Text="this is title" FontSize="48" Grid.ColumnSpan="3"/>
<TextBlock Grid.Row="1" Text="name:" Grid.RowSpan="3"/>
<TextBox Grid.Row="1" Grid.Column="1" Margin="0,0,0,10"/>
<TextBlock Grid.Row="2" Text="name:" VerticalAlignment="Center"/>
<TextBox Grid.Row="2" Grid.Column="1" Margin="0,0,0,10"/>
<TextBlock Grid.Row="3" Text="name:" VerticalAlignment="Center"/>
<TextBox Grid.Row="3" Grid.Column="1" Margin="0,0,0,10"/>
<Button Grid.Row="4" Grid.Column="1" Content="save"/>
```
　　上述示例代码中的`Grid.Row`、`Grid.Column`、`Grid.ColumnSpan`、`Grid.RowSpan`都是附加属性，用来指定当前的元素在Grid网格中的位置：
　　　　1. `Grid.Row`：指定当前元素在网格中位于第几行中，默认从0开始。
　　　　2. `Grid.Column`：指定当前元素在网格中位于第几列种，默认从0开始。
　　　　3. `Grid.RowSpan`：指定当前的元素横跨几行。
　　　　4. `Grid.ColumnSpan`：指定当前的元素跨几列。


# [StackPanel](https://msdn.microsoft.com/zh-cn/library/windows/apps/windows.ui.xaml.controls.stackpanel.aspx)
　　可以将子元素排列成一行(沿水平方向或者垂直方向)。默认是垂直方向。
　　`<StackPanel></StackPanel>`标签可以进行无限嵌套。
### 示例代码
　　下面我们用爆伯视频中的一个例子

```
<StackPanel>
        <TextBlock FontSize="48">我的第一个标题</TextBlock>
        <StackPanel Orientation="Horizontal">
            <TextBlock TextWrapping="Wrap" Width="250">
                sadasdasdsadasdasdsadasdasdsadasdasdsadasda 
                ...
            </TextBlock>
            <TextBlock TextWrapping="Wrap" Width="500">
                sadasdasdsadasdasdsadasdasdsadasdasdsadasdas
                ...
            </TextBlock>
            <StackPanel Orientation="Horizontal">
                <Rectangle Width="200" Height="200" Fill="Blue" VerticalAlignment="Top"/>
                <StackPanel>
                <Rectangle Width="100" Height="100" Fill="Red"/>
                <Rectangle Width="100" Height="100" Fill="Yellow"/>
                </StackPanel>
            </StackPanel>
        </StackPanel>
    </StackPanel>
```
![](http://ww3.sinaimg.cn/mw690/c55a7aeegw1f3offhfeikj20vz0gg3zn.jpg)
　　`Orientation`：该属性用来设置排列的方式。灵活使用该属性，就可以使用StackPanel标签做各种布局了。

# Grid和StackPanel结合的例子
　　还是使用爆伯视频教程中的一个例子：
```
<Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}" >
        <Grid Width="500" HorizontalAlignment="Left">
            <Grid.RowDefinitions>
                <RowDefinition Height="Auto"/>
                <RowDefinition Height="Auto"/>
                <RowDefinition Height="Auto"/>
            </Grid.RowDefinitions>
            <StackPanel Orientation="Horizontal">
                <TextBlock FontSize="24">text1</TextBlock>
                <StackPanel   Margin="20,0,0,20">
                    <TextBlock TextWrapping="Wrap" Width="300">
                        sdfghfghdjfghdjfhgsdfghfghdjfghdjfhg
                        ...
                    </TextBlock>
                    <StackPanel Orientation="Horizontal">
                        <Rectangle Height="30" Width="100" Fill="Red"/>
                        <Rectangle Height="30" Width="100" Fill="Blue"/>
                        <Rectangle Height="30" Width="100" Fill="Yellow"/>
                    </StackPanel>
                </StackPanel>
            </StackPanel>
            <StackPanel Orientation="Horizontal" Grid.Row="1">
                <TextBlock FontSize="24">text1</TextBlock>
                <StackPanel   Margin="20,0,0,20">
                    <TextBlock TextWrapping="Wrap" Width="300">
                        sdfghfghdjfghdjfhgsdfghfghdj
                        ...
                    </TextBlock>
                    <StackPanel Orientation="Horizontal">
                        <Rectangle Height="30" Width="100" Fill="Red"/>
                        <Rectangle Height="30" Width="100" Fill="Blue"/>
                        <Rectangle Height="30" Width="100" Fill="Yellow"/>
                    </StackPanel>
                </StackPanel>
            </StackPanel>
            <StackPanel Orientation="Horizontal" Grid.Row="2">
                <TextBlock FontSize="24">text1</TextBlock>
                <StackPanel  Margin="20,0,0,20">
                    <TextBlock TextWrapping="Wrap" Width="300">
                        sdfghfghdjfghdjfhgsdfghfhdjfghdjfhgsdf
                        ...
                    </TextBlock>
                    <StackPanel Orientation="Horizontal">
                        <Rectangle Height="30" Width="100" Fill="Red"/>
                        <Rectangle Height="30" Width="100" Fill="Blue"/>
                        <Rectangle Height="30" Width="100" Fill="Yellow"/>
                    </StackPanel>
                </StackPanel>
            </StackPanel>
            
        </Grid>
    </Grid>
```
![](http://ww3.sinaimg.cn/mw690/c55a7aeegw1f3oflfhtatj20u00qudhx.jpg)