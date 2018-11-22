
```java
package com.example.myf.sql;

import android.content.ContentValues;
import android.content.Context;
import android.database.Cursor;
import android.database.sqlite.SQLiteDatabase;
import android.database.sqlite.SQLiteException;
import android.database.sqlite.SQLiteOpenHelper;
import android.database.sqlite.SQLiteDatabase.CursorFactory;

public class DBAdapter {

	private static final String DB_NAME = "student.db";
	private static final String DB_TABLE = "studentinfo";
	private static final int DB_VERSION = 1;

	public static final String KEY_ID = "_id";
	public static final String KEY_NAME = "name";
	public static final String KEY_CLASS = "class";
	public static final String KEY_NUMBER = "number";

	private SQLiteDatabase db;
	private final Context context;
	private DBOpenHelper dbOpenHelper;

	public DBAdapter(Context _context) {
		context = _context;
	}

	/** Close the database */
	public void close() {
		if (db != null){
			db.close();
			db = null;
		}
	}

	/** Open the database */
	public void open() throws SQLiteException {
		dbOpenHelper = new DBOpenHelper(context, DB_NAME, null, DB_VERSION);
		try {
			db = dbOpenHelper.getWritableDatabase();
		}
		catch (SQLiteException ex) {
			db = dbOpenHelper.getReadableDatabase();
		}
	}


	public long insert(Student people) {
		ContentValues newValues = new ContentValues();

		newValues.put(KEY_NAME, people.Name);
		newValues.put(KEY_CLASS, people.Class);
		newValues.put(KEY_NUMBER, people.Number);

		return db.insert(DB_TABLE, null, newValues);
	}


	public Student[] queryAllData() {
		Cursor results =  db.query(DB_TABLE, new String[] { KEY_ID, KEY_NAME, KEY_CLASS, KEY_NUMBER},
				null, null, null, null, null);
		return ConvertToStudent(results);
	}

	public Student[] queryOneData(long id) {
		Cursor results =  db.query(DB_TABLE, new String[] { KEY_ID, KEY_NAME, KEY_CLASS, KEY_NUMBER},
				KEY_ID + "=" + id, null, null, null, null);
		return ConvertToStudent(results);
	}

	private Student[] ConvertToStudent(Cursor cursor){
		int resultCounts = cursor.getCount();
		if (resultCounts == 0 || !cursor.moveToFirst()){
			return null;
		}
		Student[] peoples = new Student[resultCounts];
		for (int i = 0 ; i<resultCounts; i++){
			peoples[i] = new Student();
			peoples[i].ID = cursor.getInt(0);
			peoples[i].Name = cursor.getString(cursor.getColumnIndex(KEY_NAME));
			peoples[i].Class = cursor.getInt(cursor.getColumnIndex(KEY_CLASS));
			peoples[i].Number= cursor.getInt(cursor.getColumnIndex(KEY_NUMBER));

			cursor.moveToNext();
		}
		return peoples;
	}

	public long deleteAllData() {
		return db.delete(DB_TABLE, null, null);
	}

	public long deleteOneData(long id) {
		return db.delete(DB_TABLE,  KEY_ID + "=" + id, null);
	}

	public long updateOneData(long id , Student people){
	    if (id != 0)
        {
            ContentValues updateValues = new ContentValues();
            updateValues.put(KEY_NAME, people.Name);
            updateValues.put(KEY_CLASS, people.Class);
            updateValues.put(KEY_NUMBER, people.Number);

            return db.update(DB_TABLE, updateValues,  KEY_ID + "=" + id, null);
        }
		return -1;
	}

	/** 静态Helper类，用于建立、更新和打开数据库*/
	private static class DBOpenHelper extends SQLiteOpenHelper {

		public DBOpenHelper(Context context, String name, CursorFactory factory, int version) {
			super(context, name, factory, version);
		}

		private static final String DB_CREATE = "create table " +
				DB_TABLE + " (" + KEY_ID + " integer primary key autoincrement, " +
				KEY_NAME+ " text not null, " + KEY_CLASS+ " integer," + KEY_NUMBER + " float);";

		@Override
		public void onCreate(SQLiteDatabase _db) {
			_db.execSQL(DB_CREATE);
		}

		@Override
		public void onUpgrade(SQLiteDatabase _db, int _oldVersion, int _newVersion) {
			_db.execSQL("DROP TABLE IF EXISTS " + DB_TABLE);
			onCreate(_db);
		}
	}
}
```


