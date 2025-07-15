### 1. Memory Leaks
#### 1.1 Detecting Memory Leaks with AddressSanitizer
`valgrind` is another useful tool to detect memory leaks from `malloc` and `free`
```
valgrind <executable>
```

#### 1.2 Detecting Memory Leaks with AddressSanitizer
There’s also sanitizer tools built into Clang (and now gcc), but you have to recompile
- Add the -Db_sanitize=address flag to Meson
```
rm -rf build
meson setup build -Db_sanitize=address
meson compile -C build
```
