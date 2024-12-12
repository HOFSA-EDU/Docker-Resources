# Dockerfile Troubleshooting

- If you encounter issues while building an image, check your Dockerfile for errors. Use the `docker build` command with the `--no-cache` flag to ensure that all steps are executed from scratch:

```sh
  docker build --no-cache -t <image_name> .
```

[[Docker image]]