```java
package com.example.myf.sql;


import android.app.Activity;
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.Button;
import android.widget.EditText;
import android.widget.TextView;

public class SQLiteDemoActivity extends Activity {
    /**
     * Called when the activity is first created.
     */
    private DBAdapter dbAdepter;

    private EditText nameText;
    private EditText ageText;
    private EditText heightText;
    private EditText idEntry;

    private TextView labelView;
    private TextView displayView;

    private static String TAG = "TEST";


    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);

        nameText = (EditText) findViewById(R.id.name);
        ageText = (EditText) findViewById(R.id.age);
        heightText = (EditText) findViewById(R.id.height);
        idEntry = (EditText) findViewById(R.id.id_entry);

        labelView = (TextView) findViewById(R.id.label);
        displayView = (TextView) findViewById(R.id.display);


        Button addButton = (Button) findViewById(R.id.add);
        Button queryAllButton = (Button) findViewById(R.id.query_all);
        Button clearButton = (Button) findViewById(R.id.clear);
        Button deleteAllButton = (Button) findViewById(R.id.delete_all);

        Button queryButton = (Button) findViewById(R.id.query);
        Button deleteButton = (Button) findViewById(R.id.delete);
        Button updateButton = (Button) findViewById(R.id.update);


        addButton.setOnClickListener(addButtonListener);
        queryAllButton.setOnClickListener(queryAllButtonListener);
        clearButton.setOnClickListener(clearButtonListener);
        deleteAllButton.setOnClickListener(deleteAllButtonListener);

        queryButton.setOnClickListener(queryButtonListener);
        deleteButton.setOnClickListener(deleteButtonListener);
        updateButton.setOnClickListener(updateButtonListener);

        dbAdepter = new DBAdapter(this);
        dbAdepter.open();


    }

    OnClickListener addButtonListener = new OnClickListener() {
        @Override

        public void onClick(View v) {

            if ((nameText.getText().toString().length() != 0)
                    && (ageText.getText().toString().length() != 0)
                    && (heightText.getText().toString().length() != 0 )) {
                {
                    Student people = new Student();
                    people.Name = nameText.getText().toString();
                    people.Class = Integer.parseInt(ageText.getText().toString());
                    people.Number = Integer.parseInt(heightText.getText().toString());
                    long colunm = dbAdepter.insert(people);
                    if (colunm == -1) {
                        labelView.setText("添加过程错误！");
                    } else {
                        labelView.setText("成功添加数据，ID：" + String.valueOf(colunm));

                    }
                }
            } else {
                labelView.setText("前三行信息必须都不为空！");
            }
        }
    };

    OnClickListener queryAllButtonListener = new OnClickListener() {
        @Override
        public void onClick(View v) {
            Log.i(TAG, nameText.getText().toString());
            Student[] peoples = dbAdepter.queryAllData();
            if (peoples == null) {
                labelView.setText("数据库中没有数据");
                return;
            }
            labelView.setText("数据库：");
            String msg = "";
            for (int i = 0; i < peoples.length; i++) {
                msg += peoples[i].toString() + "\n";
            }
            displayView.setText(msg);
        }
    };

    OnClickListener clearButtonListener = new OnClickListener() {

        @Override
        public void onClick(View v) {
            displayView.setText("");
        }
    };

    OnClickListener deleteAllButtonListener = new OnClickListener() {
        @Override
        public void onClick(View v) {
            dbAdepter.deleteAllData();
            String msg = "数据全部删除";
            labelView.setText(msg);
        }
    };

    OnClickListener queryButtonListener = new OnClickListener() {
        @Override
        public void onClick(View v) {
            if (idEntry.getText().toString().length() != 0) {
                int id = Integer.parseInt(idEntry.getText().toString());
                Student[] peoples = dbAdepter.queryOneData(id);

                if (peoples == null) {
                    labelView.setText("数据库中没有ID为" + String.valueOf(id) + "的数据");
                    return;
                }
                labelView.setText("数据库：");
                displayView.setText(peoples[0].toString());
            }
            else {
                labelView.setText("id不能为空！");
            }


        }
    };

    OnClickListener deleteButtonListener = new OnClickListener() {
        @Override
        public void onClick(View v) {
            if (idEntry.getText().toString().length() != 0) {
                long id = Integer.parseInt(idEntry.getText().toString());
                long result = dbAdepter.deleteOneData(id);
                String msg = "删除ID为" + idEntry.getText().toString() + "的数据" + (result > 0 ? "成功" : "失败");
                labelView.setText(msg);
            } else {
                labelView.setText("id不能为空！");
            }

        }
    };

    OnClickListener updateButtonListener = new OnClickListener() {
        @Override
        public void onClick(View v) {
            Student people = new Student();

            if ((nameText.getText().toString().length() != 0)
                    && (ageText.getText().toString().length() != 0)
                    && (heightText.getText().toString().length() != 0)
                    && idEntry.getText().toString().length() != 0) {

                people.Name = nameText.getText().toString();


                people.Class = Integer.parseInt(ageText.getText().toString());

                people.Number = Integer.parseInt(heightText.getText().toString());
                long id = Integer.parseInt(idEntry.getText().toString());
                long count = dbAdepter.updateOneData(id, people);
                if (count == -1) {
                    labelView.setText("更新错误！");
                } else {
                    labelView.setText("更新成功，更新数据" + String.valueOf(count) + "条");

                }
            } else {
                labelView.setText("所有四行输入必须都不为空!");
            }

        }


    };

}
```

