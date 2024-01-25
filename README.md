# Classic Criteria

Дает объект-критерий.

Нужен для описания критериев объектов, чтобы затем определять,
соответствуют ли те или иные объекты критерию, или для формирования запроса,
к примеру, в SQL-хранилище.

Пример:
```python
from datetime import datetime
from dataclasses import dataclass
from classic.criteria import Criteria


@dataclass
class Task:
    created_at: datetime
    finished_at: datetime

    
@dataclass
class TaskOlderThan(Criteria[Task]):
    date: datetime

    def is_satisfied_by(self, candidate: Task) -> bool:
        return candidate.created_at < self.date

    
@dataclass
class TaskObsolete(Criteria[Task]):
    days_to_work: int

    def is_satisfied_by(self, candidate: Task) -> bool:
        days_spent = candidate.finished_at - candidate.created_at
        return days_spent.days > self.days_to_work

    
some_task = Task(
    created_at=datetime(2024, 1, 1),
    finished_at=datetime(2024, 1, 10),
)
criteria = TaskObsolete(3) & TaskOlderThan(datetime(2024, 1, 31))
assert criteria.is_satisfied_by(some_task)


some_task = Task(
    created_at=datetime(2024, 1, 1),
    finished_at=datetime(2024, 1, 1),
)
criteria = TaskObsolete(3) & TaskOlderThan(datetime(2024, 1, 31))
assert not criteria.is_satisfied_by(some_task)
```
