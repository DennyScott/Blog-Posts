# Unity and Sublime
##My Struggle With an IDE

I despise MonoDevelop. Developing in Unity has generally been a treat. The version control isn't always the greatest, but with the mix of plugins, support, and an entity-component system, Unity has always had huge upsides. 

**But I can't stand Monodevelop.** It's Vim emulation is spotty, I don't like the available color themes, its slow, it doesn't have a strong community behind it, lack of plugins, and most importantly, you can't open multiple windows in it. 

This is 2015. My toaster can open multiple windows.

While developing Mobile Apps, Vim has normally been my go-to text-editor. I've also been inclined to use Sublime for a number of projects, on days that plugins frustrate me (when your using plugins last updated in 2005, you know desperation). Latley, a lot of my projects at work revolve around Unity development. Having worked a lot with Unity in my spare time, I was more then happy to sink my teeth into these new projects. But then my old familiar nemesis returned. Monodevelop.

I realize a good solution would be to use Visual Studio, and I understand a large amount of Unity developers use VS. In honesty, I have not tried Visual Studio, though I'm sure I will use it (or the dreaded MonoDevelop) for bebugging scripts one of these days, so I can't give it a rating in either way. But I decided some time ago that for me personally, I'm going to change my Unity editing environment to Vim. 

This is a lesson for another day. 

