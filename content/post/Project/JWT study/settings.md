settings.py 의 SIMPLE_JWT 상수 설정의 의미들

https://django-rest-framework-simplejwt.readthedocs.io/en/latest/settings.html#access-token-lifetime

```python
from datetime import timedelta

SIMPLE_JWT = {
    'ACCESS_TOKEN_LIFETIME': timedelta(hours=2),
    'REFRESH_TOKEN_LIFETIME': timedelta(days=1),
    'ROTATE_REFRESH_TOKENS': False,
    'BLACKLIST_AFTER_ROTATION': True,
    'TOKEN_USER_CLASS': 'pocket.User',
}
```