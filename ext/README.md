# fake-openssh-portable

# build

```bash
autreoconf
./configure
cmake -B build -DCMAKE_TOOLCHAIN_FILE=[path_to_vcpkg]/scripts/buildsystems/vcpkg.cmake
cmake --build build --target sshd
```