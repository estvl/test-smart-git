
# Slum Routes Backend

## Overview

This project follows best practices to ensure high-quality, maintainable, and scalable code. The goal is to have 0 warnings and 0 informational messages during the build process. This README outlines the standards and guidelines for achieving this goal.

## Best Practices

### 1. Coding Standards

- Follow the **[C# Coding Conventions](https://docs.microsoft.com/en-us/dotnet/csharp/fundamentals/coding-style/coding-conventions)**.
- Use meaningful variable and method names.
- Write XML documentation comments for all public members.

### 2. Code Analysis

- Enable all recommended **code analysis rules** in your project.
- Regularly run static code analysis tools (e.g., SonarQube, ReSharper).

### 3. Zero Warnings Policy

- Treat all compiler warnings as errors. This ensures that no warning is ignored and must be addressed.
- To enable this, add the following line to your project file (`.csproj`):
  ```xml
  <PropertyGroup>
      <TreatWarningsAsErrors>true</TreatWarningsAsErrors>
  </PropertyGroup>
  ```

## Project Structure

- **Controllers**: Handle HTTP requests and responses.
- **Services**: Contain business logic and interact with repositories.
- **Repositories**: Interact with the database using Entity Framework Core.
- **Models**: Define the data structures used throughout the application.

## Controllers

### BusinessController

- `GET /api/business/hike/{hikeId}`: Gets businesses associated with a specific hike.

### EventController

- `GET /api/event/hike/{hikeId}`: Gets events associated with a specific hike.

### HikeController

- `GET /api/hike/{id}`: Gets a hike by its ID.

### HomePageController

- `GET /api/homepage/highlights`: Gets highlights for hikes, events, and businesses.
- `GET /api/homepage/search`: Searches for hikes, events, and businesses based on a query.

## Running the Project

### Using .NET CLI

1. **Restore Dependencies**: 
    ```sh
    dotnet restore
    ```

2. **Build the Project**: 
    ```sh
    dotnet build
    ```

3. **Run the Project**: 
    ```sh
    dotnet run
    ```

### Using Docker

Docker is used to containerize the application for different environments (localhost, Development, Production).

#### Prerequisites

- Docker installed on your machine.

#### Build and Run with Docker Compose

1. **Localhost**
   
   **1.1 Prerequisites**
    
    ### Running Redis as a Docker Container
    
    For the development process in a localhost environment, you can install and run Redis as a Docker container to simplify the setup. Redis is a lightweight, high-performance in-memory database used for caching or 
    messaging.
    
    ```sh
    docker pull redis
    docker run --name my-redis -d redis

2. **Development**

   ```sh
   docker-compose -f docker-compose.yml -f docker-compose.development.yml up --build
   ```

3. **Production**

   ```sh
   docker-compose -f docker-compose.yml -f docker-compose.production.yml up --build
   ```

#### Docker Compose Files

- `docker-compose.yml`: Base configuration common to all environments.
- `docker-compose.override.yml`: Localhost-specific configuration.
- `docker-compose.development.yml`: Development-specific configuration.
- `docker-compose.production.yml`: Production-specific configuration.

#### Example docker-compose.yml

```yaml
services:
  hikeservice:
    build:
      context: .
      dockerfile: HikeService/Dockerfile
    ports:
      - "5002:80"

  userservice:
    build:
      context: .
      dockerfile: UserService/Dockerfile
    ports:
      - "5001:80"

  gateway:
    build:
      context: .
      dockerfile: Gateway/Dockerfile
    depends_on:
      - hikeservice
      - userservice
    ports:
      - "5000:80"
```

#### Example docker-compose.override.yml (for Localhost)

```yaml
services:
  hikeservice:
    volumes:
      - ./HikeService/appsettings.json:/app/appsettings.json

  userservice:
    volumes:
      - ./UserService/appsettings.json:/app/appsettings.json

  gateway:
    volumes:
      - ./Gateway/appsettings.json:/app/appsettings.json
      - ./Gateway/ocelot.json:/app/ocelot.json
```

#### Example docker-compose.development.yml

```yaml
services:
  hikeservice:
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
    volumes:
      - ./HikeService/appsettings.Development.json:/app/appsettings.Development.json
      - ./HikeService/appsettings.json:/app/appsettings.json

  userservice:
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
    volumes:
      - ./UserService/appsettings.Development.json:/app/appsettings.Development.json
      - ./UserService/appsettings.json:/app/appsettings.json

  gateway:
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
    volumes:
      - ./Gateway/appsettings.Development.json:/app/appsettings.Development.json
      - ./Gateway/appsettings.json:/app/appsettings.json
      - ./Gateway/ocelot.Development.json:/app/ocelot.Development.json
      - ./Gateway/ocelot.json:/app/ocelot.json
```

#### Example docker-compose.production.yml

```yaml
services:
  hikeservice:
    environment:
      - ASPNETCORE_ENVIRONMENT=Production
    volumes:
      - ./HikeService/appsettings.Production.json:/app/appsettings.Production.json
      - ./HikeService/appsettings.json:/app/appsettings.json

  userservice:
    environment:
      - ASPNETCORE_ENVIRONMENT=Production
    volumes:
      - ./UserService/appsettings.Production.json:/app/appsettings.Production.json
      - ./UserService/appsettings.json:/app/appsettings.json

  gateway:
    environment:
      - ASPNETCORE_ENVIRONMENT=Production
    volumes:
      - ./Gateway/appsettings.Production.json:/app/appsettings.Production.json
      - ./Gateway/appsettings.json:/app/appsettings.json
      - ./Gateway/ocelot.Production.json:/app/ocelot.Production.json
      - ./Gateway/ocelot.json:/app/ocelot.json
```

## Create Certs
Even in local https in enforced, use a certificate like this

```
mkdir certs
dotnet dev-certs https --trust
dotnet dev-certs https --export-path certs/aspnetapp.pfx --password password
```


## Running Tests

To run the tests, use the following command:
```sh
dotnet test
```

Make sure all tests pass and there are no warnings or informational messages.

## Using the .http File

An `.http` file is provided to test the API endpoints. You can use tools like **Visual Studio Code** with the **REST Client** extension or **Postman** to execute these requests.

### Example .http File

```http
### BusinessController Endpoints

# Get Businesses by Hike ID
GET http://localhost:5002/api/business/hike/{{hikeId}}
Content-Type: application/json

### EventController Endpoints

# Get Events by Hike ID
GET http://localhost:5002/api/event/hike/{{hikeId}}
Content-Type: application/json

### HikeController Endpoints

# Get Hike by ID
GET http://localhost:5002/api/hike/{{hikeId}}
Content-Type: application/json

### HomePageController Endpoints

# Get Home Page Highlights
GET http://localhost:5002/api/homepage/highlights?index=0&limit=10
Content-Type: application/json

# Search Home Page
GET http://localhost:5002/api/homepage/search?query=test&index=0&limit=10
Content-Type: application/json

### User Service Endpoints

# This request attempts to log in a user with the specified email address.
GET http://localhost:5001/api/routes/login?email=test@example.com
Accept: application/json
```

Replace `{{hikeId}}` with actual hike IDs when testing the API.

## Documentation

Ensure all public members have XML documentation comments. This helps in generating useful API documentation and maintaining code quality.

```csharp
/// <summary>
/// Gets a hike by its ID.
/// </summary>
/// <param name="id">The ID of the hike.</param>
/// <returns>Returns the details of the specified hike.</returns>
/// <response code="200">Returns the hike details.</response>
/// <response code="404">If the hike is not found.</response>
[HttpGet("{id}")]
public async Task<ActionResult<Hike>> GetHikeById(Guid id)
{
    var hike = await _hikeService.GetByIdAsync(id);
    if (hike == null)
    {
        return NotFound();
    }
    return Ok(hike);
}
```

## Conclusion

By following these guidelines and best practices, you can ensure that the Slum Routes Backend is of high quality, maintainable, and scalable. Regularly review and update your code to adhere to these standards.
