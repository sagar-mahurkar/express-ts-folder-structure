# Express-ts Project Folder Structure

This repository has folder structure for express-ts project which is scalable

## Recommended Architecture - Feature/Module Based

```bash
src
│
├── app.ts                # express app config
├── server.ts            # server bootstrap
│
├── config/
│   ├── env.ts
│   ├── database.ts
│   └── logger.ts
│
├── modules/             ⭐ (MOST IMPORTANT CHANGE)
│   ├── user/
│   │   ├── user.entity.ts
│   │   ├── user.controller.ts
│   │   ├── user.service.ts
│   │   ├── user.routes.ts
│   │   ├── user.dto.ts
│   │   └── user.repository.ts
│   │
│   ├── auth/
│   └── event/
│
├── middlewares/
│
├── common/              ⭐ shared logic
│   ├── errors/
│   ├── utils/
│   ├── constants/
│   └── types/
│
├── loaders/             ⭐ clean startup pattern
│   ├── db.loader.ts
│   ├── express.loader.ts
│   └── index.ts
│
├── tests/
│
└── types/

```

```bash
# protective boundary around your system
Client → DTO → Controller → Service → Repository → DB
```

⭐ Where It Is Used

Controller:

```typescript
const parsed = CreateUserDTO.parse(req.body);

await userService.createUser(parsed);
```

🚨 Mistake to Avoid
❌ Using Entity as Request Type

Bad:

```typescript
createUser(user: User)
```

Why bad?

Because DB entities often contain:

- ids

- timestamps

- internal flags

- relations

Never expose those.

DTO protects your database.

```bash
# Repository is the data access layer.
# It is the ONLY layer that should talk to the database.

Service → Repository → Database
```

What Should Go Inside user.repository.ts?

ONLY database operations:

✅ create
✅ find
✅ update
✅ delete
✅ complex queries

NO business logic.

Example (TypeORM Style)

```typescript
import { AppDataSource } from "@config/database";
import { User } from "./user.entity";

export class UserRepository {
  private repo = AppDataSource.getRepository(User);

  async create(userData: Partial<User>) {
    const user = this.repo.create(userData);
    return this.repo.save(user);
  }

  async findByEmail(email: string) {
    return this.repo.findOne({ where: { email } });
  }

  async findById(id: string) {
    return this.repo.findOne({ where: { id } });
  }

  async delete(id: string) {
    return this.repo.delete(id);
  }
}
```

Then Services uses it

```typescript
const userRepo = new UserRepository();

export class UserService {

  async createUser(data: CreateUserInput) {

    const existing = await userRepo.findByEmail(data.email);

    if (existing) {
      throw new Error("Email already registered");
    }

    return userRepo.create(data);
  }
}
```

See the separation?

👉 Repository → HOW to fetch

👉 Service → WHEN and WHY

Beautiful architecture.

⭐ Ultra-Clean Flow (Ideal)

```bash
Route
  ↓
Controller
  ↓
DTO Validation
  ↓
Service (business logic)
  ↓
Repository (DB)
  ↓
Entity
```