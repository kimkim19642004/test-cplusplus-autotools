<!--
Document File Format:
	- GFM (GitHub Flavored Markdown)
References:
	- [Mastering Markdown](https://guides.github.com/features/mastering-markdown/)
	- [Markdown Editor](https://jbt.github.io/markdown-editor/)
	- [A template to make good README.md](https://gist.github.com/PurpleBooth/109311bb0361f32d87a2)
-->

# test-cplusplus

This is a 'OpenCV with GTK+ 3' application that load and display image.

**Linking this software to 'OpenCV with Qt' library is deprecated.**

Note:
- There are some problems in OpenCV (OpenCV with Qt) library on the Fedora repository. See [simple C++ OpenCV imshow example crashes with segmentation fault](https://stackoverflow.com/questions/41240911/simple-c-opencv-imshow-example-crashes-with-segmentation-fault).
- How to build 'OpenCV with GTK+ 3' library is here [...](#how_to_build_opencv_with_gtk3)

Some of usages of this command are shown below.

How to display help:
```
$ test-cplusplus --help
Usage: test-cplusplus [OPTION] FILE
Load and display image

OPTION:
      --disptime=msec Display time (1~2147483647)
      --single        Prevent double startup of this program
      --cvinfo        Display OpenCV build information and exit
      --msginfo       Display message catalog information and exit
      --help          Display this help and exit
      --version       Display version information and exit

URL: 
Report bugs to: https://github.com/kimkim19642004/test-cplusplus-autotools/issues
```

How to know supported image formats:
```
$ test-cplusplus --cvinfo | awk '/Media I\/O:/,/^$/'
  Media I/O: 
    ZLib:                        /lib64/libz.so (ver 1.2.11)
    JPEG:                        /lib64/libjpeg.so (ver )
    WEBP:                        /lib64/libwebp.so (ver encoder: 0x020e)
    PNG:                         /lib64/libpng.so (ver 1.6.34)
    TIFF:                        /lib64/libtiff.so (ver 42 / 4.0.9)
    JPEG 2000:                   /lib64/libjasper.so (ver 2.0.14)
    OpenEXR:                     /lib64/libImath.so /lib64/libIlmImf.so /lib64/libIex.so /lib64/libHalf.so /lib64/libIlmThread.so (ver 2.2.0)
    GDAL:                        YES (/lib64/libgdal.so)
    GDCM:                        YES (ver 2.8.4)
```

How to know supported GUI libraries:
```
$ test-cplusplus --cvinfo | awk '/GUI:/,/^$/'
  GUI: 
    GTK+:                        YES (ver 3.22.30)
      GThread :                  YES (ver 2.56.1)
      GtkGlExt:                  NO
    OpenGL support:              NO
    VTK support:                 NO
```

How to load and display image:
```
$ test-cplusplus tests/kingfisher-1905255_640.jpg 
```

![How to load and display image](https://github.com/kimkim19642004/test-cplusplus-autotools/blob/master/How_to_load_and_display_image.png "How to load and display image")

## Getting Started

These instructions will get you a copy of the project up and running on your local machine for development and testing purposes. See [Deployment](#id_deployment) for notes on how to deploy the project on a live system.

### Prerequisites

Install necessary packages for installing this software.

When the latest version of [Fedora](https://getfedora.org/) is clean installed:
```
$ sudo dnf install dnf-plugins-extras-tracer
$ sudo dnf upgrade
```
Follow instructions of the DNF command, restart the specified applications, or reboot your system, or login to your system again.

Note:
- If Plugin for DNF command [dnf-plugins-extras-tracer](http://tracer.readthedocs.io/en/latest/user-guide/) is installed on your system, the DNF command will display information about running applications that need to be restarted. This plugin is useful, but it may take time to execute. To disable this plugin, specify '--disableplugin=tracer' as the command option.

Install Development Tools:
```
$ sudo dnf groupinstall "C Development Tools and Libraries"
$ sudo dnf install autoconf-archive gettext-devel bats
$ sudo dnf install openssl-devel gtk3-devel
```

When using the development tools in Japanese:
```
$ echo $LANG
ja_JP.UTF-8

$ sudo dnf install glibc-langpack-ja man-pages-ja
```
**Install language packages that match your locale.**

<div id="how_to_build_opencv_with_gtk3"></div>

Add user 'mockbuild' as a normal user (non-root user) to build your own RPM packages:
```
$ sudo useradd mockbuild
$ sudo passwd mockbuild
```

Login as user 'mockbuild' and do the following:
```
$ cd $HOME
$ dnf download --source opencv (or download the latest SRPM pakage from 'http://rpmfind.net/linux/rpm2html/search.php?query=opencv')
$ rpm -ivh opencv-3.4.1-3.fc28.src.rpm
$ gedit rpmbuild/SPECS/opencv.spec
```

Rewrite the following parts of file 'opencv.spec':
```
(line 254: before) -DWITH_QT=ON \
(line 254: after ) -DWITH_QT=OFF \
(line 329: before) %{_libdir}/libopencv_cvv.so.%{abiver}*
(line 329: after ) #{_libdir}/libopencv_cvv.so.%{abiver}*
```

Then do the following:
```
$ rpmbuild -bb --clean rpmbuild/SPECS/opencv.spec
```
You should see a lot of error messages indicating that packages necessary for build are not installed. Login as a user(administrator) who is not user 'mockbuild', and install those packages using DNF command.

Follow instructions of the DNF command, restart the specified applications, or reboot your system, or login to your system again.

And then, Login as user 'mockbuild' and do the following again:
```
$ rpmbuild -bb --clean rpmbuild/SPECS/opencv.spec
```

If the build succeeds, there should be 13 files below under subdirectories of $HOME/rpmbuild/RPMS:
```
opencv-3.4.1-3.fc28.x86_64.rpm
opencv-contrib-3.4.1-3.fc28.x86_64.rpm
opencv-contrib-debuginfo-3.4.1-3.fc28.x86_64.rpm
opencv-core-3.4.1-3.fc28.x86_64.rpm
opencv-core-debuginfo-3.4.1-3.fc28.x86_64.rpm
opencv-debuginfo-3.4.1-3.fc28.x86_64.rpm
opencv-debugsource-3.4.1-3.fc28.x86_64.rpm
opencv-devel-3.4.1-3.fc28.x86_64.rpm
opencv-devel-docs-3.4.1-3.fc28.noarch.rpm
opencv-python3-3.4.1-3.fc28.x86_64.rpm
opencv-python3-debuginfo-3.4.1-3.fc28.x86_64.rpm
python2-opencv-3.4.1-3.fc28.x86_64.rpm
python2-opencv-debuginfo-3.4.1-3.fc28.x86_64.rpm
```
Keep a copy of these files on removable media.

Finally, login as administrator and install using DNF command:
```
$ cd $HOME/opencv-3.4.1-3.fc28.x86_64 <-- Directory where your own RPM packages are stored
$ sudo dnf install ./*
```
Note:
- Do not execute command 'sudo dnf install opencv'. When you execute the command, the RPM packages will be downloaded from the Fedora repository.

Follow instructions of the DNF command, restart the specified applications, or reboot your system, or login to your system again.

### Installing

The installation procedure of this software is as follows:
```
$ cd $HOME
$ git clone https://github.com/kimkim19642004/test-cplusplus-autotools.git
$ cd test-cplusplus-autotools
$ ./configure && make
$ make check
$ sudo make install
$ make installcheck
```
Note:
- See 'INSTALL' for more information.

<div id="id_deployment"></div>

## Deployment

See [Creating RPM packages](https://docs.fedoraproject.org/en-US/quick-docs/creating-rpm-packages/index.html).

## Versioning

We use [SemVer](http://semver.org/) for versioning. For the versions available, see [po2bats-autotools](https://github.com/kimkim19642004/test-cplusplus-autotools). 

## Authors

See 'AUTHORS' for more information.

## License

See 'COPYING' for more information.

## NEWS

This is the latest information on development environment.
See 'NEWS' for more information.
