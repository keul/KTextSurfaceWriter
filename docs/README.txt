Introduction
============

The scope of this package is to simply display a text inside a `pygame.Rect`__ instance.
After obtainer a KTextSurfaceWriter instance, you can use its ''draw'' method to display
the text over a `pygame.Surface`__.

__ http://www.pygame.org/docs/ref/rect.html
__ http://www.pygame.org/docs/ref/surface.html

Visit the `home page of the project`__ for more.

__ http://keul.it/develop/python/ktextsurfacewriter/

Examples
========

Here a fully example of use of this library. Even if I use the Python doctest format, this isn't a
politically correct test because I wait for user input and no real tests are done on the results.

Maybe somday I'll fix this!

Init all the pygame stuff
-------------------------

Lets begin loading the KTextSurfaceWrite class

   >>> from ktextsurfacewriter import KTextSurfaceWriter

Now init the pygame environment

   >>> import pygame
   >>> from pygame.locals import *
   >>> import pygame.font
   >>> pygame.font.init()
   >>> screen = pygame.display.set_mode((640,480), 0, 32)

To make things more complicated, I'll not draw directly on the screen but I get a Surface where I can draw

   >>> surface = pygame.Surface( (400,400), flags=SRCALPHA, depth=32 )
   >>> surface.fill( (255,255,255,255) )
   <rect(0, 0, 400, 400)>

Now we blit the surface on the screen. As soon as we will repeat this procedure several times, lets create out first
dummy function (those function aren't useful outside this test environment):

   >>> def blitSurface():
   ...     screen.blit(surface, (50,50) )
   ...     pygame.display.update()

So we can call it for the first time.

   >>> blitSurface()

This is a graphical test, so we need to delay the drawing.
We wait for user input before going on. To do this I create a silly method that I'll call often later

   >>> def waitForUserAction():
   ...     while True:
   ...
   ...         for event in pygame.event.get():
   ...             if event.type == QUIT:
   ...                 import sys
   ...                 sys.exit(0)
   ...             if event.type==KEYDOWN:
   ...                 return

Ok, lets call it for the first time.

   >>> waitForUserAction()

Simple text drawing
-------------------

We are ready to create our instance of the class. The __init__ method want a `pygame.Rect`__
where the text will be displayed.

   >>> text_rect = pygame.Rect( (10,10),(350,350) )
   >>> text_rect
   <rect(10, 10, 350, 350)>
   
This mean that the text will be displayed later on a surface starting from x,y coordinates (10,10)
from the top left corner.
The text will also not be larger than 350 pixels in height and width.

Now we will load the text inside the KTextSurfaceWriter, but before this we (obviously) need the instance.

   >>> ktext = KTextSurfaceWriter(text_rect)

Now the text.

   >>> import example_texts
   >>> ktext.text = example_texts.EXAMPLE_TEXT_1

Now some color for a better display experience

   >>> ktext.color = (0,0,0,255)
   >>> ktext.fillcolor = (155,155,155,255)

We changed the fillcolor to a gray because this way is simple to see that we drawn a white surface onto a
black screen, and onver this surface we blit the text inside a rectangle shorter that the surface itself.

Ok, stop talking, lets display it!

   >>> ktext.draw(surface)
   >>> blitSurface()
   >>> waitForUserAction()

Simple (but longer) text drawing
--------------------------------

The text above is very short so we didn't tested all the KTextSurfaceWriter features right now.
Lets try with a longer ones...

   >>> ktext.text = example_texts.EXAMPLE_TEXT_2
   
Now we can immediatly test this new text.

   >>> ktext.draw(surface)
   >>> blitSurface()
   >>> waitForUserAction()

What is changed? Even if the EXAMPLE_TEXT_2 string is not splitted on several lines, the displayed text never exit from
the constrain `pygame.Rect`__ instance!

And what is the text will be already splitted on several lines?

   >>> ktext.text = example_texts.EXAMPLE_TEXT_3
   >>> ktext.draw(surface)
   >>> blitSurface()
   >>> waitForUserAction()

Obviously the carriage return in the text is keep.
You can also think about do a little indentation, to make different render for new lines that are wanted (because are
inserted into the text) and other that are generated automatically for a too long text.

   >>> ktext.justify_chars = 3

If I draw again now, I get no difference. For performance needs the KTextSurfaceWriter.draw method don't evaluate every
time the text and the font graphical stuff, but memoize the result.
When you change the text you also automatically invalidate this cache, but if you didn't change anything,
you can always do it manually.

   >>> ktext.invalidate()

Ok, now you'll see the difference.

   >>> ktext.draw(surface)
   >>> blitSurface()
   >>> waitForUserAction()
   
Text too loong for a single line
--------------------------------

As you want to draw thext always inside a rectangle, or even if you use a `pygame.Rect`__ big as the entire surface,
is always possible that the text is too long.

What happen in this case?

   >>> ktext.text = example_texts.EXAMPLE_TEXT_4
   >>> ktext.draw(surface)
   >>> blitSurface()
   >>> waitForUserAction()

The default behaviour is to cut the word at the maximum length possible. This because without saying anything the
KTextSurfaceWriter instance use a criteria called CUT.
You can change this to another value

   >>> ktext.line_length_criteria = "split"

Then if we repeat the test we get a new behaviour.

   >>> ktext.invalidate()
   >>> ktext.draw(surface)
   >>> blitSurface()
   >>> waitForUserAction()

The text now is splitted on many lines and no one characters gets lost. Of course, the too long single word is splitted
by an ugly barbarian at some random character (depending on the rect size, font, and so on).

Another possible value for the 'line_length_criteria' attribute is "overflow".

   >>> ktext.line_length_criteria = "overflow"
   >>> ktext.invalidate()
   >>> ktext.draw(surface)
   >>> blitSurface()
   >>> waitForUserAction()

TODO
====

 * A way to scroll a too (vertically) long text.

Subversion and other
====================

The SVN repository is hosted at the `Keul's Python Libraries`__

__ https://sourceforge.net/projects/kpython-utils/

