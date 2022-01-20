## Building and running the container

```
docker build --tag grpctest -f ./grpcsample/Dockerfile . 
docker run --name grpctest -p 5001:5001 -d grpctest
```

