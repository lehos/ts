# TypeScript tips & tricks

## Convert Array to Record

```typescript
function arrayToRecord<T extends { id: string }>(obj: T[]): Record<string, T> {
  return obj.reduce((acc, cur) => {
    acc[cur.id] = cur;
    return acc;
  }, {});
}


// usage

interface Obj {
  id: string
  foo: string
}

const objArr: Obj[] = [
  {
    id: '1',
    foo: 'bar'
  },
  {
    id: '2',
    foo: 'baz'
  }
]


// obj has type Record<string, Obj>
const objRec = arrayToRecord(objArr);
```
