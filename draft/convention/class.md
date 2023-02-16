# Class

## Class name

We use [`PascalCase`](/draft/convention/legend-naming-convention.md)

```ts
class LogIn {}
```

## Access Modifiers

### Public (eslint)

Properties with no access modifiers are by default public.

:heavy_check_mark: Do this:
```ts
class SomeClass {
  someProperty: string
}
```
:x: Not this:
```ts
class SomeClass {
  public someProperty: string
}
```

### Protected (eslint)

We prefix protected assets with underscore `_`.

```ts
class SomeClass {
  protected _someProperty: string
  protected get _value(): numer {
    this.value
  }
  protected _someFunction(): void {
    // ...
  }
}
```

### Private (eslint)

We prefix protected assets with double underscore `__`.

```ts
class SomeClass {
  private __someProperty: string
  private get __value(): numer {
    this.value
  }
  private __someFunction(): void {
    // ...
  }
}
```

## The story behind underscoring properties

Underscore is not needed if you look at code from compilers perspective, but if you are looking at code in a PR then the underscore give us additional info so we don't need to scroll to the definition part. This is also helpful when we don't have intellisense of the IDE or we are looking at the small part of the code (github PR).
