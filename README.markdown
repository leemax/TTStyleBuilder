Overview
========
TTStyleBuilder is an interactive tool for creating and exploring Three20's TTStyle rendering pipelines. The tool can run in either the iPhone simulator or directly on the device. Some more information about the design and motivation of TTStyle is available [here](http://groups.google.com/group/three20/web/understanding-ttstyle).

Video [demo/screencast](http://www.vimeo.com/5429347)

Release Notes
==============
**v0.1 (July 7, 2009)**  

If you have already cloned TTStyleBuilder before this release, you may need to do 'git submodules sync' because I switched the three20 submodule URL from Joe Hewitt's Three20 repository on GitHub to my Three20 fork (klazuka) on GitHub.

Getting Started
===============
1. git clone git://github.com/klazuka/TTStyleBuilder.git
2. cd TTStyleBuilder
3. git submodule update --init
4. open TTStyleBuilder.xcodeproj
5. Build and Go!

Usage Instructions
==================
TTStyleBuilder has 3 modes

- **Create a New Style** (instantiates a blank TTStyle and displays it)
- **Explore a Style Sheet** (finds all methods defined on the global TTStyleSheet that return TTStyle objects (see footnote 1))
- **Load from Disk** (finds all files in the ARCHIVES_DIR that have the '.ttstyle' extension (see footnote 2))

When you load a TTStyle into the editor, TTStyleBuilder displays the style linked list (hereafter referred to as the 'pipeline') from top to bottom. So the first row in the table view is the head of the pipeline, and the last row is the tail of the pipeline.

Below the pipeline table view is the "live style preview" area. This is where the result of the pipeline renders to. Whenever you make a change to the pipeline or the properties of the individual style nodes, the "live style preview" will refresh to display the current state.

You can append new styles to the end of the pipeline by tapping the "+" button in the upper-right corner.

You can delete and re-arrange styles within the pipeline by tapping the "Edit" button. 

Tap the "Settings" button to change the settings for the live style preview view. The live style preview also acts as the TTStyleDelegate, so you can specify the text and image to be used when styles like TTTextStyle query their delegate for an NSString or UIImage.

The Object Editor System
========================
One of the guiding principles while implementing this tool is that it should put very little burden on subclassers of TTStyle, and it should avoid patching Three20 as much as possible. In order to do this, TTStyleBuilder relies heavily on the Objective-C runtime to dynamically reflect on the system in memory. TTStyleBuilder implements a generic object/property editing system that could, theoretically, be used in other iPhone apps that need an easy way to provide a UI for editing objects. Rather than defining an editor plugin for each TTStyle subclass (which would increase the burden on subclassers), I instead chose to write the plugins for each basic type (int, float, CGSize, UIColor, etc.).

Writing Editor Plugins
----------------------
1. Inherit from UIViewController (or a subclass of UIViewController)
2. Implement the ValueEditor protocol.

Adding New TTStyles and TTShapes
--------------------------------
1. Create your subclass.
2. Expose your instance variables through Objective-C 2.0 properties.
3. Implement the class factory method -(TTStyle*)prototypicalInstance.
4. Don't forget to implement initWithCoder: and encodeWithCoder:

Known Issues
============
* When browsing a style sheet, only selectors that do not take any arguments are shown (so stuff like "blackToolbarButton:" will not be shown because it takes an argument). This might be impossible to fix without making changes to TTStyle.{h,m} which is something that I've been reluctant to do.
* There are still many property types that do not yet have an editor plug-in (for instance, UIFont and UIImage).

Footnotes
=========
**Note 1:**
Actually, there is not enough runtime information to determine which methods return TTStyle*, so I just list all methods that return an 'id'. When the user taps on a method name row, I first verify that calling that method actually returns a TTStyle object before allowing the user to edit the returned object. 

**Note 2:**
The style archives directory is different depending on whether you are running TTStyleBuilder on the simulator versus on the device.
 
* Simulator: ~/Desktop/
* Device: /iPhoneAppRoot/Documents/

-keith
(klazuka)