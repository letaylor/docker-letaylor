
# Checklist

* Docker R version matches renv.lock
* Conda r-env version matches renv.lock


# Example usage

```bash
# Pull the image
docker pull letaylor/sc_decontx:latest

# Run interactive session v1
docker run --rm -it letaylor/sc_decontx:latest bash

# Run interactive session v2
docker run -ti --rm letaylor/sc_decontx:latest
```
