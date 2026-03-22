# ADR-0001: Use class-validator

**Status:** ~~Accepted~~ → Replaced by ADR-0002

We decided to use class-validator for input validation in our NestJS app.

## Why
- Works well with NestJS ValidationPipe
- Decorator-based, easy to use

## Downside
- Type inference is weak

**Replaced by:** ADR-0002 (Zod migration)
