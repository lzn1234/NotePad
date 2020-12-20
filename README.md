# 期中项目 NOTEPAD

## 一、实现时间戳
### 修改代码部分
1.添加映射
```java
private static final String[] PROJECTION = new String[] {
            NotePad.Notes._ID, // 0
            NotePad.Notes.COLUMN_NAME_TITLE, // 1
            NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE, //3
            NotePad.Notes.COLUMN_NAME_TAG //4
    };
```
2.修改适配器为simpleAdapter,并对取出的事件戳数据进行修改
```java
//便利Cursor并处理数据放入容器中
           listItems = new ArrayList<>();
           for(cursor.moveToFirst();!cursor.isAfterLast();cursor.moveToNext())
   
           {
               int idColumn = cursor.getColumnIndex(NotePad.Notes._ID);
               int titleColumn = cursor.getColumnIndex(NotePad.Notes.COLUMN_NAME_TITLE);
               int dateColumn = cursor.getColumnIndex(NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE);
   
               int id = cursor.getInt(idColumn);
               String title = cursor.getString(titleColumn);
               String date =  timeTransfer(cursor.getString(dateColumn));
   
               Map<String,Object> listItem = new HashMap<>();
               listItem.put(NotePad.Notes.COLUMN_NAME_TITLE,title);
               listItem.put(NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE,date);
               listItem.put(NotePad.Notes._ID,id);
               listItems.add(listItem);
   
           }
   
           // 使用SimpleAdaper设置列表
         adapter = new SimpleAdapter(this,listItems,R.layout.noteslist_item,dataColumns,viewIDs);
```

3.时间戳处理部分
```java
    public String timeTransfer(String timeStamp)
    {

        SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd");
        Date date = new Date(System.currentTimeMillis());
        return simpleDateFormat.format(date);
    }
```


## 二、实现搜索

## 一、界面美化

## 一、标记分类
