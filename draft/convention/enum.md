# Enum

We only use enums with string value

## Name (eslint)

We use [`PascalCase`](/draft/convention/legend-naming-convention.md)

## Key name (eslint)

We use [`CONSTANT_CASE`](/draft/convention/legend-naming-convention.md)

## Value name

Value names are the same as key values, this is because enums are constants and we don't want to encourage programmers to display enum values to end users :smile:. Whenever enum needs to be displayed to end user it value must be converted to something readable :smile:. And if the enum value appear in the database we can clearly see that it is a constant value by having it in [`CONSTANT_CASE`](/draft/convention/legend-naming-convention.md)

:heavy_check_mark: Do this:
```ts
enum SomeEnum {
  VALUE_ONE   = "VALUE_ONE",
  VALUE_TWO   = "VALUE_TWO",
  VALUE_THREE = "VALUE_THREE",
}
```

If you need to use enum for mapping you can add suffix Mapper and have any value ( for keys we must use CONSTANT_CASE)

:heavy_check_mark: Do this:
```ts
enum SomeEnumMapper {
  TYPE_ONE = 'type one',
  TYPE_TWO = 'type-two',
  TYPE_THREE = 'or any other value',
}
```

:x: Not this:
```ts
enum SomeEnum {
  TypeOne = 'type-one',
  TYPE_TWO = 'type two',
  typeThree = 'Type three'
}
```

:x: Not this:
```ts
enum SomeEnum {
  VALUE_ONE,
  VALUE_TWO,
  VALUE_THREE
}
```


