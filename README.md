# TypeScript tips & tricks

## Common types

```typescript
// temp, waiting for TS 3.5
type Omit<T, K extends keyof T> = Pick<T, Exclude<keyof T, K>>

// https://github.com/Microsoft/TypeScript/issues/14094#issuecomment-373782604
type Without<T, U> = { [P in Exclude<keyof T, keyof U>]?: never };
type XOR<T, U> = (T | U) extends object 
    ? (Without<T, U> & U) | (Without<U, T> & T) 
    : T | U;

// make one field optional 
type PartialBy<T, K extends keyof T> = Omit<T, K> & Partial<Pick<T, K>>
```

## Convert Array to Record

```typescript
function arrayToRecord<T extends { id: string }>(obj: T[]): Record<string, T> {
  return obj.reduce((acc, cur) => {
    acc[cur.id] = cur
    return acc
  }, {})
}


// usage

interface Obj {
  id: string
  foo: string
}

const objArr: Obj[] = [
  { id: '1', foo: 'bar' },
  { id: '2', foo: 'baz' }
]

const objRec = arrayToRecord(objArr)
// Record<string, Obj>
```

## Make type with keys from union of strings
```typescript
type Keys = 'foo' | 'bar' | 'baz'

type Obj = {[key in Keys]: any}
// {
//   foo: any
//   bar: any
//   baz: any
// }
```


## Make type from object and type from object keys
```typescript
const obj = {
  foo: 1,
  bar: 'hello',
  baz: true,
}

type ObjType = typeof obj
// {
//   foo: number
//   bar: string
//   baz: boolean
// }


type Keys = keyof ObjType
// or
type Keys = keyof typeof obj
// 'foo' | 'bar' | 'baz'
```


## Pick Type From Union
```typescript
type PickFromUnion<T, K> = Extract<T, { kind: K }>;


// usage

type Foo = {
    kind: 'foo'
    prop: string
}

type Bar = {
    kind: 'bar',
    prop: number;
}

type PickedBar = PickFromUnion<Foo | Bar, 'bar'>
// {
//   kind: 'bar',
//   prop: number;
// }
``` 


## Make union of values from enum-like object
```typescript
const roles = {
  User: 'ROLE_USER' as const,
  Admin: 'ROLE_ADMIN' as const,
} 

type Roles = typeof roles
type RoleValues = Roles[keyof Roles]
// ''ROLE_USER' | 'ROLE_ADMIN'
```


## Branded types
```typescript
type Brand<K, T> = K & { __brand: T };

type Foo = Brand<string, 'Foo'>;
type Bar = Brand<string, 'Bar'>;

// Foo is not assignable to Bar
```
