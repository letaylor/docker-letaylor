
# docker-letaylor

This repo contains Docker images that are automatically built using GitHub Actions. It is not designed to scale to many images.

# Push images to DockerHub

## 1. Access to upload to DockerHub

Store a `$DOCKERHUB_TOKEN` as a secret in your repository.

1. Login to DockerHub [hub.docker.com](https://hub.docker.com/).
2. Click on your username on the top left and go to 'Account Settings'.
3. On the left hand panel, go to 'Security' and enter your password as requested.
4. Now create an API token. Name it GitHub Actions.
5. Create the token and copy it.
6. In your GitHub repository, navigate to settings, and secrets under security
8. Add an environment variable with the name `DOCKERHUB_TOKEN` and give it the value of the API token that you copied from [hub.docker.com](https://hub.docker.com/).

NOTE: You may also add a `DOCKER_USERNAME` secret. By default, the workflow assumes your DockerHub username is the same as your GitHub username. To set up a different username, you will need to replace `${{ github.actor }}` with `${{ secrets.DOCKER_USERNAME }}` in `.github/workflows/docker-publish.yml`. 

## 2. Edit config files

Edit the following files to point to the image you wish to build (i.e., `./images/{IMAGE_NAME}/Dockerfile`):
* `.github/workflows/docker-publish.yml` : alter `IMAGE_NAME`.


# Push images to GitHub Container Registry

## 1. Access to upload to GitHub Container Registry

Do a similar process as outlined above, but at https://github.com/settings/tokens. Store the token as `GHCR_TOKEN` in your repository.

## 2. Edit config files

Edit the following files to point to the image you wish to build (i.e., `./images/{IMAGE_NAME}/Dockerfile`):
* `.github/workflows/ghcr-publish.yml` : alter `IMAGE_NAME`.
