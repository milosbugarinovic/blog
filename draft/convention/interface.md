# Interface

Try not to mark interfaces specially (IMyInterface or MyFooInterface). Use PascalCase
Try naming interface with adding the able to the end of the interface name if the functionality is highly reusable :wink:

```ts
export interface Serializable {}
export interface Clonable {}
export interface Iterable {}
```

When naming an interface for the strategy pattern just add Strategy :man_shrugging:

```ts
export interface LogInStrategy {}
export class TwoFactorAuthLogInStrategy implements LogInStrategy {}
export class SimpleLogInStrategy implements LogInStrategy {}
export class SSOLogInStrategy implements LogInStrategy {}
```
And if you need to create an interface to implement DAL and there is no better name for it, you can call it `ICommonDal`.
