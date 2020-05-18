wrote this when i sent someone the code, might be useful idk

Hendo Defendo: an Etonian take on whack-a-mole.

Please don't use a trackpad :)

Requirements if running from source:
  Python 3.x
  tkinter or Tkinter
  PIL
  termcolor

--i only build for x64 screw you--

If you want to change difficulty basically change Game.MIN_TIME_UP and Game.MAX_TIME_DN, the other vars are really spicy

To control printing to stdout use Game.debug, there's no other logging because it's completely pointless.
(I've left printing enabled in the source, disabled in the build.)

If you're running from another script, use Game.window.mainloop() to start a Game.

When creating a Game, if you don't specify an IMAGE_PREFIX it assumes that images are stored in os.getcwd()/media/

If adding custom images of Hendo make sure they're 180x180px and inside IMAGE_PREFIX/hendos/, the game will automatically detect them.
If adding other custom images, make sure they're 180x180px and included in the Game's variables (with the exception of icon.ico, which can be any size that works with tk.)
