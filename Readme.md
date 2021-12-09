# Embed v8 on Mac silicon

Note: Might complain about XCode, so make sure it's installed and pointed to the xcode instead of the command line tools.

1) XCode

    ```shell
    # Command Line Tools. Although, v8 seems to complain if not using xcode
    # xcode-select --install
    # sudo xcode-select --switch /Library/Developer/CommandLineTools # Enable command line tools

    sudo xcode-select -s /Applications/Xcode.app/Contents/Developer
    ```

2) Install depot tools

    ```shell
    mkdir -p $HOME/v8
    cd $HOME/v8
    git clone https://chromium.googlesource.com/chromium/tools/depot_tools.git

    # Add depot_tools to the front of your PATH (you will probably want to put this in your ~/.bashrc or ~/.zshrc). Assuming you cloned depot_tools to /path/to/depot_tools:
    # .zprofile
    export PATH=$HOME/v8/depot_tools:$PATH
    export V8_HOME=$HOME/v8/v8
    export V8_RELEASE=arm64.release
    ```

3) Fetch v8 source
   
    ```shell
    fetch v8
    cd $HOME/v8/v8
    gclient sync
    ```

4) Build static library

    ```shell
    tools/dev/v8gen.py $V8_RELEASE.sample
    ninja -C out.gn/$V8_RELEASE.sample v8_monolith
    ```

5) Compile

    In this project...

    ```shell
    cp $V8_HOME/out.gn/$V8_RELEASE.sample/icudtl.dat .
    g++ -I$V8_HOME/include -I. -Iinclude hello-world.cc -o hello_world -lv8_monolith -L$V8_HOME/out.gn/$V8_RELEASE.sample/obj/ -pthread -std=c++14 -DV8_COMPRESS_POINTERS
    ```

# References

* https://v8.dev/docs/embed
* https://smileorigin.site/web/v8/
* https://github.com/nodejs/node-gyp/issues/569
