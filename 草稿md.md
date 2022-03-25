```python
class CacheHandler:
    """
    A Cache Handler to manage access to Cache instances.

    Ensure only one instance of each alias exists per thread.
    """
    def __init__(self):
        self._caches = local()

    def __getitem__(self, alias):
        try:
            return self._caches.caches[alias]
        except AttributeError:
            self._caches.caches = {}
        except KeyError:
            pass

        if alias not in settings.CACHES:
            raise InvalidCacheBackendError(
                "Could not find config for '%s' in settings.CACHES" % alias
            )

        cache = _create_cache(alias)
        self._caches.caches[alias] = cache
        return cache

    def all(self):
        return getattr(self._caches, 'caches', {}).values()

```

