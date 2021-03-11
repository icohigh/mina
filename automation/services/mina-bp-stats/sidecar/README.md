# Mina Block Producer Metrics Sidecar

This is a simple sidecar that communicates with Mina nodes to ship off uptime data for analysis.

Unless you're a founding block producer, you shouldn't need to run this sidecar.

## Configuration

The sidecar takes 2 approaches to configuration, a pair of envars, or a configuration file. 

**Note**: Environment variables always take precedence, even if the config file is available and valid. 

#### Envars
- `MINA_BP_UPLOAD_URL` - The URL to upload block producer statistics to
- `MINA_NODE_URL` - The URL that the sidecar will reach out to to get statistics from

#### Config File
The mina metrics sidecar will also look at `/etc/mina-sidecar.json` for its configuration variables, and the file should look like this:

```
{
  "uploadURL": "https://your.upload.url.here?token=someToken", 
  "nodeURL": "https://your.mina.node.here:4321"
}
```

The `uploadURL` parameter should be given to you by the Mina engineers

## Running with Docker
Running in docker should be as straight forward as anything else. The examples below assume you've checked out this repo and run `docker build -t mina-sidecar .` in this folder. 

We will likely also be cutting a release to docker hub soon which will likely live at `codaprotocol/mina-sidecar` (subject to change).

#### Running with envars
```bash
$ docker run --rm -it -e MINA_BP_UPLOAD_URL=https://some-url-here -e MINA_NODE_URL=https://localhost:4321 mina-sidecar
```

#### Running with a config file
```bash
$ docker run --rm -it -v $(pwd)/mina-sidecar.json:/etc/mina-sidecar.json mina-sidecar
```
#### You can even bake your own docker image with the config file already in it
```bash
# Custom Docker Image
$ echo '{"uploadURL": "https://some-url-here", "nodeURL": "https://localhost:4321"}' > your_custom_config.conf
$ cat <<EOF > Dockerfile.custom
FROM codaprotocol/mina-sidecar
COPY your_custom_config.conf /etc/mina-sidecar.json
EOF
$ docker build -t your-custom-sidecar .
$ docker run --rm -it your-custom-sidecar
```