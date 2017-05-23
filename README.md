# NotePad
This is an AndroidStudio rebuild of google SDK sample NotePad

# 一、显示时间戳

在类NoteEditor的函数updateNote()中添加代码：

	/**
      * 设置时间
      */
	SimpleDateFormat df = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss"); 
	values.put(NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE, df.format(new Date()));
	
新建一个布局，替换原先的布局后，可显示note的标题，内容，时间戳：

	<?xml version="1.0" encoding="utf-8"?>
	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
		android:orientation="vertical" android:layout_width="match_parent"
		android:layout_height="match_parent"
		android:weightSum="1">

		<TextView
			android:id="@+id/text_title"
			android:layout_width="match_parent"
			android:layout_height="31dp"
			android:text="TextView" />

		<TextView
			android:id="@+id/text_Time"
			android:layout_width="match_parent"
			android:layout_height="60dp"
			android:text="TextView" />
	</LinearLayout>
	
在NoteList的onCreate()中修改为新布局的显示：

	 //修改为新布局
		String[] dataColumns = { NotePad.Notes.COLUMN_NAME_TITLE ,NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE } ;//添加显示时间
	 
        int[] viewIDs = { R.id.text_title, R.id.text_Time };//显示新布局的title、time

        // Creates the backing adapter for the ListView.
         adapter
            = new SimpleCursorAdapter(
                      this,                             // The Context for the ListView
                      R.layout.datetime_item,          //新布局 // Points to the XML for a list item
                      cursor,                           // The cursor to get items from
                      dataColumns,
                      viewIDs
              );
			  
# 二、搜索功能

在NoteList中添加新函数addSearchView()，实现模糊查找功能：

	/**
     * 新添加的具有搜索功能的函数
     */
    private void addSearchView() {
        //给listview添加头部(search)
        View v=View.inflate(this, R.layout.search_item,null);

        getListView().addHeaderView(v);
        //给搜索框添加搜索功能
        final EditText et_Search=(EditText)v.findViewById(R.id.editText_search);
        et_Search.addTextChangedListener(new TextWatcherForSearch(){
            @Override
            public void onTextChanged(CharSequence charSequence, int i, int i1, int i2) {
                super.onTextChanged(charSequence, i, i1, i2);
                if (charSequence.length()!=0 && et_Search.getText().toString().length()!=0){
                    String str_Search = et_Search.getText().toString();
                    Cursor search_cursor = managedQuery(
                            getIntent().getData(),            // Use the default content URI for the provider.
                            PROJECTION,                       // Return the note ID and title for each note.
                            NotePad.Notes.COLUMN_NAME_TITLE+" like ?",                             // No where clause, return all records.
                            new String[]{"%"+str_Search+"%"}, //匹配字符串条件                            // No where clause, therefore no where column values.
                            NotePad.Notes.DEFAULT_SORT_ORDER  // Use the default sort order.
                    );
                    adapter.swapCursor(search_cursor);//刷新listview

                }else {
                    if (cursor!=null)//删除搜索框中的text后刷新listview
                        adapter.swapCursor(cursor);//刷新listview
                }
            }
        });
    }
	
新建布局，增加搜索框：

	<?xml version="1.0" encoding="utf-8"?>
	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
		android:orientation="vertical" android:layout_width="match_parent"
		android:layout_height="match_parent"
		android:weightSum="1">

		<EditText
			android:id="@+id/editText_search"
			android:layout_width="match_parent"
			android:layout_height="60dp"
			android:ems="10"
			android:inputType="textPersonName"
			android:hint="Search"
			android:layout_weight="0.12" />
	</LinearLayout>

# 运行截图：
## 添加时间戳
![添加时间戳.png](https://github.com/FjAndjam/NotePad/blob/master/Sreenshot/添加时间戳.png)
## 搜索功能
![搜索功能.png](https://github.com/FjAndjam/NotePad/blob/master/Sreenshot/搜索功能.png)
## 基础功能-添加
![添加新note1.png](https://github.com/FjAndjam/NotePad/blob/master/Sreenshot/添加新note1.png)
![添加新note2.png](https://github.com/FjAndjam/NotePad/blob/master/Sreenshot/添加新note2.png)
## 基础功能-修改
![修改note.png](https://github.com/FjAndjam/NotePad/blob/master/Sreenshot/修改note.png)
## 基础功能-删除
![删除note.png](https://github.com/FjAndjam/NotePad/blob/master/Sreenshot/删除note.png)
