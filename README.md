go-render: A verbose recursive Go type-to-string conversion library.
====================================================================

Forked from https://github.com/luci/go-render

This fork adds an "AsCode" function to do everything `render.Render` previously did, but clean up output to be re-usable as code. The direct `render.AsCode` string output can be pasted as a new struct without modification and the go linter will be happy.

## Overview

The *render* package implements a more verbose form of the standard Go string
formatter, `fmt.Sprintf("%#v", value)`, adding:
  - Pointer recursion. Normally, Go stops at the first pointer and prints its
    address. The *render* package will recurse and continue to render pointer
    values.
  - Recursion loop detection. Recursion is nice, but if a recursion path detects
    a loop, *render* will note this and move on.
  - Custom type name rendering.
  - Deterministic key sorting for `string`- and `int`-keyed maps.
  - Testing!

Call `render.Render` and pass it an `interface{}`.

For example:

```Go
type customType int
type testStruct struct {
        S string
        V *map[string]int
        I interface{}
}

a := testStruct{
        S: "hello",
        V: &map[string]int{"foo": 0, "bar": 1},
        I: customType(42),
}

fmt.Println("Render test:")
fmt.Printf("fmt.Printf:    %#v\n", a)))
fmt.Printf("render.Render: %s\n", Render(a))
```

Yields:
```
fmt.Printf:    render.testStruct{S:"hello", V:(*map[string]int)(0x600dd065), I:42}
render.Render: render.testStruct{S:"hello", V:(*map[string]int){"bar":1, "foo":0}, I:render.customType(42)}
```

