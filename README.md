# TypeScript tips & tricks

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

// objRec has type Record<string, Obj>
const objRec = arrayToRecord(objArr)
```

## Make type with keys from union of strings
```typescript
type Keys = 'foo' | 'bar' | 'baz'

type Obj = {[key in Keys]: any}

// now Obj is like:
type Obj = {
  foo: any
  bar: any
  baz: any
}
```


## Make type from object and type from object keys
```typescript
const obj = {
  foo: 1,
  bar: 'hello',
  baz: true,
}

type Obj = typeof obj

// now Obj type is:
type Obj = {
  foo: number
  bar: string
  baz: boolean
}


type Keys = keyof Obj
// or
type Keys = keyof typeof obj

// now Keys type is:
type Keys = 'foo' | 'bar' | 'baz'

```
