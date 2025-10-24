---
title: "Rを使ってicalファイルのタイムゾーンを修正する"
date: 2021-09-30T01:14:30+09:00
draft: true
tags: ["R","ical","google","calendar","カレンダー"]
---

### 背景

Googleカレンダーの整理をしていたら，カレンダー内の予定がUTCとなっていた．
カレンダー表示のときには通常通りJSTの時間で表示されているが，予定の詳細を見たり編集する際にUTCとなっている．

手作業で直す場合，タイムゾーンを変えても時間を表す数字は変わらないので，+9:00する必要がある．
そもそも件数が多いのでやっていられない．

色々と探った結果，ical形式でエクスポートすると，カレンダーの設定によらずUTCで出力されることが判明した．フィードバックは送ったが，待っていられないし，そもそも仕様の可能性もある．

そのため，以下のようなRスクリプトを書いて，icalを修正することにした．

### 成果物

```r
if (!require("pacman")) install.packages("pacman"); library(pacman)
p_load(tidyverse)
setwd("icalファイルの場所")

# ical（拡張子はics）ファイルを文字列ベクトルで読み込み
data <- read_lines("Calendar.ics")

# 修正対象はDTSTART,DTEND,DTSTAMP,CREATED,LAST-MODIFIED
## lubridateパッケージのsecond minute hour day month yearを使って最初に"TZID=Asia/Tokyo:"以下の部分を作っておいて，その後にmutateする方が良いと思うが，直す気力がない．
dat <- tibble(raw=data, jst=with_tz(ymd_hms(str_sub(data,9)), "Asia/Tokyo")) %>% 
  mutate(DTSTART= paste0("DTSTART;TZID=Asia/Tokyo:",str_sub(jst,1,4),str_sub(jst,6,7),str_sub(jst,9,10),"T",str_sub(jst,12,13),str_sub(jst,15,16),str_sub(jst,18,19)), 
         DTEND= paste0("DTEND;TZID=Asia/Tokyo:",str_sub(jst,1,4),str_sub(jst,6,7),str_sub(jst,9,10),"T",str_sub(jst,12,13),str_sub(jst,15,16),str_sub(jst,18,19)), 
         DTSTAMP= paste0("DTSTAMP;TZID=Asia/Tokyo:",str_sub(jst,1,4),str_sub(jst,6,7),str_sub(jst,9,10),"T",str_sub(jst,12,13),str_sub(jst,15,16),str_sub(jst,18,19)), 
         CREATED = paste0("CREATED;TZID=Asia/Tokyo:",str_sub(jst,1,4),str_sub(jst,6,7),str_sub(jst,9,10),"T",str_sub(jst,12,13),str_sub(jst,15,16),str_sub(jst,18,19)), 
         "LAST-MODIFIED"= paste0("LAST-MODIFIED;TZID=Asia/Tokyo:",str_sub(jst,1,4),str_sub(jst,6,7),str_sub(jst,9,10),"T",str_sub(jst,12,13),str_sub(jst,15,16),str_sub(jst,18,19)))

out <- ifelse(str_detect(dat$raw, pattern="DTSTART:2"),dat$DTSTART,  # カレンダーの設定情報である"DTSTART:19700101T000000"を除外するため"DTSTART:2"とする
            ifelse(str_detect(dat$raw, pattern="DTEND:"),dat$DTEND,
                   ifelse(str_detect(dat$raw, pattern="DTSTAMP:"),dat$DTSTAMP,
                   ifelse(str_detect(dat$raw, pattern="CREATED:"),dat$CREATED,
                   ifelse(str_detect(dat$raw, pattern="LAST-MODIFIED:"),dat$"LAST-MODIFIED",
                          dat$raw)))))

# ファイルに書き出し
write_lines(out, "FixedCalendar.ics", na = "NA", append = FALSE)
```

同じ事象に悩む方のお役に立てば幸いです．
