[![Build and Test](https://github.com/taorodrigueswork/rest-api/actions/workflows/dotnet.yml/badge.svg?branch=main)](https://github.com/taorodrigueswork/rest-api/actions/workflows/dotnet.yml)
[![Quality Gate Status](https://sonarcloud.io/api/project_badges/measure?project=taorodrigueswork_rest-api&metric=alert_status)](https://sonarcloud.io/dashboard?id=taorodrigueswork_rest-api)
[![Coverage](https://sonarcloud.io/api/project_badges/measure?project=taorodrigueswork_rest-api&metric=coverage)](https://sonarcloud.io/dashboard?id=taorodrigueswork_rest-api)
[![Bugs](https://sonarcloud.io/api/project_badges/measure?project=taorodrigueswork_rest-api&metric=bugs)](https://sonarcloud.io/dashboard?id=taorodrigueswork_rest-api)
[![Code Smells](https://sonarcloud.io/api/project_badges/measure?project=taorodrigueswork_rest-api&metric=code_smells)](https://sonarcloud.io/dashboard?id=taorodrigueswork_rest-api)
[![Duplicated Lines (%)](https://sonarcloud.io/api/project_badges/measure?project=taorodrigueswork_rest-api&metric=duplicated_lines_density)](https://sonarcloud.io/dashboard?id=taorodrigueswork_rest-api)
[![Lines of Code](https://sonarcloud.io/api/project_badges/measure?project=taorodrigueswork_rest-api&metric=ncloc)](https://sonarcloud.io/dashboard?id=taorodrigueswork_rest-api)
[![Maintainability Rating](https://sonarcloud.io/api/project_badges/measure?project=taorodrigueswork_rest-api&metric=sqale_rating)](https://sonarcloud.io/dashboard?id=taorodrigueswork_rest-api)
[![Reliability Rating](https://sonarcloud.io/api/project_badges/measure?project=taorodrigueswork_rest-api&metric=reliability_rating)](https://sonarcloud.io/dashboard?id=taorodrigueswork_rest-api)
[![Security Rating](https://sonarcloud.io/api/project_badges/measure?project=taorodrigueswork_rest-api&metric=security_rating)](https://sonarcloud.io/dashboard?id=taorodrigueswork_rest-api)
[![Technical Debt](https://sonarcloud.io/api/project_badges/measure?project=taorodrigueswork_rest-api&metric=sqale_index)](https://sonarcloud.io/dashboard?id=taorodrigueswork_rest-api)
[![Vulnerabilities](https://sonarcloud.io/api/project_badges/measure?project=taorodrigueswork_rest-api&metric=vulnerabilities)](https://sonarcloud.io/dashboard?id=taorodrigueswork_rest-api)
 
# REST API TEMPLATE .NET CORE 7 

This is a template for building REST APIs using .NET 7. It is a monolith using multilayer architecture, and uses some common libraries and configurations very used in .Net community.

To get started, clone this repository and run the application using Visual Studio 2022 or the .NET CLI. 

## Technologies

This project uses the following technologies:
- .NET 7
- C#
- SQL Server
- Entity Framework Core 7
- Serilog
- Seq or Elastic Search
- Automapper
- MSTest, [AutoFixture](https://docs.educationsmediagroup.com/unit-testing-csharp/autofixture/quick-glance-at-autofixture) to create objects with data, and [ByteStudio.AutoFixture](https://libraries.io/nuget/ByteStudio.AutoFixture.MSTest1) to use AutoData in MSTest.
- Swagger
- Global Error Middleware
- Repository Pattern
- Azure Key Vault
- Sonar Cloud
- Coverlet- Validation Filter Attribute to validate DTOs
- API Versioning and improved swagger documentation to support multiple versions

## Project Design

This project has classes representing one-to-many and many-to-many relationships, based on the database diagram below. A schedule has a list of days (one-to-many), which also can have a list of person, and each person can belong to multiple days (many-to-many).

![image](https://github.com/taorodrigueswork/rest-api/assets/135357085/7a48f8a5-9510-412f-9ccf-cef0bae0c7c3)

## Project Structure

- `API`: This folder contains the source code for the web API.
  - `Controllers`: This folder contains the controllers that handle incoming requests.
  - `Program.c	s`: This file is responsible for configuring and running the web host. After .NET6 we don't have to use Startup.cs anymore. And the code in the Program file is more concise and simple. TODO: Code Coverage is not ignoring this file when running GitHub Actions using dot net cli and SonarCloud.
  - `CustomMiddlewares`:  Implements the IMiddleware interface and is responsible for global error handling.  It intercepts exceptions thrown by the application and throw the corret exception. 
  - `ValidationFilterAttribute.cs`:  Validates DTO required properties.
- `Business`: This project uses a generic Interface in all classes, in order to make it simple to make dependency injection. We only need to register one time in the Program.cs file and it is going to inject all business classes into the system commented. All classes receive an AutoMapper and a Log via dependency injection.
  - `IBusiness`: This folder contains the interfaces for the business logic components.
- `Entities`: This folder contains the entity models.
  - `DTO`: This folder contains the data transfer objects (DTOs) used for request and response payloads.
  - `Entity`: This folder contains the database entities. There is a many-to-many relationship example with an explicit class to handle it. There is also a BaseEntity class with a generic validate method for the entities.
  - `MapperProfile`: This file contains the AutoMapper mappings profile between DTOs and entity models.
- `Persistence`: This folder contains the data access layer components, using Entity Framework Core ORM.
  - `Context`: It has an ApiContext that inherits from DbContext, which is a class provided by Entity Framework Core that represents a session with the database and allows you to query and save instances of your entity classes.
  - `Migrations`: Contain all migration files defining the changes to the model that should be applied to the database. NOTE: We are using SQL Server for production and Development and SQLite to run Integration Tests. In order to run the same migrations for different providers, we need to edit the migration and create a conditional to verify if the provider is SQL Server or SQLite. The difference between them is the AutoIncrement syntax for Primary Keys.
  - `Repository`: This folder contains the repository classes that handle database operations.
  - `IRepository`: This folder contains the interfaces for the repository classes.
- `Tests`: 
  - `Entities.Test`: This folder contains the entity tests using MSTest.
	- `MappingProfileTests`: It tests if all the properties are mapped correctly. Not a single property should be ignored. Helps to avoid mistakes when mapping entities and DTOs.
  - `Business.Test`: This folder contains the business tests using MSTest.
  - `ExceptionHandlingMiddlewareTests`: 
  - `ValidationFilterAttributeTests`: 
  - `Persistence`: 
	
## Components

### Logging

The logging component uses the built-in `Serilog` library. The logging level can be configured through the `appsettings.json` file. The logs are sent to Seq, but the configuration to log in an ElasticSearch sink is already in the Program.cs file.

### Database Connectivity

The database connectivity is handled by Entity Framework Core. The database connection string can be configured through the `appsettings.json` file.
All the migrations are going to run automatically when the application runs.

### DTOs

DTOs are used to prevent exposing entity models directly to the API consumers. It allows for maintaining a clear separation between the application and the database. AutoMapper library is used to map data between DTOs and entity models.

### Azure Key Vault

Azure Key Vault is a cloud service that can be used to store secrets and configuration data for .NET or ASP.NET Core applications. Azure Key Vault securely stores secret values and allows access to those values without any explicit code change to both applications hosted in Azure and users based on their Azure credentials.

## Future Implementations

- OAuth
- Docker File
- AKS
- HATEOAS
