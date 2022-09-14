
 [Track changes in Excel, Word, PowerPoint, PDFs, Images & Videos with Git](https://tech.marksblogg.com/git-track-changes-in-media-office-documents.html)

# 原理
若需要 `git` 管理版本，则必须有工具帮助 `git` 判断版本差异。对于纯文本文档，`git` 自然可以直接判断，但对于 office 文档，系统默认是将其作为二进制文档，所以`git` 必须借助第三方工具。

比如，如果没有借助第三方工具，默认对`mp4`文档进行比较时，会出现：

```bash
git diff test.mp4
```
> diff --git a/test.mp4 b/test.mp4
> index 9a9efb7..d9607c6 100644
> Binary files a/test.mp4 and b/test.mp4 differ

# 安装第三方工具
## 系统工具
```bash
brew install \
	exiftool \
	git \
	pandoc \
	virtualenv
```

## Python 虚拟环境
```bash
virtualenv ~/.officecomp
source ~/.officecom/bin/activate
python3 -m pip install \
	openpyxl \
	Pandas \
	python-pptx \
	textract
```

# 比较 office 文档
## 建立 `git` 环境
```bash
git init
```
```bash
vi .gitattribures
```
```
*.docx diff=word
*.pptx diff=powerpoint
*.xlsx diff=excel
*.pdf diff=pdf
*.jpg diff=images_videos
*.png diff=images_videos
*.gif diff=images_videos
*.mp4 diff=images_videos
```
```bash
vi .git/config
```
```
[core]
    repositoryformatversion = 0
    filemode = true
    bare = false
    logallrefupdates = true
    ignorecase = true

[alias]
    wdiff = diff --word-diff=color --unified=1

[diff "word"]
    textconv=pandoc --to=markdown
    binary=true
    prompt=false

[diff "excel"]
    textconv=python3 /Users/fengh/.officecomp/bin/xlsx-dump.py
    binary=true

[diff "powerpoint"]
    textconv=python3 /Users/fengh/.officecomp/bin/pptx-dump.py
    binary=true

[diff "pdf"]
    textconv=python3 /Users/fengh/.officecomp/bin/pdf-dump.py
    binary=true

[diff "images_videos"]
    textconv=exiftool
  binary=true
```
也可以把这些内容直接 `copy` 到 `~/.gitconfig` ，便于所有仓库使用。

## 建立 `diff` 命令

对于 docx 文档，`pqndoc`  就可以直接处理。但对于 pptx, xlsx, pdf 等，还需要借助外部命令。

### pptx
```bash
vi pptx-dump.py
```
```python
import sys

import collections 
import collections.abc

from pptx import Presentation

for slide in Presentation(sys.argv[1]).slides:
    for shape in slide.shapes:
        if not shape.has_text_frame:
            continue

        for paragraph in shape.text_frame.paragraphs:
            for run in paragraph.runs:
                print(run.text)
```
### xlsx
```bash
vi xlsx-dump.py
```

```python
from io import StringIO
import sys

import pandas as pd

for sheet_name in pd.ExcelFile(sys.argv[1]).sheet_names:
    output = StringIO()
    print('Sheet: %s' % sheet_name)
    pd.read_excel(sys.argv[1], sheet_name=sheet_name)\
      .to_csv(output,
              header=True,
              index=False)
    print(output.getvalue())
```

### pdf
```bash
vi pdf-dump.py
```

```python
import sys

import textract

print(textract.process(sys.argv[1]).decode('utf-8'))
```