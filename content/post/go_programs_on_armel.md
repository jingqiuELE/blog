+++
date = "2017-05-01T07:32:53+08:00"
title = "Cross compile go programs to armel platforms"
draft = false

+++
### A recap
Recently I have tried to cross-compile a Go program on a host of armhf architecture, to a target architecture of armel.  
Armel doesn't have "vfp" or "fp" features enabled. And Go toolchain is expected to generate
binaries with soft float emulations for _GOARM=5_.
But the result is not exactly what I expected.
The whole history can be found here:  
https://github.com/golang/go/issues/20127

### My doubt
As it is shown is the discussion, GOARM wouldn't be respected if 'go build' is called on armhf machine.
I think it has something to do with the fact that go keeps a single directory "linux_arm" in GOPATH for all ARM platforms.
When cross compiling for armel on armhf machine, if go finds a runtime in the directory, the _runtime.a_ there will be used as a cache.
Unfortunately, go's prebuilt toolchain _go1.8.1.linux-armv6l.tar.gz_ comes with a runtime.a for armhf in "linux_arm" directory.
As a result, if one is cross compiling for armel using this toolchain, go will by default use the incorrect _"runtime.a"_.  

Is this the correct behavior? At lease it is not consistent with the result if crossing compile for armel on x86: in this case, 'go build'
will produce the correct binaries without any additional parameters.  
With additional parameters _"-i -installsuffix"_, go will look at a specified location for cached build and produce the correct binary.
But I tend to consider it as a workaround, not a fix.

