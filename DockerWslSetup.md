# Setup
1. Install WSL2 on Windows  - manual installation
https://docs.microsoft.com/en-us/windows/wsl/install-win10#manual-installation-steps
2. Download and install Docker for Windows
https://docs.docker.com/docker-for-windows/wsl/#download
3. Clone this repo to some **path**

# Run local SQL Server
1. Run **Ubuntu** from Windows Start Menu, this will open a terminal
2. Check if docker is installed
```
$: docker -v && docker-compose -v
```
3. In the shell **cd** to where is the **iine-env** repo **path** and run the environment.

```bash
$: cd /mnt/c/iine/repos/iine-env
$: chmod +x env-setup.sh
$: ./env-setup.sh
```
This will launch the environment, you should be able to see the docker icon in the bottom right Windows sidebar, you can click it to view current environments.

# Load DEV SQL backup into the Local SQL
1. Download backup **iine-cms-deb-backup.bacpac** from 
https://drive.google.com/drive/u/0/folders/1Rzodl_PzWfHAR2A8Je_K-SWJQtLlZf7K

2. Open SSMS and connect to
>server: **localhost, 1433**

>username: **xx**

>password: **xxx!**

**IN THE OPTIONS SELECT: TRUST SERVER CERTIFICATE CHECKBOX**

And click connect

3. Righ click on **databases** folder and select **Import Data-Tier Application** there select the backup file and give name to the database **dev**.


# More on Docker
The setup in written in docker-compose.yml
https://docs.docker.com/compose/

# Limiting resource usage for WSL2 
Workaround:

Create a **%UserProfile%/.wslconfig** file in Windows and use it to limit memory assigned to WSL2 VM.

Example
```
[wsl2]
memory=6GB
swap=0
localhostForwarding=true
```
This will still consume the entire 6GBs regardless of Linux memory usage, but at least it'll stop growing more than that.

Supported settings are documented [these release notes for wsl2](https://docs.microsoft.com/en-us/windows/wsl/release-notes#build-18945).

# SQL Server connection error
When you try to connect to docker sql server via SSMS you can encounter such error:
```
A connection was successfully established with the server, but then an error occurred during the login process. (provider: SSL Provider, error: 0 - Łańcuch certyfikatów został wystawiony przez urząd, którego nie jest zaufany.) (Microsoft SQL Server, Error: -2146893019)
```
In such case:
1. Click "options" button
2. Check checkbox labaled with: "Trust server certificate"

After that simple steps everything should work.

# Example docker compose yaml
```yaml
services:
  sqldb:
    image: mcr.microsoft.com/mssql/server:2019-latest
    ports:
      - 1433:1433
    volumes:
      - sqldata:/var/opt/mssql
    environment:
      - 'ACCEPT_EULA=Y'
      - 'MSSQL_SA_PASSWORD=password1234!'
    restart: unless-stopped
volumes:
  sqldata:
```
