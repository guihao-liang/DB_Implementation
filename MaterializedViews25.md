# 4/18/2016

```
CREATE VIEW V AS
        SELECT *
        FROM ...
        WHERE ...
```

```
SELECT * FROM V
```

Physically store the resulting table, like cache.

Add extra info `COUNT` when query uses keyword `DISTINCT`.