When I showed some of my colleagues the success I had achieved with Vim, and about my new freedom from Monodevelop, I had expected celebrations, a revolution throughout the office, possibly [a small statue made out of pure gold](https://www.youtube.com/watch?v=XMjYY3tbX1k). But they looked slightly bemused, and asked one question.

"Vim? Can you do that in Sublime Text?"

*(Ok thats sort of two questions, but you get the point.)*

So here it is my fickle friends, a way to use Sublime Text as your primary development environment with Unity. Lets outline some of the goals.

We want:
* Autocompletion (intellisense). These autocompletions cannot just be static methods saved in the editor, but point to the actual available methods.
* Syntax Error Reporting.
* Proper Syntax Highlighting
* XML Documentation
* Code Folding on Regions
* Go To Definition Support
* Vim Keybindings (It's my blog dammit!)

Aside from that, I'd also like to update the look of Sublimes theme, Icon, and Syntax colors. This isn't required, but I find the standard a bit boring. The goal is to eventually look like this: 

![Editor](https://raw.githubusercontent.com/DennyScott/Blog-Posts/master/released/Sublime-Unity/Screen%20Shot%202015-03-07%20at%208.24.51%20PM.png)

*Note: This style isn't required, we can talk about some needed parts, but color is subjective, and totally up to you!*

I develop on a Mac, so I'm going to start from there. If your using Windows, don't worry, you should have an easier time with this. This first section will have a bit of work in it just for Mac Developers, so I'll mark where Windows developers should be able to jump in.

I'm also going to go through this with the assumption that you don't have some of the basic tools like package control. If you know them, feel free to skip those segments!

##Mac

###HomeBrew
First thing, we need to install [Home Brew](http://brew.sh/). Home brew is a package management system for Mac OSX. I won't go into detail, you can download it and install it through the terminal with: 

```
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

###Mono
Homebrew has a ton of great packages, so I recommend checking out other things it has installed. For us we want Mono. Mono is an open-source implementation of the .NET framework. It's going to allow us to build and run C# files on our mac. You install it in the terminal like such:

```
brew install mono
```

##Windows and Mac

###Sublime Text 3
From here on out, we'll be primarily working in Sublime Text 3. It's a very popular text-editor, that works across all platforms. It's got a ton of baked in features, a huge community, and a ton of plugins to use. To install sublime, [download it here](http://www.sublimetext.com/3). Sublime Text 3 is in Beta, and free to try out as long as you like.

###Package Control
[Package control](https://packagecontrol.io/installation) is a package management system for Sublime Text. It's a great way to install packages, that simply allows users to find, download, and install plugins all from within the text-editor. A must have for any sublime text user. To install it, type ``ctrl + ` `` in sublime. A console should appear at the bottom of the page. Paste this code in and press enter:

```
import urllib.request,os,hashlib; h = 'eb2297e1a458f27d836c04bb0cbaf282' + 'd0e7a3098092775ccb37ca9d6b2e4b7d'; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); by = urllib.request.urlopen( 'http://packagecontrol.io/' + pf.replace(' ', '%20')).read(); dh = hashlib.sha256(by).hexdigest(); print('Error validating download (got %s instead of %s), please try manual install' % (dh, h)) if dh != h else open(os.path.join( ipp, pf), 'wb' ).write(by)
```

Its recommended from here you restart Sublime.

###Omnisharp
[Omnisharp](http://www.omnisharp.net/) allows for Cross-Platform development of .NET in the editor of our choice. Plugins exist for most of the popular editors, including Sublime. It gives us a ton of great features, including intellisense, Go To Definition, Syntax/Semantic Error Highlighting, and more.

We will be using (Omnisharp-sublime)[https://github.com/OmniSharp/omnisharp-sublime]. To install it type `cmd + shift + P` in Sublime, to bring up your command palette. Then type `install`, and select the option "Package Control: Install Package". After a few seconds, another dialog box will open. This is a repository of plugins available for Sublime. Type in `Omnisharp`, and select the Omnisharp plugin.

###Create Project Settings
Once the plugin is installed, we need to update our Sublime project to work with Unity. Open the root folder of your Unity Project in Sublime. Lets assume your project is called "SuperRPG". You should have a file in this folder called "SuperRPG.sln". With this root folder open in Sublime use `Project` > `Save Project As`, and name your Project the same name as your Unity Project, and place it in the root file so it is with the "SuperRPG.sln" from before.

Then place the following in the created SuperRPG.sublime-project:

```
{
    "folders":
    [
        {
            "path": ".",
            "file_exclude_patterns": ["*.meta"],
        },
    ],
    "solution_file": "./SuperRPG.sln"
}
```

When doing your own project, remember to change SuperRPG to the name of your project. It's not necessary to store the sublime-project file here, but it does help if another teammate is using Sublime. I should also note, if you are using github, I recommend adding this to your .gitignore:

```
*.sublime-workspace
```

###Sync With Unity
Finally we need to make sure that all our sln files have been created. To do this, go to Unity, open your project, and in the menu go `Assets` > `Sync MonoDevelop Project`. This will build the files we need. 

Before we finish Omnisharp off, I want to note that I won't be making Sublime the default text editor when double clicking a script in Unity. That's because this particular set up will only start the omnisharp server by opening the project using our .sublime-project. If you open the file directly in Unity, that server won't start. I'm sure you can set that up with Sublime, as my Vim is set up to function as such, but thats a bit outside the scope of this post.

To open the project again in sublime, and start the server, you can do so by going to `Project` > `Open Project...`, and selecting the .sublime-project in the Root Folder of your Unity project.

###Autocomplete
With Sublime open to the project, you can try out Omnisharp. As of right now you can trigger the omnisharp server with `ctrl + space`. This can be a bit frustrating, so lets fix it up. Make sure you are in a C# file, and the Syntax is set to C# (bottom right of sublime). Go to `Sublime Text` > `Preferences` > `Settings - More` > `Syntax Specific - User`. Paste the following in:

```
 {
    "auto_complete": true,
    "auto_complete_selector": "source - comment",
    "auto_complete_triggers": [ {"selector": "source.cs", "characters": ".<"} ],
 }
```

This will allow you to trigger auto complete on . as well!

If your code is not calling the omnisharp, you aren't seeing auto-complete, and a console is popping up on save in your sublime, you may need to reload your sln file. To do so, type `ctrl + shift + p`, and select `OmniSharpSublime: Reload Solution`.

### Features of Omnisharp
Theres so many awesome features of Omnisharp. Feel free to explore them either in the command palette, or you can always right click you code, and select "OmniSharpSublime" to use some other great features!


###XML Docs
We normally use the standard XML documentation for our Unity projects, so after a little searching we found one we like called XmlDocs. You can find it in your Command Palette by searching for plugins again. 

To use XmlDocs, after you completed a method in your C# file, simply go above the method, type `///` and press `tab`. The outline of your docs should be generated, and you can simply tab through the parts necessary. I admit, this feature is lacking from what I have found in Vim!

I also added DocBlockr, just for help on some other documentation, never hurts either. You can find it as well in the Plugins.

###Region Code Folding
One of the requested features was using code folding with Regions. Monodevelop had a nice feature that allowed you to simply fold all Regions. This one is a bit tricky. There is a plugin that allows for exactly that. You can find it in the plugins, it's called "SyntaxFold". 

Once you have installed it, you can type `shift` + `f5` and select `C#` from the opened dialog box. 

You can now fold and unfold regions from the command palette, or you can simply open and close them using arrows in the gutter where the regions are.

*Warning: Code Folding doesn't work great with Omnisharp. Use it at your own caution. If you use both together, I would warn you to open all code folding before using Omnisharp*


##Sublime Styling
Alright, I admit. This part isn't necessarily needed, but I like the look of Atom and often try to mimic some of that style. (It's a little too slow for use right now, IMO). So if you are only here for functional help, that part is complete. Instead here we are going to update the style for Sublime.

###Installing Font
I want to use the Inconsolata, so we'll need to install it to our font system. I am unsure of the procedure in Windows, but for windows we want to go to [Google Fonts](http://www.google.com/fonts/) and search for Inconsolata. Click the "Add To Collection" button on the right, then the Arrow on the Upper Right to download this font. Click the ".zip File" in the popup.

Once that has downloaded. Unzip it and double click each of the .ttf and choose "Install Font".

Now open your Sublime again, and go to `Sublime Text` > `Preferences` > `Settings - User` and paste in the JSON:

```
 // Typography

"font_face": "Inconsolata",
"font_size": 12,
"font_options": ["no_round"],
"highlight_line": true,
"caret_extra_width": 1,
"caret_style": "phase",
"word_wrap": false,
```

###Installing Theme
We're going to install the Predawn theme. Again, you are always welcome to use your own theme, thats just the one I'm using at this time. To do so, download the theme from your command palette, using predawn. Once tht has downloaded, add the following lines in `Sublime Text` > `Preferences` > `Settings - User` :

```
 "theme": "predawn.sublime-theme",
 "tabs_small": true,
 "sidebar_xsmall": true
```

###Changing Sublime Icon
Download one of the New Sublime icons form [here](https://github.com/jamiewilson/predawn/tree/master/dock-icons). I personally like the dark icon. So I went to sublime-predawn-dark-round-icon.icns, and clicked View Raw. This will download the icon for us. The we need to paste this file into ~/Applications/SublimeText/Contents/Resources. Call it "Sublime Text.icns", replacing the old one. Finally, right click the Sublime Text in your Applications folder, and select "Get Info". Drag the dark round icon over the Sublime Icon in the top left.

###Installing Color Scheme
In full credit, the color scheme we are using is one called Bold, by Dayle Rees. All of his work can be found [here](https://github.com/daylerees/colour-schemes). I've modified his color scheme slightly, and named it Unity. The reason I modded it was to tweak the color of the #regions. They came through as basic white text before, and I altered them to blue. 

To download the Unity Theme, [go here](https://raw.githubusercontent.com/DennyScott/Blog-Posts/master/released/Sublime-Unity/unity.tmTheme). Copy the text, and place in a file at ~/Library/Application Support/Sublime Text 3/Packages/User/unity.tmTheme. Once you've pasted it there, go to `Sublime Text` > `Preferences` > `Settings - User` and add:

```
"color_scheme": "Packages/User/unity.tmTheme",
```

###Doing Your Own Regions Alteration
If you want to use your own color theme, but alter the color of Region, you can always open their tmTheme, and add the following:

```
 <dict>
            <key>name</key>
            <string>Region</string>
            <key>scope</key>
            <string>meta.toc-list.region.source.cs, meta.preprocessor.source.cs</string>
            <key>settings</key>
            <dict>
                <key>foreground</key>
                <string>#00A8C6</string>
            </dict>
        </dict> 
```

By Changing the foreground Hex (#00A8C6), you can alter the color of region. The rest is up to you!

##Conclusion

I hope this helped some of you guys transition to Sublime. If anyone has any feedback, you can always leave me comment by emailing me at dennyscott301@gmail.com. I'm sure I'll post this at some point in the Unity Reddit, so you can always give some feedback there. You can also follow me on twitter at: [@wpg_denny](https://twitter.com/wpg_denny). Until Next time!
