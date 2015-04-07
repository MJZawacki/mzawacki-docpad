---
layout: post
title: Pressure sensitive inking using the Windows Inking API
date: 2015-04-07
---

## History

Microsoft has a long history supporting pen based computing. The first pen based computers started to arrive around 1990 with GO Corporation's PenPoint OS and shortly thereafter Micrsoft released Windows for Pen Computing on Windows 3.1. But I won't bore you with the [details](http://en.wikipedia.org/wiki/Pen_computing); This post is about using the Windows 8.1 API for inking.

## Windows 8 Inking API

With Windows 8, Microsoft introduced the Inking API in the Windows Runtime. Considering the success of the Surface Pro 3 w/ active stylus, this API is also getting a lot of attention in the  upcoming Windows 10 release to increase it's capabilities and performance.

Most of the samples I've found for the new API use fixed widths for the ink strokes either because they are writing for finger based input or it just completely ignores the pressure input from the stylus.  While it's possible to read the pointer input from the user without handling pressure, the rendered ink isn't providing the tangible experience that users expect when they write with a stylus. In addition, I think that our recognition of handwriting relies on the nuances of the strokes that only come when we variying the thickness of the ink while stroking a letter or word.

I want to highlight some of the key methods I've used in this  [ink app sample](https://github.com/MJZawacki/InkDemo) that can help you build pressure sensitive inking into your app.

## The Implementation

The [InkManager](https://social.msdn.microsoft.com/Search/en-US/windows/apps?query=InkManager&refinement=180&ac=2) class is the primary entry point to the API. It handles stroke persistence, text recognition as well as modes for stroke erase and selection. However, the rendering of the ink is still left to you to implement.

The typical architecture, regardless of language, is to implement OnPointerPressed, Released, Moved, Exited to gather the stroke data from the pointer input and update the InkManager to persist each stroke. At the same time, those events should be rendering paths to the screen. This is where you have a couple of options depending on what languages you choose for your application.

## Ink Rendering in CSharp

The easiest way to render ink in C# is to create Path or Line objects and bind those to a XAML Canvas. It is possible to reduce the amount of data stored by creating bezier curves in the stroke data and on the Canvas. However, I've found that while this may improve the smoothness of the curves, it eliminates the ability to vary the ink thickness. I typically render the raw data as lines with varying thickness and the events are fired fast enough that the ink does not appear jagged.

Most of the work is done in the OnPointerMoved event handler:


    private void InkPanelOnPointerMoved(object sender, PointerRoutedEventArgs e)
    {
    var InkCanvas = sender as Canvas;
    if (e.Pointer.PointerId == selectedItem.PenID)
    {
      PointerPoint pt = e.GetCurrentPoint(InkCanvas);
      Point currentContactPt = pt.Position;
      if (selectedItem.InkStrokeManager.Mode == InkManipulationMode.Inking)
      {
        if (Distance(currentContactPt,
          selectedItem.PreviousContactPoint) > resolution)
        {
          Line line = new Line()
          {
            X1 = selectedItem.PreviousContactPoint.X,
            Y1 = selectedItem.PreviousContactPoint.Y,
            X2 = currentContactPt.X,
            Y2 = currentContactPt.Y,
            StrokeThickness = selectedItem.BrushSize.Height * pt.Properties.Pressure,
            Stroke = new SolidColorBrush(Windows.UI.Colors.Black),
            StrokeEndLineCap = PenLineCap.Round
          };
          selectedItem.SheetMusic.InkStrokeSegments.Add(line);
          selectedItem.PreviousContactPoint = currentContactPt;
          if (penmodel.DebugFlag)
          {
            var width = selectedItem.BrushSize.Width * pt.Properties.Pressure * 1.2;
            var height = selectedItem.BrushSize.Height * pt.Properties.Pressure * 1.2;
            Ellipse ellipse = new Ellipse()
            {
              Width = width,
              Height = height,
              Stroke = new SolidColorBrush(Windows.UI.Colors.Red),
              Fill = new SolidColorBrush(Windows.UI.Colors.Red)
            };
            // Draw the line on the canvas by adding the Line object as
            // a child of the Canvas object.
            Canvas.SetLeft(ellipse, currentContactPt.X - width / 2);
            Canvas.SetTop(ellipse, currentContactPt.Y - height / 2);
            Canvas.SetZIndex(ellipse, 99);
            selectedItem.SheetMusic.InkStrokeSegments.Add(ellipse);
          }

          // Pass the pointer information to the InkManager.
          selectedItem.InkStrokeManager.ProcessPointerUpdate(pt);
        }

        } // if Inking
        else if (selectedItem.InkStrokeManager.Mode == InkManipulationMode.Erasing)
        {
          var rect = (Windows.Foundation.Rect) selectedItem.
          InkStrokeManager.ProcessPointerUpdate(pt);
          if (rect.Height != 0 && rect.Width != 0)
          {
            // Need to clear canvas and re-add the existing lines
            selectedItem.SheetMusic.InkStrokeSegments.Clear();
            RenderAllStrokes();
          }
        }
      }

      e.Handled = true;

    }


A few things to note

* It's key to use the Round StrokeEndLineCap on the Line so your strokes look smooth and connected.
* The StrokeThickness is varied by the Point Pressure property. I use the BrushSize in the app settings to multiply the pressure and create a real stroke thickness.
* Using the Canvas does increase the latency between the pen and the ink on screen a tiny amount but I've found it doesn't have much impact on the rendering. The downside of this model is that as the rendered strokes increase, the delete and select methods start to slow down as you re-render the full screen when data is removed from the Canvas.



## Native DirectX

If you are handling more complex rendering you should consider writing a native DirectX controls to capture the live data and add the stroke to the screen. In DirectX rendering, the same pointer methods are used to capture the data but the DirectX code is able to react to the pointer data much more quickly. Additionally, the Render loop is much more performant when writing the complex, variable thickness lines required to render each stroke. For a good sample of this, check out the SwapChainPanel implementation in this [sample](https://code.msdn.microsoft.com/windowsapps/InkingDemo-CS-Sample-ba695bf2). While it doesn't have the pressure sensitive ink capture, it does a great job of showing that it isn't that difficult to implement the rendering in C++.


#References

[History of Pen Computing](http://en.wikipedia.org/wiki/Pen_computing)

[Ink API](https://social.msdn.microsoft.com/Search/en-US/windows/apps?query=InkManager&refinement=180&ac=2)

[InkDemo on GitHub](https://github.com/MJZawacki/InkDemo)

[SwapChainPanel Renderer](https://code.msdn.microsoft.com/windowsapps/InkingDemo-CS-Sample-ba695bf2)
