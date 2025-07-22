# SeaweedFS-Storage: ASP.NET File Server

## Overview

**SeaweedFS-Storage** is an ASP.NET Core Web API that provides a simple, scalable, and RESTful interface for file storage, retrieval, and management, leveraging [SeaweedFS](https://github.com/seaweedfs/seaweedfs) as the distributed file system and MongoDB for metadata persistence. This project is designed to serve as a backend file server for applications requiring efficient, large-scale file storage with fast access and metadata management.

## Features

- **File Upload**: Upload files to SeaweedFS via a REST API.
- **File Retrieval**: Retrieve file URLs using a unique secret name.
- **File Deletion**: Remove files from SeaweedFS and clean up metadata.
- **Metadata Management**: Store and query file metadata (including secret names and public URLs) in MongoDB.
- **Swagger/OpenAPI**: Interactive API documentation and testing.
- **Logging**: Integrated with Serilog for robust logging to console and file.

## Architecture

- **ASP.NET Core Web API**: Exposes RESTful endpoints for file operations.
- **SeaweedFS**: Handles distributed file storage and retrieval.
- **MongoDB**: Stores file metadata, including secret names and file IDs.
- **Serilog**: Provides structured logging.

## Quick Start

### Prerequisites

- [.NET 8.0 SDK](https://dotnet.microsoft.com/download)
- [MongoDB](https://www.mongodb.com/try/download/community)
- [SeaweedFS](https://github.com/seaweedfs/seaweedfs) running (see below)

### 1. Configure SeaweedFS

Start SeaweedFS master and volume servers (example):

```bash
weed master -port=9333
weed volume -dir="/tmp/data1" -max=5 -mserver="localhost:9333" -port=8080
```

### 2. Configure MongoDB

Ensure MongoDB is running and accessible at the connection string specified in `appsettings.json`.

### 3. Configure the Application

Edit `FileServer-Asp/appsettings.json` to match your SeaweedFS and MongoDB endpoints:

```json
{
  "SeaweedFS": {
    "MasterUrl": "http://localhost:9333",
    "VolumeUrl": "http://localhost:9333",
    "HelperBaseUrl": "http://localhost:8080"
  },
  "MongoDB": {
    "ConnectionString": "mongodb://localhost:27017",
    "DatabaseName": "seaweedfs"
  }
}
```

### 4. Run the ASP.NET File Server

```bash
cd FileServer-Asp
dotnet run
```

The API will be available at `https://localhost:7261` or as configured.

### 5. Explore the API

Navigate to `/swagger` (e.g., `https://localhost:7261/swagger`) for interactive API documentation.

## API Endpoints

### File Operations

- **POST** `/api/File/Upload`
  - Upload a file (multipart/form-data, fields: `SecretName`, `File`)
- **GET** `/api/File/ViewFileViaSecret/{secret}`
  - Retrieve the public URL for a file by its secret name
- **DELETE** `/api/File/Delete/{fid}`
  - Delete a file by its file identifier

### Metadata Operations

- **GET** `/api/FileRegister/ViewBySecretName?secret={secret}`
  - Retrieve file metadata by secret name

## Example Usage

**Upload a File:**

```bash
curl -F "SecretName=mysecret" -F "File=@/path/to/file.jpg" https://localhost:7261/api/File/Upload
```

**Get File URL:**

```bash
curl https://localhost:7261/api/File/ViewFileViaSecret/mysecret
```

**Delete a File:**

```bash
curl -X DELETE https://localhost:7261/api/File/Delete/{fid}
```

## Project Structure

- `Controllers/` - API endpoints
- `Services/` - Business logic for file and metadata operations
- `Entities/` - Data models for MongoDB
- `Configurations/` - Strongly-typed configuration classes
- `HelperServices/` - SeaweedFS integration helpers
- `Middlewares/` - Custom middleware (e.g., IP whitelisting)
- `GlobalExceptionHandler/` - Centralized error handling

## Development

- Uses dependency injection for services and configuration.
- Logging via Serilog (console and rolling file logs).
- API documentation via Swagger (Swashbuckle).

## License

This project is provided as-is for educational and integration purposes. For production use, review and adapt security, error handling, and deployment practices.
