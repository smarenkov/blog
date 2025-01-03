---
title: NestJS and TypeORM Database Migrations
date: 2025-01-02
description: A quick guide to setting up database migrations in a NestJS project using TypeORM.
tags: 
  - NestJS
  - TypeORM
categories:
  - NestJS
  - TypeORM
---

## Setting Up Database Module

To begin, create a new module in your project for managing database connections and migrations:

```
├─ src/
│   └─ database/
│       ├─ migrations/
│       ├─ database.config.ts
│       └─ database.module.ts
└─ .env
```

### `database.config.ts`
```ts
// Load .env file from project root directory
dotenv.config({ path: '.env' }); // ,env file from root

// Logging env variables to ensure proper loading of .env file
console.log("Environment Variables:"); 
console.log("POSTGRES_USER:", env.POSTGRES_USER);
console.log("POSTGRES_DB:", env.POSTGRES_DB);

const baseConfig = {
  type: "postgres" as const,
  entitySkipConstructor: true,
  host: env.POSTGRES_HOST,
  port: parseInt(env.POSTGRES_PORT),
  username: env.POSTGRES_USER,
  password: env.POSTGRES_PASSWORD,
  database: env.POSTGRES_DB,
  entities: [cwd() + "/dist/core/**/*.entity.js"],
  migrations: [cwd() + "/dist/database/migrations/*.js"],
  synchronize: false,
  dropSchema: false,
  migrationsRun: true,
  logging: false,
  maxQueryExecutionTime: 30000,
  poolSize: 5,
};

export const databaseConfig: DataSourceOptions = {
  ...baseConfig,
};

export const dataSource = new DataSource({
  ...baseConfig,
  host: "localhost" // "localhost" for use outside Docker
});
```

### `database.module.ts`
```typescript 
@Module({
  imports: [
    TypeOrmModule.forRoot(databaseConfig),
  ],
})
export class DatabaseModule { }
```

### `.env`
``` 
POSTGRES_HOST=
POSTGRES_PORT=
POSTGRES_USER=
POSTGRES_DB=
POSTGRES_PASSWORD=
```

### `application’s main module`

```ts
@Module({
  imports: [
    ConfigModule.forRoot({
      envFilePath: `.env`,
      isGlobal: true,
    }),
    DatabaseModule,
  ],
})
export class AppModule { }
```

## Setting Up Migration Commands

Add these scripts to your package.json:

```json
{
  "scripts": {
    "migration:create": "npx typeorm migration:create src/database/migrations/$npm_config_name",
    "migration:generate": "npm run build && npx typeorm migration:generate -d dist/database/database.config.js src/database/migrations/$npm_config_name",
    "migration:run": "npx typeorm migration:run -d dist/database/database.config.js",
    "migration:revert": "npm run build && npx typeorm migration:revert -d dist/database/database.config.js"
  }
}
```

Now you can use these commands to manage your migrations:

```bash
# Create a new empty migration
npm run migration:create --name=your-migration-name

# Generate a migration based on entity changes
npm run migration:generate --name=your-migration-name

# Run pending migrations
npm run migration:run

# Revert the last migration
npm run migration:revert
```