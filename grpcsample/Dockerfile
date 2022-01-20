#See https://aka.ms/containerfastmode to understand how Visual Studio uses this Dockerfile to build your images for faster debugging.

FROM mcr.microsoft.com/dotnet/aspnet:6.0-alpine AS base
WORKDIR /app
EXPOSE 5001
## To get rid of the warnings for urls
ENV ASPNETCORE_URLS=

FROM mcr.microsoft.com/dotnet/sdk:6.0-alpine AS build
ENV GLIBC_REPO=https://github.com/sgerrand/alpine-pkg-glibc
ENV GLIBC_VERSION=2.34-r0
RUN set -ex && \
    apk --update add libstdc++ curl ca-certificates && \
    for pkg in glibc-${GLIBC_VERSION} glibc-bin-${GLIBC_VERSION}; \
        do curl -sSL ${GLIBC_REPO}/releases/download/${GLIBC_VERSION}/${pkg}.apk -o /tmp/${pkg}.apk; done && \
    apk add --allow-untrusted /tmp/*.apk && \
    rm -v /tmp/*.apk && \
    /usr/glibc-compat/sbin/ldconfig /lib /usr/glibc-compat/lib

WORKDIR /src
COPY ["grpcsample/grpcsample.csproj", "grpcsample/"]
RUN dotnet restore "grpcsample/grpcsample.csproj"
COPY . .
WORKDIR "/src/grpcsample"
RUN dotnet build "grpcsample.csproj" -c Release -o /app/build

FROM build AS publish
RUN dotnet publish "grpcsample.csproj" -c Release -o /app/publish

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "grpcsample.dll"]