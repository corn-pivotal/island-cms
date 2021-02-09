# Island CMS
CMS Portal built using Piranha CMS frameworks and modified for Container deployments

## Overview
This application is built using .NET Core 3.1 and CMS frameworks developed by Piranha CMS Open Source project. The application is designed to use an NFS mount to store media and SQL Server to store application data.

## Objectives

- Use NFS volume mounts with .NET Core application deployments to persist content
- Implement a simple configuration based application options support for different deployment options
- Provide the ability to override configuration file data with environment variables injected using config maps implemented through a single options class
- Support both Cloud Foundry and Kubernetes based deployments from a single set of source code

## Installation

### Volume Mounts
One difference between cloud foundry and kubernetes deployments is when nfs mounts are done during the container deployment stage. On cloud foundry, volume mounts occur at the start of the the container deployment process while on kubernetes, volume mounts are attached after the application is staged. Because of this, you cannot nfs mount a volume to a sub-folder of the application. This can cause issues for applications dependent on subfolders to be located in a specific location on cloud foundry. In this application, that is the case for the base URL. The base URL must point to a sub-folder of the application. To resolve this, the start command in the manifest has been modified to create a symbolic link to the nfs location. The start command in the manifest file will need to be modified if the volume is mounted in a different location than `/home/media`.

### Available Configuration Environment Variables
By default, the environment variables will always override configuration settings located in the appsettings.json file. All of the settings below can also be configured in settings file.

- PIRANHA_DBTYPE - Database type for application data
	- Valid Values: file (default) | sqlserver | mysql | postgres
	- Parameters: Connection String provided in appsettings.json or secrets
- PIRANHA_DBNAME - Database filename for database file
	- Valid Values: string (default = piranha.db)
	- Note: Only valid for 'file' DBTYPE
- PIRANHA_DBPATH - Database path for database file
	- Valid Values: string (default = .)
	- Note: Only valid for 'file' DBTYPE
- PIRANHA_BASEPATH - Upload location for media files
	- Valid Values: string (default = wwwroot/uploads)
- PIRANHA_BASEURL - URL location for media files
	- Valid Values: string (default = ~/uploads/)
- PIRANHA_MEDIASTORE - Media storage type 
	- Valid Values: file (default) | azure
	- Parameters: Connection String provided in appsettings.json or secrets


### Available Configuration via appsettings.json
The application can be configured using the standard .NET Core configuration files.

Example appsettings.json entries:

		"piranha": {
		  "DatabaseType": "file",
		  "DatabaseFilename": "piranha.db",
		  "DatabasePath": ".",
		  "BasePath": "wwwroot/uploads",
		  "BaseUrl": "~/uploads/",
		  "MediaStorageType": "file"
		},
		"ConnectionStrings": {
		  "piranha": "Server=sqlserver;Database=island_cms;UID=piranha_user;Password=password",
		  "pirahna-media": "DefaultEndpointsProtocol=https;AccountName=;AccountKey=;EndpointSuffix="
		}


### Secrets Support
This application is designed to support loading an optional secrets file located in the secrets sub-folder. Additional settings can be injected by providing an `appsettings.secrets.json` file placed in the secrets folder.
