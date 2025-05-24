# How to Connect Your Next.js App to a SQL Database Using Prisma

## 1. Install Dependencies

Run the following commands in your project root:

```bash
npm install prisma --save-dev
npm install @prisma/client
```

Also install the database client depending on your database:

- For MySQL:
  ```bash
  npm install mysql2
  ```
- For PostgreSQL:
  ```bash
  npm install pg
  ```

## 2. Initialize Prisma

```bash
npx prisma init
```

This creates a `prisma` folder with `schema.prisma` and a `.env` file.

## 3. Configure Database Connection

Edit `.env` file to add your database connection string:

```env
DATABASE_URL="mysql://user:password@localhost:3306/mydb"
# or for PostgreSQL
DATABASE_URL="postgresql://user:password@localhost:5432/mydb"
```

## 4. Define Your Data Model

Edit `prisma/schema.prisma` to define your tables. Example:

```prisma
datasource db {
  provider = "mysql" // or "postgresql"
  url      = env("DATABASE_URL")
}

generator client {
  provider = "prisma-client-js"
}

model User {
  id       Int      @id @default(autoincrement())
  username String   @unique
  email    String   @unique
  password String
  role     String
  appointments Appointment[]
  transactions Transaction[]
}

model Appointment {
  id          Int      @id @default(autoincrement())
  date        DateTime
  time        String
  description String?
  userId      Int
  user        User     @relation(fields: [userId], references: [id])
}

model Transaction {
  id          Int      @id @default(autoincrement())
  type        String
  amount      Float
  description String?
  date        DateTime @default(now())
  userId      Int
  user        User     @relation(fields: [userId], references: [id])
}
```

## 5. Generate Prisma Client

```bash
npx prisma generate
```

## 6. Run Migrations

```bash
npx prisma migrate dev --name init
```

## 7. Use Prisma Client in API Routes

Create API routes in `src/pages/api` to handle CRUD operations. Example:

```ts
// src/pages/api/users.ts
import { PrismaClient } from '@prisma/client'

const prisma = new PrismaClient()

export default async function handler(req, res) {
  if (req.method === 'GET') {
    const users = await prisma.user.findMany()
    res.json(users)
  }
  // Add POST, PUT, DELETE handlers as needed
}
```

## 8
