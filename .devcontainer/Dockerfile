FROM mcr.microsoft.com/powershell:7.0.0-debian-10

# Avoid warnings by switching to noninteractive
ENV DEBIAN_FRONTEND=noninteractive

ENV FUNCTIONS_WORKER_RUNTIME=powershell

RUN apt-get update \
    && apt-get -y install --no-install-recommends apt-utils dialog 2>&1 \
    #
    # Verify git and needed tools are installed
    && apt-get -y install \
        git \
        openssh-client \
        less \
        iproute2 \
        procps \
        curl \
        apt-transport-https \
        gnupg2 \
        lsb-release 
        
# Install .NET Core and Functions Core Tools
RUN apt-get update \
    && apt-get install -y wget gpg \
    && wget -O- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.asc.gpg \
    && mv microsoft.asc.gpg /etc/apt/trusted.gpg.d/ \
    && wget https://packages.microsoft.com/config/debian/10/prod.list \
    && mv prod.list /etc/apt/sources.list.d/microsoft-prod.list \
    && apt-get update \
    && apt-get install apt-transport-https \
    && apt-get update \
    && apt-get install -y dotnet-sdk-3.1 \
    && wget https://packages.microsoft.com/config/debian/9/prod.list \
    && mv prod.list /etc/apt/sources.list.d/microsoft-prod-debian-9.list \
    && apt-get update \
    && apt-get install -y azure-functions-core-tools-3 \
    && rm /etc/apt/sources.list.d/microsoft-prod-debian-9.list

# Install custom PowerShell worker
RUN apt-get update \
    && apt-get install -y unzip \
    && wget https://ci.appveyor.com/api/buildjobs/046o895ut8hf5bto/artifacts/Microsoft.Azure.Functions.PowerShellWorker.PS7.3.0.266.nupkg \
    && unzip Microsoft.Azure.Functions.PowerShellWorker.PS7.3.0.266.nupkg -d /tmp/psworker \
    && rm Microsoft.Azure.Functions.PowerShellWorker.PS7.3.0.266.nupkg \
    && if [ -f "/usr/lib/azure-functions-core-tools-3/workers/powershell/Microsoft.Azure.Functions.PowerShellWorker.dll"]; then mkdir /usr/lib/azure-functions-core-tools-3/workers/powershell/6; mv "!(/usr/lib/azure-functions-core-tools-3/workers/powershell/6)" /usr/lib/azure-functions-core-tools-3/workers/powershell/6; fi \
    && if [ -d "/usr/lib/azure-functions-core-tools-3/workers/powershell/7" ]; then rm -r /usr/lib/azure-functions-core-tools-3/workers/powershell/7; fi \
    && cp -R /tmp/psworker/contentFiles/any/any/workers/powershell/7 /usr/lib/azure-functions-core-tools-3/workers/powershell/ \
    && mv /usr/lib/azure-functions-core-tools-3/workers/powershell/7/worker.config.json /usr/lib/azure-functions-core-tools-3/workers/powershell/

# Switch back to dialog for any ad-hoc use of apt-get
ENV DEBIAN_FRONTEND=
