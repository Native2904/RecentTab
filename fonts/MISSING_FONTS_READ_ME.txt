Two font files are supposed to live in this folder and don't yet:

  JetBrainsMono-Regular.ttf
  FiraCode-Regular.ttf

They couldn't be downloaded and included automatically here - please
grab them yourself from the official sources and drop them into this
folder before building:

  JetBrains Mono:
  https://github.com/JetBrains/JetBrainsMono/releases
  (take JetBrainsMono-Regular.ttf from the fonts\ttf\ folder inside
  the release zip)

  Fira Code:
  https://github.com/tonsky/FiraCode/releases
  (take FiraCode-Regular.ttf from the ttf\ folder inside the release
  zip)

Both are SIL Open Font License 1.1 - free to bundle and redistribute
with this plugin, commercially or not, no attribution required (though
obviously appreciated).

Once both files are in place, RecentTab.ini's MonoFont=/MonoFontName=
already point at JetBrains Mono by default - nothing else to configure.

Delete this file once the two fonts are actually here.
