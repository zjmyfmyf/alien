

```

package com.example.asus.listview;

import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.view.View;
import android.widget.ArrayAdapter;
import android.widget.Button;
import android.widget.EditText;
import android.widget.ListView;
import android.widget.PopupMenu;
import android.widget.AdapterView;
import android.view.MenuItem;

import java.util.ArrayList;
import java.util.List;

public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity);

        final ListView lv1 = (ListView)findViewById(R.id.lv1);

        final List<String> list1=new ArrayList<String>();

        final ArrayAdapter<String> adapter1 = new ArrayAdapter<String>(this,android.R.layout.simple_list_item_1,list1);

        Button btn = (Button)findViewById(R.id.button_add);

        btn.setOnClickListener(new View.OnClickListener() {
            public void  onClick(View view){
                EditText et_class = (EditText)findViewById(R.id.text_class);
                EditText et_num = (EditText)findViewById(R.id.text_num);
                EditText et_name = (EditText)findViewById(R.id.text_name);
                list1.add(et_class.getText().toString()+"   "+et_num.getText().toString()+"    "+et_name.getText().toString());


                // ArrayAdapter temp_adp=(ArrayAdapter)lv.getAdapter();
                // temp_adp.add(et_class.getText().toString()+"  "+et_num.getText().toString()+"  "+et_name.getText().toString());
                lv1.setAdapter(adapter1);


            }
        });

        lv1.setOnItemLongClickListener(new AdapterView.OnItemLongClickListener() {
            @Override
            public boolean onItemLongClick(AdapterView<?> parent, View view, final int position, long id) {

                PopupMenu popup=new PopupMenu(MainActivity.this,view);
                popup.getMenuInflater().inflate(R.menu.popmenu,popup.getMenu());
                popup.show();


                popup.setOnMenuItemClickListener(new PopupMenu.OnMenuItemClickListener() {
                    @Override
                    public boolean onMenuItemClick(MenuItem item) {
                        adapter1.remove(adapter1.getItem(position));

                        return false;
                    }
                });
                //adapter.remove(adapter.getItem(0));
                return true;
            }
        });


    }
}

```

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical" android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:weightSum="1">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical">

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="0dp"
            android:layout_weight="0.3"
            android:orientation="horizontal">

            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="班级："
                android:textSize="20dp" />

            <EditText
                android:id="@+id/text_class"
                android:layout_width="100sp"
                android:layout_height="wrap_content" />
        </LinearLayout>

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="0dp"
            android:layout_weight="0.3"
            android:orientation="horizontal"
            android:weightSum="1">

            <TextView

                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="学号："
                android:textSize="20dp" />

            <EditText
                android:id="@+id/text_num"
                android:layout_width="120dp"
                android:layout_height="wrap_content"
                 />
        </LinearLayout>

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="0dp"
            android:layout_weight="0.3"
            android:orientation="horizontal">

            <TextView

                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="姓名："
                android:textSize="20dp" />

            <EditText
                android:id="@+id/text_name"
                android:layout_width="80dp"
                android:layout_height="wrap_content" />
        </LinearLayout>

    </LinearLayout>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:weightSum="1">

        <Button
            android:id="@+id/button_add"
            android:layout_width="75dp"
            android:layout_height="38dp"
            android:text="添加" />
    </LinearLayout>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:weightSum="1">


        <TextView
            android:id="@+id/textView3"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="0.3"
            android:text="班级" />

        <TextView
            android:id="@+id/textView2"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="0.3"
            android:text="学号" />

        <TextView
            android:id="@+id/textView4"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="0.3"
            android:text="姓名" />
    </LinearLayout>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:weightSum="1"
        android:orientation="horizontal">


        <ListView
            android:id="@+id/lv1"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
             />

    </LinearLayout>

</LinearLayout>
```
