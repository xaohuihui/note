## postgresql更改用户并删除

>ERROR: role "ryan" cannot be dropped because some objects depend on it
DETAIL: privileges for database mydatabase

解决办法：
```bash
REASSIGN OWNED BY ryan TO postgres;
DROP OWNED BY ryan;
DROP USER ryan;
```
