# Clone the repository with submodules

```shell
$ git clone --recurse-submodules https://github.com/unionsep/unionsep.dev.git

$ cd unionsep.github.io
```

### Initialize Git submodules

If you have already cloned the repository without `--recurse-submodules`, run:

```shell
$ git submodule update --init --recursive
```

# Install Hugo Extended

```shell
$ wget https://github.com/gohugoio/hugo/releases/download/v0.161.1/hugo_extended_0.161.1_linux-amd64.deb
$ sudo apt install ./hugo_extended_0.161.1_linux-amd64.deb
```

# Run the local development server

```shell
$ hugo server
```
