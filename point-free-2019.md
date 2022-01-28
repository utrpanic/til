# [Episode #42 The Many Faces of Flat‑Map: Part 1](https://www.pointfree.co/episodes/ep42-the-many-faces-of-flat-map-part-1)
- Map이나 Zip을 쓸 수도 있겠지만...
- Nesting problem. map 블록 내에서 map을 반복할 때마다.
- 그래서 flatMap.

# [Episode #43 The Many Faces of Flat‑Map: Part 2](https://www.pointfree.co/episodes/ep43-the-many-faces-of-flat-map-part-2)
```
// Array 
// map:        ((A)    ->  C ) -> (([A])      -> [C])
// zip(with:): ((A, B) ->  C ) -> (([A], [B]) -> [C])
// flatMap:    ((A)    -> [C]) -> (([A])      -> [C])

// Optional
// map:        ((A)    -> C ) -> ((A?)     -> C?)
// zip(with:): ((A, B) -> C ) -> ((A?, B?) -> C?)
// flatMap:    ((A)    -> C?) -> ((A?)     -> C?)

// Result
// map:        ((A)    ->        C    ) -> ((Result<A, E>)               -> Result<C, E>)
// zip(with:): ((A, B) ->        C    ) -> ((Result<A, E>, Result<B, E>) -> Result<C, E>)
// flatMap:    ((A)    -> Result<C, E>) -> ((Result<A, E>)   
```
- map: Purely transform the underlying type of your generic container.
- zip: Combine multiple generic container values into a single generic container.
- flatMap: Sequence your generic containers.