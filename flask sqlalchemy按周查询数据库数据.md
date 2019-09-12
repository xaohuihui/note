## flask sqlalchement按周查询数据库数据

```python
from datetime import timedelta
from datetime import datetime

c = datetime.datetime(2019, 1, 1, 2, 27, 15, 706351)
int(c.strftime('%W'))   # 0, 2019年第0周

c.weekday()   # 1， 周2，周一为0

## 数据库查询第几年第几周数据
 a = Leak.query.filter(extract('week', Leak.create_time)==10, extract('year', Leak.create_time)==2019).all()

from sqlalchemy import func

Leak.query.with_entities(
func.count(Leak.id),
extract('year', Leak.create_time),
extract('week', Leak.create_time)
).group_by(
extract('year', Leak.create_time),
extract('week', Leak.create_time)
).order_by(
extract('year', Leak.create_time)
).order_by(
extract('week', Leak.create_time)
).all()
```
