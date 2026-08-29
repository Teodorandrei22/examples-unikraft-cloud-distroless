# Python HTTP Server (Distroless)

This is a simple HTTP server written in Python, using a [distroless](https://github.com/GoogleContainerTools/distroless) base image (`gcr.io/distroless/python3-debian12`) instead of a `scratch`-based image.

This example is a variant of [`httpserver-python3.12`](../httpserver-python3.12).

## Note on Python version

This distroless variant uses `gcr.io/distroless/python3-debian12`,
which currently ships Python 3.11.2. The original `httpserver-python3.12`
example achieves Python 3.12 by manually copying the interpreter binary
and required shared libraries from the official `python:3.12` image into
a `scratch` base — a more fragile but version-exact approach.

At the time of writing, Google's distroless project has not yet published
an official Python 3.12 base image (see
[GoogleContainerTools/distroless#1703](https://github.com/GoogleContainerTools/distroless/issues/1703)).
Since `server.py` only uses standard library features compatible with
both versions, the application behaves identically regardless of the
minor version difference.

To run this example, follow these steps:

1. Install the CLI.
   Use the [unikraft CLI](https://unikraft.com/docs/cli/unikraft) or the legacy [kraft CLI](https://unikraft.org/docs/cli/install).
   You need a [BuildKit](https://github.com/moby/buildkit) builder. The easiest way to get one is via [Docker](https://docs.docker.com/engine/install/).

2. Clone the [`examples` repository](https://github.com/unikraft-cloud/examples) and `cd` into the `examples/httpserver-python3.12-distroless/` directory:

```bash
   git clone https://github.com/unikraft-cloud/examples
   cd examples/httpserver-python3.12-distroless/
```

Make sure to log into Unikraft Cloud and pick a [metro](https://unikraft.com/docs/platform/metros) close to you.
This guide uses `fra` (Frankfurt, 🇩🇪):

**Using the unikraft CLI (Recommended)**
```bash title="unikraft"
unikraft login
```

or

**Using the legacy kraft CLI**
```bash title="kraft"
# Set Unikraft Cloud access token
export UKC_TOKEN=token
# Set metro to Frankfurt, DE
export UKC_METRO=fra
```

When done, invoke the following command to deploy this app on Unikraft Cloud:

**Using the unikraft CLI (Recommended)**
```bash title="unikraft"
unikraft build . --output <my-org>/httpserver-python3.12-distroless:latest
unikraft run --metro fra \
  -m 512M \
  -p 443:8080/tls+http \
  --scale-to-zero policy=on,cooldown-time=1000 \
  --image <my-org>/httpserver-python3.12-distroless:latest
```

or

**Using the legacy kraft CLI**
```bash title="kraft"
kraft cloud deploy \
  -M 512Mi \
  -p 443:8080/tls+http \
  --scale-to-zero on \
  --scale-to-zero-cooldown 1s \
  .
```

Use `curl` to query the instance:

```bash
curl https://<your-instance-fqdn>
```

```text
Hello, World!
```

When done, remove the instance:

**Using the unikraft CLI (Recommended)**
```bash title="unikraft"
unikraft instances remove <instance-name>
```

or

**Using the legacy kraft CLI**
```bash title="kraft"
kraft cloud instance remove <instance-name>
```

## Customize your app

* `server.py`: the actual Python HTTP server implementation
* `Kraftfile`: the Unikraft Cloud specification
* `Dockerfile`: the Docker-specified app filesystem

Lines in the `Dockerfile` have the following roles:

* `FROM gcr.io/distroless/python3-debian12`: Build the runtime filesystem from Google's [distroless](https://github.com/GoogleContainerTools/distroless) Python 3 base image, which already contains a minimal Python interpreter and its required system libraries.

* `COPY ./server.py /src/server.py`: Copy the server implementation file into the app filesystem.

## Learn more

- [Unikraft Cloud's Documentation](https://unikraft.cloud/docs/)
- [Distroless Images](https://github.com/GoogleContainerTools/distroless)
