
# Example usage

```bash
# Pull the image
docker pull letaylor/trednet-predict:latest

# Run interactive session
docker run --rm -it letaylor/trednet-predict:latest bash
```

# Notes

Due to conda install issues, `pysam` and `pybedtools` installed via pip in the Dockerfile.