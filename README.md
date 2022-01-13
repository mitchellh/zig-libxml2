# libxml2 Built with Zig

This repository contains Zig code for building libxml2 using Zig.
This allows other projects that use the Zig build system to easily
consume this library, cross-compile it, etc.

**Why?** Using the Zig build system makes it much easier to cross-compile
a library, even if you aren't using the Zig programming language. See
[Maintain it with Zig](https://kristoff.it/blog/maintain-it-with-zig/)
for some more information.

## Usage

While we all eagerly await the [Zig Package Manager](https://github.com/ziglang/zig/issues/943),
the recommended way to use this is via git submodules or just embedding
this into your repository.

```zig
const libxml2 = @import("path/to/libxml2.zig");

pub fn build(b: *std.build.Builder) void {
    // ...

    const lib = libxml2.create(b, target, mode. {
        // These are the minimal options to NOT depend on any other libraries.
        // If you ave these libraries, just set these to true.
        .iconv = false,
        .lzma = false,
        .zlib = false,
    });

    const exe = b.addExecutable("my-program", "src/main.zig");
    lib.link(exe);
}
```

This package does not provide any Zig-native APIs to access the underlying
C library. This is by design, the focus of this repository is only to enable
building the underlying library using the Zig build system. Therefore, to
use the library, import the headers and use the C API:

```zig
const c = @cImport({
    @cInclude("libxml/xmlreader.h");
});

// ... do stuff with `c`
```

### Other Dependencies

Some features require other libraries. In the example above, we disabled
those features. For example, if you set `.zlib = true`, then zlib must
be available.

In this scenario, you can find and use a zlib build such as
[zig-zlib](https://github.com/mattnite/zig-zlib) as normal. When that
library is also added to the project, it adds its include paths and
linking information to the build, so libxml2 can be built with zlib support.
