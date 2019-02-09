FROM microsoft/dotnet:2.2-aspnetcore-runtime AS base
WORKDIR /app
EXPOSE 80

FROM microsoft/dotnet:2.2-sdk AS build
WORKDIR /src
COPY src/Services/Ordering/Ordering.API/Ordering.API.csproj src/Services/Ordering/Ordering.API/
RUN dotnet restore src/Services/Ordering/Ordering.API/Ordering.API.csproj
COPY . .
WORKDIR /src/src/Services/Ordering/Ordering.API
RUN dotnet build Ordering.API.csproj -c Release -o /app

FROM build AS publish
RUN dotnet publish Ordering.API.csproj -c Release -o /app

FROM base AS final
WORKDIR /app
COPY --from=publish /app .
ENTRYPOINT ["dotnet", "Ordering.API.dll"]
