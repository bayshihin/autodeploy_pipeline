# autodeploy_pipeline


```
name: Autodeploy-pipe
on:
  push:
    branches:
      - 'main'
  
permissions: write-all

jobs:

  build_id_version:
    runs-on: self-hosted
    steps:
      
      - name: Checkout code
        uses: actions/checkout@v2

      - name: Build container
        run: docker build -t ghcr.io/<PROFILE_NAME>/<REPOSITORY_NAME> .           
       
      - name: Login to GitHub Container Registry
        run: echo "${{ secrets.GITHUB_TOKEN }}" | docker login ghcr.io -u ${{ github.repository_owner }} --password-stdin

      - name: Push container to GitHub Container Registry
        run: docker push ghcr.io/<PROFILE_NAME>/<REPOSITORY_NAME>:latest

      - name: Down present deploy with docker-compose
        run:  docker compose down 
        working-directory: /your/dockercomposefile/directory

      - name: Deploy container with docker-compose
        run:  docker compose up -d
        working-directory: /your/dockercomposefile/directory

        #optional
      - name: Delete <none> tagged old image of pulse-service
        run: docker image prune -f
```
