FROM mcr.microsoft.com/dotnet/sdk:5.0 AS build-env
ARG Configuration=Release
ARG BuildNumber
LABEL TEST=${BuildNumber}

COPY ./src/ /src/
COPY ./Deployment/ /deployment/

RUN dotnet restore ./src/KanikoTest.sln
RUN dotnet build  ./src/KanikoTest.sln
RUN dotnet publish -c ${Configuration} -o /publish/app  ./src/KanikoTest.sln

# Build runtime image
FROM mcr.microsoft.com/dotnet/aspnet:5.0

RUN apt-get update

LABEL "com.tradestation.tsappdev.baseimage"="mcr.microsoft.com/dotnet/aspnet:5.0"

RUN groupadd -g 2000 container_group
RUN useradd -u 1000 container_user

RUN mkdir -p /var/www/

COPY --from=build-env /publish /var/www/
WORKDIR /var/www/app

RUN chown -R container_user:container_group /var/www/app/

USER container_user

ENTRYPOINT ["dotnet", "KanikoTest.dll"]
