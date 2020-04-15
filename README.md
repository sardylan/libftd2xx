# libftd2xx

This repository creates a deb package with libftd2xx precompiled binaries

To generate DEB package, run:

```bash
mkdir build
cd build
cmake ..
make package
```

Package will be available at `build/libftd2xx-<version>-<arch>.deb`
