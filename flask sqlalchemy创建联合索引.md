## flask sqlalchemy 创建联合索引

```python
__table_args__ = (
       UniqueConstraint('ip', 'source_id', name='ix_grayips_ip_source_id'),
   )
```
