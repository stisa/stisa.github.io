---
title: "Kivy Layout"
date: "2015-08-11T18:49:26+02:00"
draft : false
layout: post
tags: ["kivy","example","python"]
---
So I've started messing around with [Kivy](http://kivy.org/), and I was stuck trying to understand just *how* the layout thing works when using .kv code.
And I think I finally understood what I was doing wrong. I was basically setting the layout of the small square that is the default widget size in Kivy so, knowing that, I tried creating the base widget as a layout and it *worked* :D

Example:

Python code:  
```python
	from kivy.app import App
	from kivy.uix.gridlayout import GridLayout

	class TestApp(App):
		def build(self):
			root : Root()
			return root

	# define the main widget as a GridLayout or any other layout of your choice		
	class Root(GridLayout):
	    pass

	if __name__ : '__main__':
		TestApp().run()  
```

Kv code:  
```python
	#:kivy 1.8.0
	<Root>:
	# define layout variables
		cols: 2

		Label:
			font_size: 70
			text: "Test"
		Label:
			font_size: 70
			text: "Test"
```
Result:  

![Imgur](http://i.imgur.com/LCQdm11.jpg?1)
