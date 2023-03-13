# [DRAFT] [msh-entity](https://github.com/beecode-rs/msh-entity) `v0.1.0`

## entity-cache

> This section is a candidate for moving util functionality to it's own module/library.

In this section we use memory as a DAL for the entities we want to cache. The way we get the data from cached entity is by
subscribing to an entityId. When we subscribe get the entity from cache, if there is no entity in the cache the entity is required
by using `_entityAsync` call that needs to be defined. If there is a cached value but it has expired, the data is refreshed again
using `_entityAsync` call. Once the entity is refreshed, if there are any subscribers listening to the same id the fresh new
object is going to be sent to them.