```java
package com.example.myf.sql;

public class Student {
	public int ID = -1;
	public String Name;
	public int Class;
	public int Number;

	@Override
	public String toString(){
		String result = "";
		result += "ID：" + this.ID + "，";
		result += "姓名：" + this.Name + "，";
		result += "班级：" + this.Class + "， ";
		result += "学号：" + this.Number + "，";
		return result;
	}
}

```


```xml
<?xml version="1.0" encoding="utf-8"?>

<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    >
    <RelativeLayout android:id="@+id/RelativeLayout01" 
		android:layout_width="wrap_content" 
		android:layout_height="wrap_content" >

		<EditText android:id="@+id/name"
			android:text=""
			android:layout_width="280dip"
			android:layout_height="wrap_content"
			android:layout_alignParentRight="true"
			android:layout_marginLeft="10dip" >
		</EditText>
		<TextView android:id="@+id/name_label"
			android:text="姓名："
			android:layout_width="wrap_content"
			android:layout_height="wrap_content"
			android:layout_alignParentLeft="true"
			android:layout_toLeftOf="@id/name"
			android:layout_alignBaseline="@+id/name">
		</TextView>
		
		<EditText android:id="@+id/age"
			android:text=""  
			android:layout_width="280dip" 
			android:layout_height="wrap_content"
			android:layout_alignParentRight="true" 
			android:layout_marginLeft="10dip" 
			android:layout_below="@id/name" 
			android:numeric="integer">
		</EditText>

		<TextView
			android:id="@+id/age_label"
			android:layout_width="wrap_content"
			android:layout_height="wrap_content"
			android:layout_alignBaseline="@+id/age"
			android:layout_alignParentLeft="true"
			android:layout_toLeftOf="@id/age"
			android:text="班级："></TextView>

		<EditText android:id="@+id/height"
			android:layout_width="280dip" 
			android:layout_height="wrap_content"
			android:layout_alignParentRight="true" 
			android:layout_marginLeft="10dip"
			android:layout_below="@id/age" 
			android:numeric="decimal">
		</EditText>

		<TextView
			android:id="@+id/height_label"
			android:layout_width="wrap_content"
			android:layout_height="wrap_content"
			android:layout_alignBaseline="@+id/height"
			android:layout_alignParentLeft="true"
			android:layout_toLeftOf="@id/height"
			android:text="学号："></TextView>
		
	</RelativeLayout>
	
	<LinearLayout android:id="@+id/LinearLayout01" 
		android:layout_width="fill_parent" 
		android:layout_height="wrap_content">
		<Button android:id="@+id/add" 
			android:text="添加数据" 
			android:layout_width="wrap_content"
			android:layout_height="wrap_content"
			android:padding = "5dip"
			android:layout_weight="1">
		</Button>
		<Button android:id="@+id/query_all" 
			android:text="全部显示" 
			android:layout_width="wrap_content"
			android:layout_height="wrap_content"
			android:padding = "5dip"
			android:layout_weight="1">
		</Button>
		<Button android:id="@+id/clear" 
			android:text="清除显示" 
			android:layout_width="wrap_content"
			android:layout_height="wrap_content"
			android:padding = "5dip"
			android:layout_weight="1">
		</Button>
		<Button	android:id="@+id/delete_all" 
			android:text="全部删除" 
			android:layout_width="wrap_content"
			android:layout_height="wrap_content"
			android:padding = "5dip"
			android:layout_weight="1">
		</Button>
	</LinearLayout>
	
	<LinearLayout android:id="@+id/LinearLayout03" 
		android:layout_width="fill_parent" 
		android:layout_height="wrap_content">
		<TextView android:text="ID："
			android:layout_width="wrap_content" 
			android:layout_height="wrap_content"
			android:padding = "3dip">
		</TextView>
		<EditText android:id="@+id/id_entry"
			android:layout_width="50dip" 
			android:layout_height="wrap_content"
			android:padding = "3dip"
			android:layout_weight="1">
		</EditText>
		<Button android:id="@+id/delete" 
			android:text="ID删除" 
			android:layout_width="50dip"
			android:layout_height="wrap_content"
			android:padding = "3dip"
			android:layout_weight="1">
		</Button>
		<Button android:id="@+id/query" 
			android:text="ID查询" 
			android:layout_width="50dip"
			android:layout_height="wrap_content"
			android:padding = "3dip"
			android:layout_weight="1">
		</Button>	
		<Button android:id="@+id/update" 
			android:text="ID更新" 
			android:layout_width="50dip"
			android:layout_height="wrap_content"
			android:padding = "3dip"
			android:layout_weight="1">
		</Button>	
</LinearLayout>
	
	<TextView android:id="@+id/label"
		android:text="查询结果："
		android:layout_width="wrap_content" 
		android:layout_height="wrap_content">
	</TextView>
	<ScrollView  android:layout_width="fill_parent"
    	android:layout_height="fill_parent">
    	<LinearLayout android:layout_width="fill_parent" 
			android:layout_height="wrap_content" 
			android:orientation="vertical">
			<TextView android:id="@+id/display"
				android:text=""
				android:layout_width="wrap_content" 
				android:layout_height="wrap_content">
			</TextView>
		</LinearLayout>
	</ScrollView>

</LinearLayout>


```
