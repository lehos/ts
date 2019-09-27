# TypeScript tips & tricks


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

## Make union of values from enum-like object
```typescript
const roles = {
  User: 'ROLE_USER',
  Admin: 'ROLE_ADMIN',
} as const

type Roles = typeof roles
type RoleValues = Roles[keyof Roles]
// ''ROLE_USER' | 'ROLE_ADMIN'
```


## Recursive partial
```typescript
type RecursivePartial<T> = {
  [P in keyof T]?:
    T[P] extends (infer U)[] ? RecursivePartial<U>[] :
    T[P] extends object ? RecursivePartial<T[P]> :
    T[P];
};
```


## Convert Array to Record

```typescript
function arrayToRecord<T extends { id: string }>(obj: T[]): Record<string, T> {
  return obj.reduce((acc: {[key:string]: T}, cur) => {
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

##  Fields of type

```typescript
type FieldsOfType<S, T> = { [K in keyof S]: S[K] extends T ? K : never }[keyof S];

// usage
type Foo = {
  bar: string;
  baz: number;
  xyz: string;
}

type Bar = FieldsOfType<Foo, string> 
// 'bar' | 'xyz'
```


## Branded types
```typescript
type Brand<K, T> = K & { __brand: T };

type Foo = Brand<string, 'Foo'>;
type Bar = Brand<string, 'Bar'>;

// Foo is not assignable to Bar
```
see [Nominal typing in TS](https://michalzalecki.com/nominal-typing-in-typescript/)

## Common types

```typescript
// https://github.com/Microsoft/TypeScript/issues/14094#issuecomment-373782604
type Without<T, U> = { [P in Exclude<keyof T, keyof U>]?: never };
type XOR<T, U> = (T | U) extends object 
    ? (Without<T, U> & U) | (Without<U, T> & T) 
    : T | U;

// make one field optional 
type PartialBy<T, K extends keyof T> = Omit<T, K> & Partial<Pick<T, K>>
```

## Props dependent on other props (React)
[Playground](http://www.typescriptlang.org/play/index.html?jsx=2#code/JYWwDg9gTgLgBAJQKYEMDG8BmUIjgcilQ3wG4AocmATzCTgFkUBnAazgF45mYpgA7AOZwAPnAAU4gJScAfHBT9qUilVr0ACjjDMAPAzhIAHjCT8AJs0YtW8rgG9ycOCBsAuRhWcBXfq7YA-B4ARhAQADao-BQAvnAAZBIGxqYWVgBy3iDBSFAAwhD8PFDeGNBwAXD2cPxZOVBaEGBB3LwCwnEe9jEqlJi+GMCFcAXg+oYmZpbWbLLiYNrMHo06+rIyjs5EMN5Q-HC6sgAWSOHhELoA9LKxfQMwQ-sAKkg8o2DSVU5w27v74t9nIdAc4Du8XDYOPZMtlcnFarCGtoOPhguEUPg4NcQUDwf5WFCYfV4XVcisUWiMXAAO44IQrLGyHFg3BgCFsQmkqAkxEM7Gg0G6PGQ+yfDjyTAocLMJA8+p8pkC3Gs9kE+z4Zi4ejFdr4OVk7SM5lClX4qEarWtPhCPVGgVXRVwXoxIA)

```typescript jsx
type Mask = string | (() => any);

type Props<M extends Mask> = {
  mask: M;
  unmask?: boolean;
} & (M extends NumberConstructor ? { numberProp?: string } : {});

function Comp<M extends Mask>(props: Props<M>) {
  return <>hello</>;
}

function TestComp() {
  return (
    <>
      <Comp mask={Number} numberProp='bla' /> // ok
      <Comp mask={Number} numberProp='bla' wrongProp /> // err, unknown prop
      <Comp mask={Number} numberProp /> // err, numberProp wrong type
      <Comp mask={() => false} numberProp /> // err, numberProp only for Number
      <Comp mask={'some string'} numberProp /> // err, numberProp only for Number
      <Comp mask={'some string'} /> // ok
    </>
  )
``` 
