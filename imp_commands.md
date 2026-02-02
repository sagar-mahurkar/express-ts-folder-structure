# Important Commands Followed

```bash
# initialize node project
npm init -y

# initialize typescript project
tsc --init
```

```json
// tsconfig.json
{
  "compilerOptions": {
    "target": "ES6",
    "module": "commonjs",
    "outDir": "./dist",
    "rootDir": "./src",
    "esModuleInterop": true
  }
}
```

```bash
mkdir src

# entry point to the application
touch src/index.ts

# integration with external services like integrating database services (one data-source corresponds to one specific database)
mkdir -p src/config/data-sources

# initialize all databases
touch src/config/DatabaseConnection.ts

# all database entities
mkdir src/entities

# environment variables configuration files
mkdir src/environments
touch src/environments/.env.staging
touch src/environments/.env.preprod
touch src/environments/.env.prod

# error handlers
mkdir src/errors
touch src/errors/ErrorHandler.ts
touch src/errors/ErrorResponse.ts
touch src/errors/HttpCodes.ts

# middlewares
mkdir src/middlewares

# routers
mkdir src/routers

# version 1 routers
mkdir -p src/routers/v1
touch src/routers/V1Routes.ts

# routers will call functions from controllers
mkdir -p src/controllers/v1
```
