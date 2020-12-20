# 期中项目 NOTEPAD

## 一、实现时间戳

### 修改代码
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

4.修改对应的xml文件
```xml
    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="75dp"
        android:background="@drawable/list_selector"
        android:dividerPadding="20dp"
        android:orientation="vertical"
        android:paddingLeft="6dip"
        android:paddingRight="6dip"
        android:paddingBottom="3dip">
    
        <TextView
            android:id="@+id/note_title"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:paddingLeft="10dp"
            android:paddingTop="10dp"
            android:text="TextView"
            android:textColor="@color/title_textColor"
            android:textSize="23sp"
            android:singleLine="true"/>
    
        <TextView
            android:id="@+id/note_time"
            android:layout_width="match_parent"
            android:layout_height="35dp"
            android:paddingLeft="10dp"
            android:paddingTop="5dp"
            android:text="TextView"
            android:textSize="19dp"
            android:singleLine="true"/>
    </LinearLayout>
```
<img src="https://github.com/lzn1234/NotePad/blob/master/image/1.png"  height="50" width="165"><br/>



## 二、实现搜索

###1.使用自定义actionBar
```java
public void initActionbar() {


        // 自定义标题栏
        getActionBar().setDisplayShowHomeEnabled(false);
        getActionBar().setDisplayShowTitleEnabled(false);
        getActionBar().setDisplayShowCustomEnabled(true);

        LayoutInflater mInflater = (LayoutInflater) getSystemService(Context.LAYOUT_INFLATER_SERVICE);
        View mTitleView = mInflater.inflate(R.layout.custom_action_bar_layout,
                null);
        getActionBar().setCustomView(
                mTitleView,
                new ActionBar.LayoutParams(ActionBar.LayoutParams.MATCH_PARENT,
                        ActionBar.LayoutParams.WRAP_CONTENT));

        /*设置searchView*/
        searchView = (SearchView) mTitleView.findViewById(R.id.search_view);
        searchView.setOnQueryTextListener(this);
        int id = searchView.getContext().getResources().getIdentifier("android:id/search_src_text", null, null);
        TextView textView = (TextView) searchView.findViewById(id);
        textView.setTextColor(Color.BLACK);//提示字体颜色

        //设置背景条颜色
        int color = Color.parseColor("#64c1bb");
        ColorDrawable drawable = new ColorDrawable(color);
        getActionBar().setBackgroundDrawable(drawable);

    }
```

custom_action_bar_layout.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="45dp"
    android:orientation="horizontal">

    <SearchView
        android:id="@+id/search_view"
        android:layout_width="0px"
        android:layout_height="25dp"
        android:layout_marginLeft="20dp"
        android:layout_marginTop="5dp"
        android:layout_marginRight="20dp"
        android:layout_weight="1"
        android:background="@drawable/search_style"
        android:iconifiedByDefault="false"
        android:searchIcon="@drawable/search"
        android:theme="@style/searchStyle"
        android:inputType="textFilter"
        android:showDividers="none"
        android:queryBackground="@null"
        android:queryHint="@string/title_search_hint"></SearchView>

</LinearLayout>
```


###2.继承SearchView.OnQueryTextListener并实现onQueryTextSubmit和onQueryTextChange来实现搜索监听
```java
    @Override
    public boolean onQueryTextSubmit(String s) {
        return false;
    }

    @Override
    public boolean onQueryTextChange(String s) {

        Log.v("123","aaa"+s);
        listItems.clear();
        for(cursor.moveToFirst();!cursor.isAfterLast();cursor.moveToNext())

        {
            int idColumn = cursor.getColumnIndex(NotePad.Notes._ID);
            int titleColumn = cursor.getColumnIndex(NotePad.Notes.COLUMN_NAME_TITLE);
            int dateColumn = cursor.getColumnIndex(NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE);

            int id = cursor.getInt(idColumn);
            String title = cursor.getString(titleColumn);
            String date =  timeTransfer(cursor.getString(dateColumn));

            if (s.trim().equals(""))
            {
                Map<String,Object> listItem = new HashMap<>();
                listItem.put(NotePad.Notes.COLUMN_NAME_TITLE,title);
                listItem.put(NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE,date);
                listItem.put(NotePad.Notes._ID,id);
                listItems.add(listItem);
            }
            else
            {
                if (title.trim().contains(s.trim()))
                {
                    Map<String,Object> listItem = new HashMap<>();
                    listItem.put(NotePad.Notes.COLUMN_NAME_TITLE,title);
                    listItem.put(NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE,date);
                    listItem.put(NotePad.Notes._ID,id);
                    listItems.add(listItem);
                }
            }


        }

        adapter.notifyDataSetChanged();
        return false;
    }
