# Rustpad

**Rustpad** is an _efficient_ and _minimal_ open-source collaborative text
editor based on the operational transformation (OT) algorithm. Similar to Google
Docs, Rustpad allows users to collaborate in real time while writing code in
their browser.

<p align="center">
<a href="https://rustpad.io/">
<img src="https://images.placeholders.dev/?width=600&height=300" width="600"><br>
<strong>Rustpad.io</strong>
</a>
</p>

The server is written in Rust using [Warp](https://github.com/seanmonstar/warp)
and the
[operational-transform](https://github.com/spebern/operational-transform-rs)
library, which is a port of
[ot.js](https://github.com/Operational-Transformation/ot.js). We compile some of
this logic to WebAssembly with
[wasm-bindgen](https://github.com/rustwasm/wasm-bindgen) for use in the browser.
The frontend is written in TypeScript using [React](https://reactjs.org/) and
interfaces with [Monaco](https://github.com/microsoft/monaco-editor), the text
editor that powers VS Code.

Architecturally, client-side code communicates via WebSocket with a central
server that stores in-memory data structures. This makes the editor very fast,
allows us to avoid provisioning a database, and makes testing our code much
easier. The tradeoff is that user documents are transient and lost between
server restarts.

## Development setup

To run this application, you need to install Rust, `wasm-pack`, and Node.js.
Then, build the WebAssembly portion of the app:

```
wasm-pack build rustpad-wasm
```

When that is complete, you can install dependencies for the frontend React
application with:

```
npm install
```

Finally, compile and run the backend web server:

```
cargo run
```

While the backend is running, open another shell and run the following command
to start the frontend portion.

```
npm start
```

This command will open a browser window to `http://localhost:3000`, with hot
reloading on changes.

## Testing

To run unit tests and integration tests for the server, use the standard
`cargo test` command. For the WebAssembly component, you can run tests in a
headless browser with

```
wasm-pack test rustpad-wasm --chrome --headless
```

## Deployment

Rustpad is distributed as a single ~10 MB Docker image, which is built from the
`Dockerfile` in this repository. GitHub Actions automatically builds a new image
on each push to the `main` branch.

We deploy a public instance of this image using
[DigitalOcean App Platform](https://www.digitalocean.com/products/app-platform/).
