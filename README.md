# ASP.NET Core Web API 2.0 preview 1 on Raspberry Pi 2 and Raspbian Jessie

I found the good instructions from https://timdows.com/projects/asp-net-core-2-0-0-beta-on-a-raspberry-pi-3/ which I updated to meet the current ASP.NET Core 2 builds and my Raspberry Pi and Windows PC environment.

## Prepare Raspberry Pi
1. Install the following packages:

```
sudo apt-get install libunwind8 libunwind8-dev gettext libicu-dev liblttng-ust-dev libcurl4-openssl-dev libssl-dev uuid-dev
```

2. Get dotnet 2.0 from  

```
wget https://dotnetcli.blob.core.windows.net/dotnet/Runtime/release/2.0.0/dotnet-runtime-latest-linux-arm.tar.gz
```

After extracting, test ./dotnet command:

```
pi@raspberrypi:~/dotnet $ ./dotnet

Usage: dotnet [options]
Usage: dotnet [path-to-application]

Options:
  -h|--help            Display help.
  --version         Display version.

path-to-application:
  The path to an application .dll file to execute.

pi@raspberrypi:~/dotnet $
```

## Create project on Windows environment
1. Install ASP.NET Core 2.0 preview 1 on Windows environment:
https://www.microsoft.com/net/core/preview#windowscmd

2. Create a new ASP.NET Core Web API project in Windows commandline:

```
C:\Visual Studio 2017\Projects>md PiDemoWebAPI
C:\Visual Studio 2017\Projects>cd PiDemoWebAPI
C:\Visual Studio 2017\Projects\PiDemoWebAPI>dotnet new webapi
The template "ASP.NET Core Web API" was created successfully.
This template contains technologies from parties other than Microsoft, see https://aka.ms/template-3pn for details.

Processing post-creation actions...
Running 'dotnet restore' on C:\Users\heikk\Documents\Visual Studio 2017\Projects\PiDemoWebAPI\PiDemo
WebAPI.csproj...
Restore succeeded.

C:\Visual Studio 2017\Projects\PiDemoWebAPI>
```

3. Check your runtime framework version: 

```
C:\Visual Studio 2017\Projects\PiDemoWebAPI>dotnet --info
.NET Command Line Tools (2.0.0-preview1-005977)

Product Information:
 Version:            2.0.0-preview1-005977
 Commit SHA-1 hash:  414cab8a0b

Runtime Environment:
 OS Name:     Windows
 OS Version:  10.0.14393
 OS Platform: Windows
 RID:         win10-x64
 Base Path:   C:\Program Files\dotnet\sdk\2.0.0-preview1-005977\

Microsoft .NET Core Shared Framework Host

  Version  : 2.0.0-preview1-002111-00
  Build    : 1ff021936263d492539399688f46fd3827169983
  
C:\Visual Studio 2017\Projects\PiDemoWebAPI>
```

4. Add the following lines in the .csproj file under <PropertyGroup>:

```
<RuntimeIdentifier>linux-arm</RuntimeIdentifier>
<RuntimeFrameworkVersion>2.0.0-preview1-002111-00</RuntimeFrameworkVersion>
```

5. Run dotnet restore:

```
C:\Visual Studio 2017\Projects\PiDemoWebAPI>dotnet restore
  Restoring packages for C:\Users\heikk\Documents\Visual Studio 2017\Projects\PiDemoWebAPI\PiDemoWebAPI.csproj...
  Restoring packages for C:\Users\heikk\Documents\Visual Studio 2017\Projects\PiDemoWebAPI\PiDemoWebAPI.csproj...
  Restore completed in 3,03 sec for C:\Users\heikk\Documents\Visual Studio 2017\Projects\PiDemoWebAPI\PiDemoWebAPI.csproj.
  Installing runtime.linux-arm.Microsoft.NETCore.DotNetAppHost 2.0.0-preview1-002111-00.
  Installing runtime.linux-arm.Microsoft.NETCore.DotNetHostResolver 2.0.0-preview1-002111-00.
  Installing runtime.linux-arm.Microsoft.NETCore.DotNetHostPolicy 2.0.0-preview1-002111-00.
  Installing runtime.linux-arm.Microsoft.NETCore.App 2.0.0-preview1-002111-00.
  Generating MSBuild file C:\Users\heikk\Documents\Visual Studio 2017\Projects\PiDemoWebAPI\obj\PiDemoWebAPI.csproj.nuget.g.props.
  Writing lock file to disk. Path: C:\Users\heikk\Documents\Visual Studio 2017\Projects\PiDemoWebAPI\obj\project.assets.json
  Restore completed in 52,28 sec for C:\Users\heikk\Documents\Visual Studio 2017\Projects\PiDemoWebAPI\PiDemoWebAPI.csproj.

  NuGet Config files used:
      C:\Users\heikk\AppData\Roaming\NuGet\NuGet.Config
      C:\Program Files (x86)\NuGet\Config\Microsoft.VisualStudio.Offline.config

  Feeds used:
      https://api.nuget.org/v3/index.json
      C:\Users\heikk\.dotnet\NuGetFallbackFolder
      C:\Program Files (x86)\Microsoft SDKs\NuGetPackages\

  Installed:
      4 package(s) to C:\Users\heikk\Documents\Visual Studio 2017\Projects\PiDemoWebAPI\PiDemoWebAPI.csproj

C:\Visual Studio 2017\Projects\PiDemoWebAPI>
```

6. Publish WebAPI to linux arm runtime

```
C:\Visual Studio 2017\Projects\PiDemoWebAPI>dotnet publish -r linux-arm
Microsoft (R) Build Engine version 15.3.117.23532
Copyright (C) Microsoft Corporation. All rights reserved.

  PiDemoWebAPI -> C:\Visual Studio 2017\Projects\PiDemoWebAPI\bin\Debug\netcoreapp2.0\linux-arm\PiDemoWebAPI.dll
  PiDemoWebAPI -> C:\Visual Studio 2017\Projects\PiDemoWebAPI\bin\Debug\netcoreapp2.0\linux-arm\publish\
  
C:\Visual Studio 2017\Projects\PiDemoWebAPI>
```

## Run Web API on Raspberry Pi

1. Copy all the build output from
```
C:\Users\heikk\Documents\Visual Studio 2017\Projects\PiDemoWebAPI\bin\Debug\netcoreapp2.0\linux-arm
```
to Raspberry PI application directory (in my case it is ```~/PiDemoWebAPI```)

2. Run published Web API:

```
pi@raspberrypi:~/PiDemoWebAPI/publish $ /home/pi/dotnet/dotnet PiDemoWebAPI.dll
Hosting environment: Production
Content root path: /home/pi/PiDemoWebAPI/publish
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

9. Test WebAPI:

```
pi@raspberrypi:~ $ curl http://localhost:5000/api/values
["value1","value2"]
```


