# Build Gimp from source (Draft)
A documentation draft for a tutorial how to build gimp from source under Linux for those who like to read twice what to do and when and where before fiddling around.

Based on: https://www.gimp.org/source/howtos/gimp-git-build.html which was originally published at:
http://www.chromecode.com/2009/12/best-way-to-keep-up-with-gimp-from-git_26.html

Some words in the beginning
---------------------------

*(Skip this chapter if you are in a hurry)*

To quote the original author of the linked sources Martin Nordholts "The more people that use the latest GIMP code from git the better.", I write this draft in the purpose that the development of Gimp will be fasten up and better supported by testing when more middle experienced users or developers from other corners of the universe build gimp from source and report back.

Please make sure that your try to follow this steps is at least half overlapping your skill level since it only helps you and the developers of Gimp using the latest dev version if the outcome of it equals the effort to install it.

I assume that you use a terminal on a regular basis and that you already know what an enviroment or global variable in shell script is (links?) and what the export command is doing.

I also assume that you already have made some attempts to build other software from source or have some experience in make & make install procedures (for example ffmpeg was such case for a long time)...

I also would like to mention that I wrote this only because I felt some kind of confused in parts of the original documentation, and with the awesome and nice help of the Gimp developers in the #gimp IRC channel, which are actually very busy to develop such a great application suite like Gimp, I was able to clarify this parts. This report is a mirror of this clarifications.

So lets start with a step by step draft, which we will split later into peaces with explanations:

Preparation
-----------

We finally will have 2 folders to maintain by Nordholts approach (Use the names you want, this is an example): 

 1. `gimp`
   + The automatically created sub folder from the cloning of the git repository
   + Will be created automatically while cloning
 2. `install`
   + A second sub folder for the later installation from the build
   + You can name it like you want

NOTE: I personally recommend to seperate the whole gimp development version folder structure from the rest of the user folder. But you can choose what you want. You only need to fit the rest of the tutorial to your needs. I'd even put this grouping folder inside another top folder where I have all my custom builds and code snippets inside (mostly at `bin` or `share`). But let's keep it with the one grouping folder in user folder for now:

Following my attempt both would best be put in a grouping folder like `gimp-dev` inside your user folder to seperate it from your possible original Gimp release .gimp-2.8 config folder (Linux) and the rest of your user folder. You can achieve this in one single terminal command. The first sub folder doesn't need to be created manually. Let's call the second `install`. 

    mkdir -p ~/gimp-dev/install
    
Now we clone Gimp development branch from git into the ~/gimp-dev folder, which will create the mentioned first sub folder named `gimp` inside `~/gimp-dev` (NOTE: this takes a while, it is a huge repo!):

    cd ~/gimp-dev && git clone git://git.gnome.org/gimp
    
In the meantime we can prepare our configuration for the later autogen and make install process in another terminal. Nordholts prefers to use autoconf and its sidecar file called `config.site`, which does not need to confuse you, like it did with me the first time. All you got to do (regarding our folder example from above) is to put the following (code block below) into this file and place it under /home/yourusername/gimp-dev/build/share/config.site (based on our example folder structure):

    # content of /home/yourusername/gimp-dev/build/share/config.site
    PREFIX=/home/yourusername/gimp-dev/build
    export PATH="$PREFIX/bin:$PATH"
    export PKG_CONFIG_PATH="$PREFIX/lib/pkgconfig:$PKG_CONFIG_PATH"
    export LD_LIBRARY_PATH="$PREFIX/lib:$LD_LIBRARY_PATH"
    export ACLOCAL_FLAGS="-I $PREFIX/share/aclocal $ACLOCAL_FLAGS"
    
Or you can simply use this terminal command to do this in one step in second terminal:

    printf 'PREFIX=$HOME/gimp-dev/build\nexport PATH="$PREFIX/bin:$PATH"\nexport PKG_CONFIG_PATH="$PREFIX/lib/pkgconfig:$PKG_CONFIG_PATH"\nexport LD_LIBRARY_PATH="$PREFIX/lib:$LD_LIBRARY_PATH"\nexport ACLOCAL_FLAGS="-I $PREFIX/share/aclocal $ACLOCAL_FLAGS"' >> $HOME/gimp-dev/build/share/config.site

Now let's hope Gimp is finally pulled from git in our first terminal (pulled 100%). Now we can close our second terminal and go back to our first terminal and navigate into the git clone folder `gimp` to start building from source:

    cd gimp
    ./autogen.sh --prefix=$PREFIX
    make
    make install
    
Important note!: Read the output carefully for missing dependencies.
