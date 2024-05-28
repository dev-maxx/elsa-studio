FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base
WORKDIR /app
EXPOSE 80

FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
RUN apt-get update -yq && apt-get upgrade -yq && apt-get install -yq curl git nano
RUN curl -sL https://deb.nodesource.com/setup_16.x | bash - && apt-get install -yq nodejs build-essential

WORKDIR /src
#COPY ["NuGet.Config", "."]
COPY ["src/hosts/Elsa.Studio.Host.Server/Elsa.Studio.Host.Server.csproj", "src/hosts/Elsa.Studio.Host.Server/"]
COPY ["src/bundles/Elsa.Studio/Elsa.Studio.csproj", "src/bundles/Elsa.Studio/"]
COPY ["src/framework/Elsa.Studio.Shell/Elsa.Studio.Shell.csproj", "src/framework/Elsa.Studio.Shell/"]
COPY ["src/framework/Elsa.Studio.Core/Elsa.Studio.Core.csproj", "src/framework/Elsa.Studio.Core/"]
COPY ["src/framework/Elsa.Studio.Shared/Elsa.Studio.Shared.csproj", "src/framework/Elsa.Studio.Shared/"]
COPY ["src/framework/Elsa.Studio.DomInterop/Elsa.Studio.DomInterop.csproj", "src/framework/Elsa.Studio.DomInterop/"]
COPY ["src/modules/Elsa.Studio.Dashboard/Elsa.Studio.Dashboard.csproj", "src/modules/Elsa.Studio.Dashboard/"]
COPY ["src/modules/Elsa.Studio.Workflows/Elsa.Studio.Workflows.csproj", "src/modules/Elsa.Studio.Workflows/"]
COPY ["src/modules/Elsa.Studio.ActivityPortProviders/Elsa.Studio.ActivityPortProviders.csproj", "src/modules/Elsa.Studio.ActivityPortProviders/"]
COPY ["src/modules/Elsa.Studio.Workflows.Core/Elsa.Studio.Workflows.Core.csproj", "src/modules/Elsa.Studio.Workflows.Core/"]
COPY ["src/modules/Elsa.Studio.UIHints/Elsa.Studio.UIHints.csproj", "src/modules/Elsa.Studio.UIHints/"]
#COPY ["src/modules/Elsa.Studio.UserGuide/Elsa.Studio.UserGuide.csproj", "src/modules/Elsa.Studio.UserGuide/"]
COPY ["src/modules/Elsa.Studio.Workflows.Designer/Elsa.Studio.Workflows.Designer.csproj", "src/modules/Elsa.Studio.Workflows.Designer/"]
COPY ["src/framework/Elsa.Studio.Core.BlazorServer/Elsa.Studio.Core.BlazorServer.csproj", "src/framework/Elsa.Studio.Core.BlazorServer/"]
COPY ["src/modules/Elsa.Studio.Login.BlazorServer/Elsa.Studio.Login.BlazorServer.csproj", "src/modules/Elsa.Studio.Login.BlazorServer/"]
COPY ["src/modules/Elsa.Studio.Login/Elsa.Studio.Login.csproj", "src/modules/Elsa.Studio.Login/"]
COPY ["src/modules/Elsa.Studio.Webhooks/Elsa.Studio.Webhooks.csproj", "src/modules/Elsa.Studio.Webhooks/"]
COPY ["src/modules/Elsa.Studio.WorkflowContexts/Elsa.Studio.WorkflowContexts.csproj", "src/modules/Elsa.Studio.WorkflowContexts/"]


COPY . .
WORKDIR "/src/src/framework/Elsa.Studio.DomInterop/ClientLib"
RUN npm install --force
RUN npm run build
WORKDIR "/src/src/modules/Elsa.Studio.Workflows.Designer/ClientLib"
RUN npm install --force
RUN npm run build
WORKDIR "/src/src/hosts/Elsa.Studio.Host.Server"
RUN dotnet restore "Elsa.Studio.Host.Server.csproj" --use-current-runtime
RUN dotnet build "Elsa.Studio.Host.Server.csproj" -c Release -o /app/build

FROM build AS publish
RUN dotnet publish "Elsa.Studio.Host.Server.csproj" -c Release -o /app/publish /p:UseAppHost=false /p:TargetFramework=net8.0

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "Elsa.Studio.Host.Server.dll"]
