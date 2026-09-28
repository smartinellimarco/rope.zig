# rope.zig

A rope over utf-8 chunks. Positions are characters, every node counts both
characters and bytes, and each chunk keeps a gap where the last edit landed so
typing forwards moves nothing.

## Install

    zig fetch --save git+https://github.com/smartinellimarco/rope.zig

Then in `build.zig`:

```zig
const rope = b.dependency("rope", .{ .target = target, .optimize = optimize });
exe.root_module.addImport("rope", rope.module("rope"));
```

## Use

```zig
const rope = @import("rope");

var text: rope.Text = try .init(gpa);
defer text.deinit();

try text.insert(0, "hello world");
text.delete(5, 6);
try text.insert(5, " there");

const all = try text.toBytes(gpa);   // "hello there"
defer gpa.free(all);

const part = try text.slice(gpa, 6, 11);   // "there"
defer gpa.free(part);
```

Positions are character offsets, so `delete(5, 6)` erases six characters, not
six bytes. Reading never moves a gap: `slice` takes a const pointer and leaves
the chunk the last edit touched where it was.

## Test

    zig build test

Zig 0.16.0.
