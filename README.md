# NCU Map

## DATA格式
JSON 在/src/assets 

```
格式
{
  "name": "",
  "intro": "...",
  "tag": []
  "classify" : []
  "pic" : "" #包含圖片的相對路徑 可以直接用 vue記得加@/
  "Position": {
      "x": ...,  #背景圖片為900*600 左上為(0,0) 該座標紀錄大致位置 
      "y": ...
    }
}

# classify 為0-3 預設是 0:食物 1:系所 2:宿舍 3:行政 分類按鈕可以用
```
