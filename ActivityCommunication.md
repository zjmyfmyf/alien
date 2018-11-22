
```java
package com.example.myf.activitycommunication;

import android.content.Intent;
import android.net.Uri;
import android.support.annotation.Nullable;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;

public class MainActivity extends AppCompatActivity {
    private static final int SUBACTIVITY1 = 1;
    private static final int SUBACTIVITY2 = 2;
    TextView textView;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        textView = (TextView)findViewById(R.id.textShow);
        final Button btn1 = (Button)findViewById(R.id.btn1);
        final Button btn2 = (Button)findViewById(R.id.btn2);

        btn1.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(MainActivity.this, SubActivity1.class);
                startActivityForResult(intent, SUBACTIVITY1);
            }
        });

        btn2.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(MainActivity.this, SubActivity2.class);
                startActivityForResult(intent, SUBACTIVITY2);
            }
        });


    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, @Nullable Intent data) {
        super.onActivityResult(requestCode, resultCode, data);

        switch(requestCode) {
            case SUBACTIVITY1:
                if (resultCode == RESULT_OK) {
                    Uri uriData = data.getData();
                    textView.setText(uriData.toString());
                }
                break;

            case SUBACTIVITY2:
                break;
        }
    }
}

```


```java
package com.example.myf.activitycommunication;

import android.content.Intent;
import android.net.Uri;
import android.os.Bundle;
import android.support.annotation.Nullable;
import android.support.v7.app.AppCompatActivity;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;

public class SubActivity1 extends AppCompatActivity{
    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.subactivity1);
        final EditText editText = (EditText)findViewById(R.id.edit);
        Button btnOK = (Button)findViewById(R.id.btn_ok);
        Button btnCancel = (Button)findViewById(R.id.btn_cancel);

        btnOK.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                String uriString = editText.getText().toString();
                Uri data = Uri.parse(uriString);
                Intent result = new Intent(null, data);
                setResult(RESULT_OK, result);
                finish();
            }
        });

        btnCancel.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                setResult(RESULT_CANCELED, null);
                finish();
            }
        });
    }
}

```

```java
package com.example.myf.activitycommunication;

import android.os.Bundle;
import android.support.annotation.Nullable;
import android.support.v7.app.AppCompatActivity;
import android.view.View;
import android.widget.Button;

public class SubActivity2 extends AppCompatActivity {
    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.subactivity2);
        Button btnReturn = (Button)findViewById(R.id.btn_return);
        btnReturn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                setResult(RESULT_CANCELED, null);
                finish();
            }
        });
    }
}

```


```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical">

        <TextView
            android:id="@+id/textShow"
            android:layout_width="match_parent"
            android:layout_height="wrap_content" />

        <Button
            android:id="@+id/btn1"
            android:layout_width="wrap_content"
            android:layout_height="20pt"
            android:text="启动Activity1" />

        <Button
            android:id="@+id/btn2"
            android:layout_width="wrap_content"
            android:layout_height="20pt"
            android:text="启动Activity2" />

    </LinearLayout>
</android.support.constraint.ConstraintLayout>
```


```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical">

        <TextView
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="SubActivity 1" />

        <EditText
            android:id="@+id/edit"
            android:layout_width="match_parent"
            android:layout_height="50dp"
            tools:text="Input here" />

        <Button
            android:id="@+id/btn_ok"
            android:layout_width="wrap_content"
            android:layout_height="20pt"
            android:text="接受" />

        <Button
            android:id="@+id/btn_cancel"
            android:layout_width="wrap_content"
            android:layout_height="20pt"
            android:text="撤销" />

    </LinearLayout>
</android.support.constraint.ConstraintLayout>
```


```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical">

        <TextView
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="SubActivity 2" />
        <Button
            android:id="@+id/btn_return"
            android:layout_width="wrap_content"
            android:layout_height="20pt"
            android:text="关闭" />

    </LinearLayout>
</android.support.constraint.ConstraintLayout>
```


```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.myf.activitycommunication">

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <activity android:name=".SubActivity1"></activity>
        <activity android:name=".SubActivity2"></activity>
    </application>


</manifest>
```
