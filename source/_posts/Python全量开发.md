---
title: Python全量开发
date: 2026-10-01 10:31:19
tags: Python全量开发
excerpt : Python全量开发

---

py 脚本

deep-translator + Google 免费接口 实现上千个日语单词的翻译

```py
import json, time, html
from deep_translator import GoogleTranslator

translator = GoogleTranslator(source="en", target="zh-CN")

with open("n2.json", encoding="utf-8") as f:
    data = json.load(f)

cache = {}
out = []

for i, item in enumerate(data, 1):
    m = html.unescape(item.get("meaning", "")).replace("\xa0", " ").strip()
    if m not in cache:
        for _ in range(3):
            try:
                cache[m] = translator.translate(m)
                break
            except Exception:
                time.sleep(1)
        else:
            cache[m] = ""
        time.sleep(0.1)
    new = dict(item)
    new["meaning_zh"] = cache[m]
    out.append(new)
    if i % 100 == 0:
        print(i, "/", len(data))

with open("n2_zh.json", "w", encoding="utf-8") as f:
    json.dump(out, f, ensure_ascii=False, indent=2)

```
