### python上传文件
```python
# app/common/upload_file.py

import os
import time

from app.common.exceptions import InvalidAPIRequest
from constant import FILE_MAX_SIZE, ALLOWED_EXTENSIONS, UPLOAD_FOLDER

def allowed_file(filename):
    return '.' in filename and \
           filename.rsplit('.', 1)[1] in ALLOWED_EXTENSIONS


def upload_file(file):
    if file and allowed_file(file.filename):
        if file.content_length < FILE_MAX_SIZE:
            suffix = file.filename.rsplit('.', 1)[1]
            save_file = os.path.join(UPLOAD_FOLDER, "{0}.{1}".format(int(time.time()), suffix))
            # file.stream.seek(0)  # 将文件指针返回到文件开头
            file.save(save_file)
            return save_file
        else:
            raise InvalidAPIRequest("Too big a document")
    else:
        raise InvalidAPIRequest("The file format is not supported")

下载文件
send_file(filename_or_fp, as_attachment=True)
```
