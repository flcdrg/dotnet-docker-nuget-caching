#See https://aka.ms/customizecontainer to learn how to customize your debug container and how Visual Studio uses this Dockerfile to build your images for faster debugging.

FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base
USER app
WORKDIR /app
EXPOSE 8080
EXPOSE 8081

FROM mcr.microsoft.com/dotnet/sdk:8.0 AS publish
ARG BUILD_CONFIGURATION=Release
WORKDIR /src
COPY ["ExampleWebApp/ExampleWebApp.csproj", "ExampleWebApp/"]

RUN dotnet restore "./ExampleWebApp/ExampleWebApp.csproj"
RUN printenv
RUN --mount=from=nuget,target=~/.nuget/packages,readwrite ls -al ~
RUN --mount=from=nuget,target=~/.nuget/packages,readwrite ls -al ~/.nuget/packages

COPY . .
WORKDIR "/src/ExampleWebApp"
RUN dotnet build --no-restore "./ExampleWebApp.csproj" -c $BUILD_CONFIGURATION -o /app/build

RUN dotnet publish --no-restore "./ExampleWebApp.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "ExampleWebApp.dll"]