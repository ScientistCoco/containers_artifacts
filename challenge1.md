1. Move the respective dockerfiles into their application folders. i.e.

```
"Dockerfile_0" into "src/user-java"
"Dockerfile_1" into "src/tripviewer"
"Dockerfile_2" into "src/userprofile"
"Dockerfile_3" into "src/poi"
"Dockerfile_4" into "src/trips"
```

You can find where these files go in the `docker-compose.yml` file

Make sure to rename these dockerfiles to "Dockerfile" so that we can run them. i.e. "Dockerfile_0" to "Dockerfile"

2. Build each docker image individually, e.x.

```
cd src/poi
docker build -t poi . # build and tag this image as poi
```

3. We then want to retag the image to follow the AZR format, i.e.

```
docker tag poi registryjzg0978.azurecr.io/poi:v1
```

4. Push this image to the ACR

```
docker push registryjzg0978.azurecr.io/poi:v1
```
