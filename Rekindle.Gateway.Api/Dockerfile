FROM mcr.microsoft.com/dotnet/aspnet:9.0 AS base
USER $APP_UID
WORKDIR /app
EXPOSE 8080
EXPOSE 8081

FROM mcr.microsoft.com/dotnet/sdk:9.0 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src

# Copy NuGet configuration first
COPY ["NuGet.Config", "."]

# Copy project file
COPY ["Rekindle.Gateway.Api/Rekindle.Gateway.Api.csproj", "Rekindle.Gateway.Api/"]

# Restore packages using the NuGet.Config
RUN dotnet restore "Rekindle.Gateway.Api/Rekindle.Gateway.Api.csproj" --configfile "./NuGet.Config"

# Copy everything else
COPY . .

# Build the project
WORKDIR "/src/Rekindle.Gateway.Api"
RUN dotnet build "./Rekindle.Gateway.Api.csproj" -c $BUILD_CONFIGURATION -o /app/build

FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "./Rekindle.Gateway.Api.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "Rekindle.Gateway.Api.dll"]
