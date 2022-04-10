### .NET 6 development environment in a Docker container


$ mkdir -p /tmp/dotnet-6-docker

$ docker run -it --rm -p 5000:5000 -v /tmp/dotnet-6-docker:/src mcr.microsoft.com/dotnet/sdk:6.0

root@361977bb2109:/# dotnet --list-sdks

root@361977bb2109:/# dotnet new webapi -o /src/api --no-openapi --no-https

root@361977bb2109:/# export ASPNETCORE_URLS=http://+:5000

root@361977bb2109:/# dotnet run --no-launch-profile --project /src/api/api.csproj

$ curl http://localhost:5000/weatherforecast

### Package your app into a Docker image

# verify the source code
$ ls /tmp/dotnet-6-docker/api

# go to source code folder:
$ cd /tmp/dotnet-6-docker

# create a Dockerfile:

```
FROM mcr.microsoft.com/dotnet/sdk:6.0 as build

WORKDIR /src
COPY api/api.csproj .
RUN dotnet restore

COPY api/ .
RUN dotnet publish -c Release -o /out

FROM mcr.microsoft.com/dotnet/aspnet:6.0

WORKDIR /app
COPY --from=build /out/ /app

ENTRYPOINT ["dotnet", "/app/api.dll"]
```

# use docker to package from source code:
$ docker build -t dotnet-api-demo:latest .

### Run your app in a new container

# run a container from your .NET 5 API image:
$ docker run -d -p 8888:80 --name api dotnet-api-demo:latest

# check the container logs:
$ docker logs api

### Links

https://docs.microsoft.com/en-us/dotnet/architecture/microservices/net-core-net-framework-containers/official-net-docker-images
