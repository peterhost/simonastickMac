## Sim on a Stick, Macos version

### Mysql install

####Download

    curl -O http://mysql.he.net/Downloads/MySQL-5.1/mysql-5.1.57.tar.gz

    curl -O http://mysql.he.net/Downloads/MySQL-5.5/mysql-5.5.12.tar.gz
    tar xzvf mysql-5.5.12.tar.gz
    cd mysql-5.5.12

####Configure & build

thx to [this post](http://geert.vanderkelen.org/post/200/ "Building MySQL universal binaries using MacOS X 10.6 (Snow Leopard)") for clear indications of
how to build a truly universal build of Mysql for Macos, and
[this hack](http://stackoverflow.com/questions/1365211/error-in-xcode-project-ld-library-not-found-for-lcrt1-10-6-o/1626958#1626958
"Error in xcode project: ld: library not found for -lcrt1.10.6.o") to make it work on my mac (OSX.6 with XCODE 3.2.3 64bits)

>####Most complete Universal Binary
>  
>First, here is away to build MySQL so it runs on MacOS X 10.5
>Intel/PowerPC and 10.6 32 or 64-bit.  Here is the source of the build
>script names build.sh. Executed it while located inside the source
>directory of MySQL.

    #!/bin/bash

    SDK="-isysroot /Developer/SDKs/MacOSX10.5.sdk"
    SDKLIB="-Wl,-syslibroot,/Developer/SDKs/MacOSX10.5.sdk"
    export MACOSX_DEPLOYMENT_TARGET="10.5"
    PREFIX=/opt/mysql/mysql-5.1.42-universal-macosx-10.5

    ARCH="-arch i386 -arch x86_64 -arch ppc"

    export CFLAGS="-O2 -fPIC $ARCH $SDK -mmacosx-version-min=10.5"
    export CXXFLAGS="-O2 -fPIC $ARCH $SDK"
    export LDFLAGS="$ARCH $SDKLIB -mmacosx-version-min=10.5"

    CC="/usr/bin/gcc-4.2"
    CXX="/usr/bin/g++-4.2"
    OBJC="/usr/bin/gcc-4.2"

    INSTALL="/usr/bin/install -c"

    ./configure --prefix=$PREFIX \
    --disable-dependency-tracking \
    --mandir=$PREFIX/share/man --infodir=$PREFIX/share/info \
    --localstatedir=$PREFIX/var/ --libdir=$PREFIX/lib \
    --bindir=$PREFIX/bin --libexecdir=$PREFIX/bin \
    --includedir=$PREFIX/include \
    --datadir=$PREFIX/share/ --sysconfdir=$PREFIX/etc \
    --with-extra-charsets=complex \
    --with-mysqld-user=mysql \
    --without-docs \
    --with-plugins=all \
    --enable-thread-safe-client --without-embedded-server \
    --with-pic --with-libedit

    if [ $? -eq 0 ]; then
        make clean
        time make -j 2
    fi

>####Here is what file shows for the mysqld binary:
>  
>Black:mysql-5.1.42 geert$ file sql/mysqld  
>sql/mysqld: Mach-O universal binary with 3 architectures  
>sql/mysqld (for architecture i386): Mach-O executable i386  
>sql/mysqld (for architecture x86_64): Mach-O 64-bit executable x86_64  
>sql/mysqld (for architecture ppc7400): Mach-O executable ppc  
>These binaries were tested and work on MacOS X 10.6 Intel and MacOS X 10.5 PowerPC.  

###Setup Mysql in a different place than the place it was built for

    ./mysql_install_db  --basedir=/Users/plhoste/Tmp/simonastick/mysql --datadir=/Users/plhoste/Tmp/simonastick/mysql/data

    Installing MySQL system tables...
    OK
    Filling help tables...
    OK

    To start mysqld at boot time you have to copy
    support-files/mysql.server to the right place for your system

    PLEASE REMEMBER TO SET A PASSWORD FOR THE MySQL root USER !
    To do so, start the server, then issue the following commands:

    /Users/plhoste/Tmp/simonastick/mysql/bin/mysqladmin -u root password 'new-password'
    /Users/plhoste/Tmp/simonastick/mysql/bin/mysqladmin -u root -h Palmerston.local password 'new-password'

    Alternatively you can run:
    /Users/plhoste/Tmp/simonastick/mysql/bin/mysql_secure_installation

    which will also give you the option of removing the test
    databases and anonymous user created by default.  This is
    strongly recommended for production servers.

    See the manual for more instructions.

    You can start the MySQL daemon with:
    cd /Users/plhoste/Tmp/simonastick/mysql ; /Users/plhoste/Tmp/simonastick/mysql/bin/mysqld_safe &

    You can test the MySQL daemon with mysql-test-run.pl
    cd /Users/plhoste/Tmp/simonastick/mysql/mysql-test ; perl mysql-test-run.pl

    Please report any problems with the /Users/plhoste/Tmp/simonastick/mysql/scripts/mysqlbug script!

###Install your SimonaStick

First, you should **format your USB key** to a native MacOS format. FAT
might work for SimonaStickMac (not tested) but I doubt it because FAT
has no file level permission settings whatsoever, and we're talking
macos, mono, mysql,... here. Two options :

*  Either lauch "Disk Utility" (/Applications/Utilities), locate your
   Stick in the left-hand pannel, select it, then click Erase on the
   top right-hand area, and chose :

    Format: Mac OS Extended (Case-sensitive, Journaled)

    Name  : the name of your Stick

   and click the "Erase" button

*  Or use a NTFS driver for your mac if you wish to be able to use the
   Stick on windows too

