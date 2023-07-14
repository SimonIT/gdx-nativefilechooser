# gdx-nativefilechooser

Choose files with [LibGDX](https://libgdx.badlogicgames.com/), natively.

This library allows you to asynchronously browse files with the _native_ file chooser available on the platform.

## Setup

Add the pretty **bold** parts into your _build.gradle_ file:

<pre>
    project(":desktop") {
        
        ...
        
        dependencies {
            compile project(":core")
            ...
            <b>compile "games.spooky.gdx:gdx-nativefilechooser-desktop:2.2.0"</b>
            or
            <b>compile "games.spooky.gdx:gdx-nativefilechooser-desktop-lwjgl:2.2.0"</b>
        }
    }
    
    project(":android") {
        
        ...
        
        dependencies {
            compile project(":core")
            ...
            <b>compile "games.spooky.gdx:gdx-nativefilechooser-android:2.2.0"</b>
        }
    }
    
    project(":core") {
        
        ...
        
        dependencies {
            ...
            <b>compile "games.spooky.gdx:gdx-nativefilechooser:2.2.0"</b>
        }
    }
</pre>

## Usage

### Add a NativeFileChooser

A more detailed version of this can be found in [libGDX documentation](https://github.com/libgdx/libgdx/wiki/Interfacing-with-platform-specific-code).

It is assumed here that your project follows the basic structure of a libGDX project.
You should then have a _core_ project and as many platform-specific projects as intended.

#### Core

In the central class of your _core_ project, the one that implements `ApplicationListener`, add a property of type `NativeFileChooser` like so:
    
    public class MyAwesomeGame implements ApplicationListener {
        NativeFileChooser fileChooser;
        public MyAwesomeGame(NativeFileChooser fileChooser) {
            super();
            this.fileChooser = fileChooser;
        }
        ...
    }

#### Platform-specific

In the launcher class of your platform-specific project, pass the implementation of `NativeFileChooser` for this platform to the core class constructor.

On Android, it gives:

    public class MyAwesomeGameAndroid extends AndroidApplication {
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            ...
            initialize(new MyAwesomeGame(new AndroidFileChooser(this)), new AndroidApplicationConfiguration());
        }
    }

### Use it

* Create a (mandatory) `NativeFileChooserConfiguration` object, stuff it with configuration details if you like.
* Create a (also mandatory) `NativeFileChooserCallback` object ready to react to anything that may happen.
* Call the `chooseFile` method from your `NativeFileChooser`, giving the two objects above as arguments.

#### Example - go get me some oggs

    // Configure
    NativeFileChooserConfiguration conf = new NativeFileChooserConfiguration();
    
    // Starting from user's dir
    conf.directory = Gdx.files.absolute(System.getProperty("user.home"));
    
    // Filter out all files which do not have the .ogg extension and are not of an audio MIME type - belt and braces
    conf.mimeFilter = "audio/*";
    conf.nameFilter = new FilenameFilter() {
        @Override
        public boolean accept(File dir, String name) {
            return name.endsWith("ogg");
        }
    };
    
    // Add a nice title
    conf.title = "Choose audio file";
    
    fileChooser.chooseFile(conf, new NativeFileChooserCallback() {
        @Override
        public void onFileChosen(FileHandle file) {
            // Do stuff with file, yay!
        }
        
        @Override
        public void onCancellation() {
            // Warn user how rude it can be to cancel developer's effort
        }
        
        @Override
        public void onError(Exception exception) {
            // Handle error (hint: use exception type)
        }
    });

#### Example - save the day

    NativeFileChooserConfiguration conf = new NativeFileChooserConfiguration();
    
    // Filter out all files which do not have the .save extension
    conf.nameFilter = new FilenameFilter() {
        @Override
        public boolean accept(File dir, String name) {
            return name.endsWith("save");
        }
    };

    // Use this if you wish to start a "Save file" chooser
    conf.intent = NativeFileChooserIntent.SAVE;
    
    // Add a nice title
    conf.title = "Save game";
    
    fileChooser.chooseFile(conf, new NativeFileChooserCallback() {
        @Override
        public void onFileChosen(FileHandle file) {
            // Write data to save file
        }
        
        @Override
        public void onCancellation() {
            // Do nothing
        }
        
        @Override
        public void onError(Exception exception) {
            // Handle error
        }
    });

## Platform support

|                 | Minimum libgdx version | Open file | Save file | Filter MIME | Filter name |
|-----------------|------------------------|-----------|-----------|-------------|-------------|
| Desktop (AWT)   | 1.8.0                  | ✓         | ✓         | ✓           | ✓           |
| Desktop (Swing) | 1.8.0                  | ✓         | ✓         | ✓           | ✓           |
| Desktop (LWJGL) | 1.11.0                 | ✓         | ✓         | ✓           | ✓           |
| Android         | 1.8.0                  | ✓         |           | ✓           |             |