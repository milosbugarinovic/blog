# [DRAFT] [msh-util](https://github.com/beecode-rs/msh-util) `v0.1.0`

`msh-util` is a part of a larger collection of useful tools
which I personally use in all my professional and personal projects

This library is a collection of useful functionalities that can be used across multiple projects (frontend, backend and mobile if
all projects are writer in typescript)

Util is a bit generic name and it all makes sense when you take a look at the functionalities that it provides. Actual it is a
collection of various functionalities that are to small to be exported in a separate library. If at any time some similar
functionality get a bit lager we will extract them into separate library.

# Tool list

<!-- toc -->

- [timeout](#timeout)
- [singleton/patter](#singletonpatter)
- [singleton/async](#singletonasync)
- [single-threshold-promise](#single-threshold-promise)
- [memoize-factory](#memoize-factory)
- [object-util](#object-util)
  * [deepClone](#deepclone)
  * [pickByList](#pickbylist)
  * [pickByObjectKeys](#pickbyobjectkeys)
  * [stringifySortOrNullOrUndefined](#stringifysortornullorundefined)
  * [deepEqual](#deepequal)
  * [deepNullToUndefined](#deepnulltoundefined)
- [regex-util](#regex-util)
  * [uuid](#uuid)
- [string-util](#string-util)
  * [generateUUID](#generateuuid)
- [time-util](#time-util)
  * [now()](#now)
  * [dateToUnix](#datetounix)
  * [dateToUnixSec](#datetounixsec)
  * [unixToDate](#unixtodate)
  * [unixSecToDate](#unixsectodate)
  * [addToDate](#addtodate)
- [type-util](#type-util)
  * [exhaustiveMessage](#exhaustivemessage)
  * [exhaustiveError](#exhaustiveerror)
- [class-factory-patten](#class-factory-patten)
- [joi-util](#joi-util)
- [express/error-handler](#expresserror-handler)

<!-- tocstop -->

## timeout

This is a promisified `setTimeout` function which is used if we need to make some delay in our code

```ts
const lightBlink = (): void => {
  turnLightOn()
  timeout(3000) // wait for 3 seconds
  turnLightOff()
  timeout(3000) // wait for 3 seconds
  turnLightOn()
}
```

## singleton/patter

Because singleton is considered as anti-pattern and makes testing classes harder we created simple function that wrappers around
the function. When the singleton wrapper function is called it executes the function that is passed as a parameter only once, and
the value is cached and returned. Second, or any other call is only returning the cached value.

```ts
export class SomeClass {
  constructor(protected _param: string) {
  }

  get param(): string {
    return this._param
  }
}

export const someClassSingleton = singletonPattern((): SomeClass => {
  return new SomeClass('some param value')
})

// using
console.log('param: ', someClassSingleton().param) // param: some param value
```

Or we can use it with simple function

```ts
export const config = singletonPattern(() => {
  return {
    env: process.NODE_ENV,
  } as const
})

// using 
console.log('NODE_ENV: ', config().env) // NODE_ENV: prod
```

## singleton/async

SingletonAsync is similar to singletonPatter but a bit more complex. Main difference is that SingletonAsync accepts async function
and returns a promise, but it caches the result in the same way singletonPattern does. Here we also have more advanced features if
needed that can clean cache which cleans the cache and rejects all subscribers if there are any (this can happen only if the
initial promise was not resolved and we decided to clean cache).

```ts
export const configSingleton = new SingletonAsync(async () => {
  await timeout(3000)
  return {
    env: process.env.NODE_ENV
  } as const
})

// using
// cache value before we call promise
console.log(configSingleton().cache()) // undefined 
console.log('NODE_ENV: ', await configSingleton().promise().env) // NODE_ENV: prod
// cache value after we call promise
console.log(configSingleton().cache()) // { env: 'prod' }
```

## single-threshold-promise

SingleThresholdPromise returns a single promise, and subsequent calls made before the promise resolves will return the same
promise.

```ts
export const refreshTokenSingleThreshold = new SingleThresholdPromise(async () => {
  const oldRefreshToken = await refreshTokenService.get()
  const { accessToken, refreshToken } = await authService.refreshToken({
    refreshToken: oldRefreshToken,
  })
  return { accessToken, refreshToken }
})

export const authService = {
  refreshToken: async (): Promise<{ accessToken: string; refreshToken: string }> => {
    return refreshTokenSingleThreshold.promise()
  }
}
```

In this example we can call multiple time `authService.refreshToken()` and if the promise function is not finished before we make
all calls, all the calls are going to receive the same promise when it is resolved.

## memoize-factory

This is a simple implementation of memoize function that caches result against the parameter passed that are passed to the
function so it never runs the same calculation twice.

```ts
export const sumTwoNumbersMemoize = memoizeFactory((a: number, b: number): number => a + b)

// using
sumTwoNumbersMemoize(5 + 10) // 15
```

## object-util

Here you can find a collection of useful function that work with objects.

### deepClone

Here we didn't wanted to reinvent the wheal so we just used lodash.clonedeep :smile:

### pickByList

Pick only properties from the property list. It is only allowed to pick properties from the first level

```ts
console.log(new ObjectUtil().pickByList({ a: 1, b: 2, c: 3 }, ['a', 'b'])) // { a: 1, b: 2 }
```

### pickByObjectKeys

Pick objects properties using keys from the second object.

```ts
console.log(new ObjectUtil().pickByObjectKeys({ a: 1, b: 2, c: 3 }, { a: '', b: 'it is not importath what the value is' })) // { a: 1, b: 2 }
```

### stringifySortOrNullOrUndefined

This function will do stringify deeper that JSON.stringify. If the object that you pass is null or undefined it will return that
value (null or undefined) otherwise it will return string

```ts
console.log(new ObjectUtil().stringifySortOrNullOrUndefined(null)) // null
console.log(new ObjectUtil().stringifySortOrNullOrUndefined(undefined)) // undefined
console.log(new ObjectUtil().stringifySortOrNullOrUndefined({ a: 1 })) // '{\n\ta: 1\n}'
// `{
//   a:1
// }`
```

### deepEqual

We are converting objects to string (or null or undefined) and comparing if the result is equal. Under the hood we are
using `stringifySortOrNullOrUndefined`

### deepNullToUndefined

This function is going to convert any null to undefined in the object that is passed to it.

```ts
console.log(new ObjectUtil().deepNullToUndefined({ a: null, b: { c: null } })) // { a: undefined, b: { c: undefined } }
```

## regex-util

### uuid

This is a UUID regex expression. This is usually used in express router to constrict the values passed as a path parameter (if you
are using UUID as your identifier).

```ts
const uuid = regexUtil.uuid()
router.route(`/users/:userId(${uuid})`).get(getUsersById)
//...
```

## string-util

### generateUUID

Generate random UUID

```ts
console.log(stringUtil.generateUUID()) // "69bfda25-df3f-46b4-8bbb-955cf5193426"
```

## time-util

We are currently using `date-fns` for handling time.

### now()

Simple wrapper around `new Date()` so if we need to change this at any time we can do it in one place.

```ts
console.log(new TimeUtil().now().toISOString()) // 2023-03-08T19:45:01.991Z
```

### dateToUnix

Convert date object to unix timestamp (milliseconds)

```ts
// timeUtil.now().toISOString() === 2023-03-08T19:45:01.991Z
const timeUtil = new TimeUtil()
console.log(timeUtil.dateToUnix(timeUtil.now())) // 1678304701991
```

### dateToUnixSec

Convert date object to unix timestamp (seconds)

```ts
// timeUtil.now().toISOString() === 2023-03-08T19:45:01.991Z
const timeUtil = new TimeUtil()
console.log(timeUtil.dateToUnix(timeUtil.now())) // 1678304701
```

### unixToDate

Convert unix timestamp (milliseconds) to date object

```ts
const timeUtil = new TimeUtil()
console.log(timeUtil.unixToDate(1678304701991).toISOString()) // 2023-03-08T19:45:01.991Z
```

### unixSecToDate

Convert unix timestamp (seconds) to date object

```ts
const timeUtil = new TimeUtil()
console.log(timeUtil.unixToDate(1678304701).toISOString()) // 2023-03-08T19:45:01.000Z
```

### addToDate

Change the value of date by unit/value pare.

```ts
// timeUtil.now().toISOString() === 2023-03-08T19:45:01.991Z
const timeUtil = new TimeUtil()
console.log(timeUtil.addToDate({ date: timeUtil.now(), unit: 'DAY', value: 1 }).toISOString()) // 2023-03-09T19:45:01.991Z
console.log(timeUtil.addToDate({ date: timeUtil.now(), unit: DurationUnit.MONTH, value: -1 }).toISOString()) //2023-02-08T19:45:01.991Z
```

## type-util

### exhaustiveMessage

In TypeScript, exhaustiveMessage is a technique that can be used with switch statements to ensure that all possible cases are
handled.

When using switch statements, it is common to have a default case that handles any unanticipated cases. However, sometimes it is
important to ensure that all cases are explicitly handled to avoid potential errors or bugs in the code.

```ts
export type Animal = 'cat' | 'dog' | 'bird';

export const makeSound = (animal: Animal): string => {
  switch (animal) {
    case 'cat':
      return 'Meow'
    case 'dog':
      return 'Woof'
    case 'bird':
      return 'Tweet'
    default:
      console.error(typeUtil.exhaustiveMessage('Unknown animal [animal]', animal))
      return 'unknown sound'
  }
}
```

### exhaustiveError

This is the similar to exhaustiveMessage, but instead of message we are returning error so we can throw it

```ts
export type Animal = 'cat' | 'dog' | 'bird';

export const makeSound = (animal: Animal): string => {
  switch (animal) {
    case 'cat':
      return 'Meow'
    case 'dog':
      return 'Woof'
    case 'bird':
      return 'Tweet'
    default:
      throw typeUtil.exhaustiveError('Unknown animal [animal]', animal)
  }
}
```

## class-factory-patten

This is a wrapper that easily converts class constructor call (`new className(..constructorParams)`) into function
call (`classNameFactory(..constructorParams)`)

```ts
export class SomeClass {
  protected _a: string

  constructor(params: { a: string }) {
    const { a } = params
    this._a = a
  }
}

export const someClassFactory = classFactoryPattern(SomeClass)

// using
const someClassInstance = someClassFactory({ a: 'test' })
```

## joi-util

This is a simple wrapper around Joi validation with two functions exposed validate and sanitize. If object is not valid function
throws an error.

```ts
type SomeType = {
  a: string
  b: number
}
const someSchema = Joi.object<SomeType>().keys({
  a: Joi.string().required(),
  b: Joi.number().required(),
}).required()

const joiUtil = new JoiUtil()

// using
const invalidObject = joiUtil.validate({}, someSchema) // validate throws an error
const validObject = joiUtil.validate({ a: 'a', b: 1 }, someSchema)
```

## express/error-handler

If you are using express and your controller function is async, this function will help you catch errors and pass them to `next`
function of express. You can only use this decorator if your controller function is a part of class.

```ts
export class RootController {
  @expressErrorHandler
  async login(req: Request, res: Response): Promise<void> {
    const { username, password } = validationUtil().sanitize(req.body, postLoginBodySchema)
    const result = await authorizationUseCase.login({ username, password })
    res.json(result)
  }
}
```

