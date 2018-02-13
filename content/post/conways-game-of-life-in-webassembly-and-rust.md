---
title: "Conway's Game of Life in WebAssembly"
date: 2018-02-12T06:53:28-08:00
draft: false
---

Recently, after learning that the Rust compiler tool-chain, now supported [WebAssembly](http://webassembly.org/) as a target, I got interested in experimenting with WebAssembly. In order to explore how Rust could be used to write code that compiled to WebAssembly, I decided to implement [Conway's Game of Life](https://en.wikipedia.org/wiki/Conway's_Game_of_Life). My goal was to have as much of the _grid_ traversing logic done in Rust as possible.

I started out by reading through a few chapters of the second edition of the [Rust book](https://doc.rust-lang.org/stable/book/second-edition/). Next, I set up my [development environment](https://www.hellorust.com/setup/wasm-target/) and started working my way through the examples on the [_Hello Rust_](https://www.hellorust.com/demos/) page. After come across an example of how WebAssembly could be used to [manipulate raw pixels on a Canvas] (https://www.hellorust.com/demos/feistel/index.html), I decided to implement the game in a similar way. I started out with code that looked that had a few `unsafe` blocks, and few others that did C style memory allocation. Soon enough, I ended up with code that had multiple `unsafe` blocks and several plain old C style pointer manipulation. The result looked somewhat like this:

```
#[no_mangle]
pub extern "C" fn next_generation(pointer: *mut u8, width: usize, height: usize) -> *mut c_void {
    // pixels are stored in RGBA, so each pixel is 4 bytes
    let byte_size = width * height * 4;
    let buf = unsafe { slice::from_raw_parts_mut(pointer, byte_size) };

    let next_gen_ptr = alloc(byte_size);
    let next_gen_ptr = next_gen_ptr as *mut u8;
    let new_buf = unsafe {slice:: from_raw_parts_mut(next_gen_ptr,byte_size)};

    for i in 0..byte_size {
        let pos = i / 4;
        let x = pos % width;
        let y = pos / width;
        if x < width && y < height {
            let neighbor_count =  count_neighbors(buf, width, x , y);
            let x1 = x as i32;
            let y1 = y as i32;
            let w1 = width as i32;
            let is_alive = is_filled(buf, w1, x1, y1);
            if is_alive && neighbor_count < 2 {
                clear_cell(new_buf, width, x, y);
            } else if is_alive && neighbor_count == 2 || neighbor_count == 3 {
                set_pixel(new_buf, width, x, y );
            } else if is_alive && neighbor_count > 3 {
                clear_cell(new_buf, width, x, y);
            } else if !is_alive && neighbor_count == 3 {
                set_pixel(new_buf, width, x, y );
            } else {
                clear_cell(new_buf, width, x, y);
            }
        }
    }
    next_gen_ptr as *mut c_void
}
```

The `alloc` function referred above was verbatim copied from a _Hello Rust_ [example](https://www.hellorust.com/demos/canvas/index.html). 

 While the code successfully compiled, running it on the browser, however, resulted a drastic performance drop of the entire browser. Turns out, this was due to a memory leak that sprung out due to the `unsafe` behavior in my Rust code. Now, rather than attempting to debug the `unsafe` code, that was not particularly happy about, I decided to re-write the game, this time using less `unsafe` behavior and avoiding C style pointer manipulation altogether. Fortunately, to my aid, I came across a well-written [blog post](https://aochagavia.github.io/blog/rocket---a-rust-game-running-on-wasm/) that addressed the caveats involved in writing a game in Rust. Based on that, I started out by first modeling the game _grid_, and building other functionality around it. Here is a snippet that shows how my game _grid_ looked like:

```
 pub struct Grid {
    state: Vec<Vec<bool>>, //State is a 2d vector of boolean representing the grid
    pub size: Size,
}
 ```

As compared to the original version that passed memory blocks between  Rust and Javascript, modeling the code as a `Vec` of `Vec`s simplified the implementation to a great extent. Next, rather than returning the computed values for the next generation from the Rust code to the Javascript code, I decided to maintain the game state on the Rust side and call back exposed rendering methods fromm Rust. In comparison to the original approach that used raw pixel manipulation on the Rust side, this provided a much simpler way of performing the `canvas` manipulation.

In the end, after working my way through a few minor implementation bugs, I had the game of life's cellular automation running. [Here](https://github.com/aishraj/game-of-life-web-assembly) is a link source code.