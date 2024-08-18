---
author: mei
title: 关于本站
date: 2024-02-24 12:02:27
permalink: /aboutsite.html
---
本站使用Hexo的fluid主题渲染 </br>
如果你也想使用Hexo渲染Linux中国的数据集,你需要:
* 一台使用Linux系统的电脑(虚拟机也行),内存大约5G就行
* 一颗性能较强的CPU
翻看微软文档及实际测试后发现，在windows系统下，你无法使用Hexo渲染如此大量的数据,如果你的CPU性能过于拉跨，也请不要尝试,可能会需要数个小时的等待才能渲染完成,本站使用149kf渲染大约用了15分钟,加上传文件和删删改改耗费了近一天的时间~~Hexo性能太烂了~~
## 修改数据
头图和大图
```shell
# 开启bash扩展功能globstar，使得**能匹配任意级目录下的文件
shopt -s globstar
sed 's|largepic|banner_img|g' -i **/*.md
sed 's|pic|index_img|g' -i **/*.md
```

如果没有头图:
```python
import os
import re

def replace_content(dir_path):
    for root, dirs, files in os.walk(dir_path):
        for file in files:
            if file.endswith('.md'):
                file_path = os.path.join(root, file)
                
                with open(file_path, 'r+', encoding='utf-8') as f:
                    content = f.read()
                    
                    # 查找并提取 banner_img 后面的内容
                    match_banner = re.search(r'banner_img:\s*(.*)', content, re.IGNORECASE | re.MULTILINE)
                    if match_banner:
                        banner_img = match_banner.group(1).strip()  # 去除两边空白字符
                        
                        # 查找并替换 index_img 后面的内容
                        match_index = re.search(r'index_img:\s*(.*)', content, re.IGNORECASE | re.MULTILINE)
                        if match_index and 'islctt: false' in content:
                            new_content = re.sub(r'index_img:\s*.*?\n', f'index_img: {banner_img}\n', content, flags=re.IGNORECASE | re.MULTILINE)
                            
                            # 替换文件内容
                            f.seek(0)
                            f.truncate()
                            f.write(new_content)

# 调用函数，指定要遍历的目录
replace_content('_posts')
```

如果渲染是出现`Error: EMFILE, too many open files`报错,执行
```shell
ulimit -n 10000
```
如果还报错就把这个数改大点      

如果
```
ATAL ERROR: CALL_AND_RETRY_LAST Allocation failed - JavaScript heap out of memory
```
可以使用 -c 参数限制构建的线程数    
例如`hexo g -c 8`

## 参考资料
[User Objects - Win32 apps | Microsoft Learn](https://learn.microsoft.com/en-us/windows/win32/sysinfo/user-objects)
[Home · Linux-CN/archive Wiki (github.com)](https://github.com/Linux-CN/archive/wiki)

在此感谢Linux中国的全体贡献者,感谢他们创作/翻译了这么多优质的内容