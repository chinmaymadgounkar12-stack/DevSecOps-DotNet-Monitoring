# =======================================================
# Stage 1 - Build the .NET application
# =======================================================

ARG IMAGE_BASE=6.0-alpine

FROM mcr.microsoft.com/dotnet/sdk:$IMAGE_BASE AS build

WORKDIR /build

COPY src ./src

WORKDIR /build/src

RUN dotnet restore
RUN dotnet publish --no-restore --configuration Release


# =======================================================
# Stage 2 - Create the runtime image
# =======================================================

FROM mcr.microsoft.com/dotnet/aspnet:$IMAGE_BASE

WORKDIR /app

COPY --from=build /build/src/bin/Release/net6.0/publish/ .


# Application port
EXPOSE 5000

# ASP.NET Core configuration
ENV ASPNETCORE_URLS=http://*:5000
ENV ASPNETCORE_ENVIRONMENT=Production

# Run the application
ENTRYPOINT ["dotnet", "dotnet-demoapp.dll"]
