# ADR-0002: Migrate to Zod

**Status:** Accepted
**Replaces:** ADR-0001

We are migrating from class-validator to Zod for better TypeScript type inference.

## Decision
Use zod for all input validation schemas. Use z.infer<> for types.

## Consequences
- Better type safety
- Migration cost from existing DTOs
