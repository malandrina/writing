# Using Apache Bench to build intuition about processing concurrent HTTP requests

_31 October 2023_

[Apache Bench](https://httpd.apache.org/docs/2.4/programs/ab.html) is what you reach for when you want to load test a web application: it's an easy-to-use command-line tool for benchmarking the performance of HTTP servers. It's also useful for quickly building intuition and validating assumptions about how your server handles concurrent requests.

I'm learning Rust and I recently adapted the multithreaded web server project in the final chapter of _The Rust Programming Language_ ([link](https://doc.rust-lang.org/book/ch20-00-final-project-a-web-server.html)) to write data to an in-memory key-value store. I wanted to see how the server behaved on receipt of multiple concurrent requests.

My questions were:

- How might an incoming request be uniquely identified?
- Are requests processed in the order in which they are received?
- Is the data being updated when I think it is being updated?
- Are responses returned in the order in which requests are processed?

Here's how I used Apache Bench to answer these questions:

### First: Add some logging

Log the beginning of handling a request:

```rust
// -- snip --
for stream in listener.incoming() {
    let stream = stream.unwrap();
    let peer_addr = &stream.peer_addr().unwrap();
    // Uniquely identify Apache Bench request by peer port
    let peer_port = peer_addr.port();
    let mut stream_entries = entries.clone();

    pool.execute(move || {
        println!("Handling request for peer {peer_port}");
        let controller = RequestController::build(stream).unwrap();
        controller.handle_request(&mut stream_entries)
    });
}
```

Log data access:

```rust
-- snip --
println!("Writing data for peer {peer_port}");
data_store.lock().unwrap().insert(key, value);
```

Log the end of request processing:

```rust
-- snip --
println!("Returning reponse to peer {peer_port}");
stream.write_all(response.as_bytes()).unwrap();
```

### Next: Execute some concurrent requests using Apache Bench

Here's the command I ran to execute four concurrent requests to my server endpoint:

```
ab -n 4 -c 4 http://127.0.0.1/set?foo=bar
```

Note: ab doesn't know what `localhost` is so always use `127.0.0.1`.

### Finally: Review server logs

Server log output:

```
Handling request for peer 52279
Writing data for peer 52279
Returning reponse to peer 52279
Handling request for peer 52280
Writing data for peer 52280
Returning reponse to peer 52280
Handling request for peer 52281
Handling request for peer 52282
Writing data for peer 52282
Returning reponse to peer 52282
Writing data for peer 52281
Returning reponse to peer 52281
```

Reviewing the logs tells me that updates to data encoded in concurrent requests may not be handled in the order in which the requests enter the system: Note that "Writing data for peer 52282" appears before "Writing data for peer 52281" in the logs.
