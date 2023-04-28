Summary from tests with this:

## General remarks

When researching low-latency video streams, "the internet" says that WebRTC is the protocol of choice for latency below 1 s. Other protocols like LL-HLS work on video chunks that introduce some inherent latency to the transmission process. Also, the WebRTC stack seems to do a lot of things to optimize quality and latency.

As a downside, the stack is pretty deep and opaque. It tries to establish a direct peer-to-peer connection using UDP or TCP between the browser and the backend that is completely independent of the connection through which the rest of the page communicates. That means it does not work through SSH tunnels, web sockets and such. It is not possible to inject a connection into the WebRTC stack, as far as I could find out. A TURN server such as coturn would have to be deployed as a relay and injected as a configuration option to establish a connection between peers that can't see each other directly.

That being said, in many scenarios the web component can run on the same host or network as the backend component, so WebRTC could be a viable option.

## Performance

It is so far the fastest method to bring dynamic image plots into the browser.

The webcam example "server.py" can reach about 2 fps at 4096x4096 with random data. That takes a bit more than 10 MBit/s bandwidth. With such "worst case" data the quality is not great. With data that changes less it is much better. At lower resolution it can easily reach 60 fps at a few percent system load.

## Viability for Jupyter notebooks

[`aiortc`](https://aiortc.readthedocs.io/en/latest/) seems to work very well. However, it only includes examples for plain Python, HTML and JavaScript, so quite a bit of development would be required to build Jupyter widgets from this.

The [`ipywebrtc`](https://ipywebrtc.readthedocs.io/en/latest/index.html) project seems to be short on manpower. It also doesn't seem to introduce a framework or examples to stream content from the backend to the frontend, only acting as a source or sink locally.

The [`open3d`](https://github.com/isl-org/Open3D/) project includes a [web and Jupyter visualization over WebRTC](http://www.open3d.org/docs/release/tutorial/visualization/web_visualizer.html). The stand-alone web version works very well and shows that this can be a viable option for dynamic visualizations. The Jupyter example [was broken at the time of writing](https://github.com/isl-org/Open3D/issues/6030).

## Possible next steps

The combination of WebRTC with Jupyter seems brittle. It might be because Jupyter/ipywidgets/... introduce backwards-incompatible changes too often. Maybe the public API is not sufficient for such an application so that internals are used? In the past we also suffered from other bugs in that ecosystem such as https://github.com/computationalmodelling/nbval/issues/194

The Open3D implementation seems closest to what we want to accomplish. However, Open3D would be a very large dependency and it seems focused on 3D visualization. To make use of it one would have to replace the visualization part and implement different interactions. If the Jupyter example is stable enough one could look at the code structure and see if parts can be spun out or adapted.

In any case, one can expect some maintenance cost to keep such a component working in different contexts while the Jupyter ecosystem keeps changing.