```

效果截图：
<img src="https://github.com/lzn1234/NotePad/blob/master/image/2.png"  height="330" width="165"><br/>
<img src="https://github.com/lzn1234/NotePad/blob/master/image/3.png"  height="330" width="165"><br/>


## 一、界面美化

效果截图
<img src="https://github.com/lzn1234/NotePad/blob/master/image/4.png"  height="330" width="165"><br/>

###列表项美化。

使用自定义xml布局和隐藏滚动条
```java
setContentView(R.layout.note_listview);
        this.getListView().setTextFilterEnabled(true);
        this.getListView().setVerticalScrollBarEnabled(false);
```

note_listview.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >

    <ListView
        android:id="@android:id/list"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_weight="1"

        android:background="@color/noteBackground"
        android:dividerHeight="12dp"
        android:paddingLeft="25dp"
        android:paddingTop="12dp"
        android:paddingRight="25dp"
        android:paddingBottom="0dp"
        android:scrollbars="vertical">

    </ListView>

    <Button
        android:id="@+id/item_add"
        android:layout_width="55dp"
        android:layout_height="55dp"
        android:layout_alignParentRight="true"
        android:layout_alignParentBottom="true"
        android:layout_marginRight="20dp"

        android:layout_marginBottom="20dp"
        android:background="@drawable/add"
        android:onClick="toTopClick"
        android:text="" />


</RelativeLayout>
```

###悬浮按钮
note_listview.xml添加按钮

按钮的点击事件
```java
//悬浮按钮点击事件
        Button addBtn = (Button)findViewById(R.id.item_add);
        addBtn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                startActivity(new Intent(Intent.ACTION_INSERT, getIntent().getData()));
            }
        });
```

###列表项点击效果

设置列表项的背景为list_selector.xml

list_selector.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <!--  点击效果  -->
    <item android:drawable="@drawable/item_selector_selected" android:state_pressed="true"></item>

    <!--  未点击效果  -->
    <item android:drawable="@drawable/item_selector_default" android:state_pressed="false"></item>

</selector>
```
item_selector_selected.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android">
    <solid android:color="@color/list_item_default"></solid>
    <corners android:radius="7dp"></corners>
</shape>
```
item_selector_default.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android">
    <solid android:color="@color/list_item_pressed"></solid>
    <corners android:radius="7dp"></corners>
</shape>

```


## 一、标记分类
效果图：
选择分类
<img src="https://github.com/lzn1234/NotePad/blob/master/image/6.png"  height="330" width="165"><br/>
列表项对应颜色
<img src="https://github.com/lzn1234/NotePad/blob/master/image/7.png"  height="330" width="165"><br/>


###数据库添加列
```java
public static final String COLUMN_NAME_TAG = "tag";      //标记
```
```java
       @Override
       public void onCreate(SQLiteDatabase db) {
           db.execSQL("CREATE TABLE " + NotePad.Notes.TABLE_NAME + " ("
                   + NotePad.Notes._ID + " INTEGER PRIMARY KEY,"
                   + NotePad.Notes.COLUMN_NAME_TITLE + " TEXT,"
                   + NotePad.Notes.COLUMN_NAME_NOTE + " TEXT,"
                   + NotePad.Notes.COLUMN_NAME_CREATE_DATE + " INTEGER,"
                   + NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE + " INTEGER,"
                   + NotePad.Notes.COLUMN_NAME_TAG + " INTEGER"
                   + ");");
       }
```

###添加spinner控件
```xml
    <Spinner
        android:id="@+id/spinner"
        android:layout_width="203dp"
        android:layout_height="wrap_content"
        android:layout_marginLeft="200dp"
        android:entries="@array/region" />
```
将tag数据已整形数据添加到数据库中
```java
values.put(NotePad.Notes.COLUMN_NAME_TAG, 1);
```

数据加载时根据tag修改列表项背景
```java
    //根据position和listview获取某一项视图
    private View getViewByPosition(int pos, ListView listView) {
        final int firstListItemPosition = listView.getFirstVisiblePosition() ;
        final int lastListItemPosition = firstListItemPosition + listView.getChildCount() - 1;

        if (pos < firstListItemPosition || pos > lastListItemPosition) {
            return listView.getAdapter().getView(pos, null, listView);
        } else {
            final int childIndex = pos - firstListItemPosition;
            return listView.getChildAt(childIndex);
        }
    }


```
```java

            ListView listView = (ListView) findViewById(android.R.id.list);
            LinearLayout layout = (LinearLayout)getViewByPosition(0,listView);
            layout.setBackgroundColor(Color.parseColor("#e7f2e5"));
```