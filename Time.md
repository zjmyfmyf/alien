
```
package com.example.asus.time;

import android.content.Intent;
import android.os.Handler;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;

import java.text.NumberFormat;

public class MainActivity extends AppCompatActivity {

    private static Handler handler = new Handler();
    private Button btn_zero;
    private Button btn_start;
    private Button btn_stop;
    private static TextView hour;
    private static TextView minute;
    private static TextView second;
    public static int hh;
    public static int mm;
    public static int ss;

    public static void UpdateGUI(int h,int m,int s){
        hh = h;
        mm = m;
        ss = s;
        handler.post(RefreshLable);
    }

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        btn_zero = findViewById(R.id.zero);
        btn_start = findViewById(R.id.start);
        btn_stop = findViewById(R.id.stop);
        hour = findViewById(R.id.hour);
        minute = findViewById(R.id.minute);
        second = findViewById(R.id.second);
        final int h = Integer.parseInt(hour.getText().toString());
        final int m = Integer.parseInt((minute.getText().toString()));
        final int s = Integer.parseInt(second.getText().toString());

        final Intent intent = new Intent(this, TimeService.class);
        //start
        btn_start.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                //                String s = tv_ss.getText().toString();
                //                tv_ss.setText(s);
                // Toast.makeText(MainActivity.this, s, Toast.LENGTH_SHORT).show();
                startService(intent);
                intent.putExtra("h",hh);
                intent.putExtra("m",mm);
                intent.putExtra("s",ss);
                Toast.makeText(MainActivity.this,"开始计时",Toast.LENGTH_SHORT).show();
            }
        });
        // /clear
        btn_zero.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {


                UpdateGUI(0,0,0);
                stopService(intent);


                Toast.makeText(MainActivity.this,"清零",Toast.LENGTH_SHORT).show();
            }
        });
        //stop
        btn_stop.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {


                stopService(intent);
                hh = Integer.parseInt(hour.getText().toString());
                mm = Integer.parseInt((minute.getText().toString()));
                ss = Integer.parseInt(second.getText().toString());
                Toast.makeText(MainActivity.this,"暂停计时",Toast.LENGTH_SHORT).show();
            }
        });
    }
    private static Runnable RefreshLable = new Runnable() {
        @Override
        public void run() {
            NumberFormat nf = NumberFormat.getInstance();
            nf.setMinimumIntegerDigits(2);
            hour.setText(nf.format(hh));
            minute.setText(nf.format(mm));
            second.setText(nf.format(ss));
        }
    };
}

```


```
package com.example.asus.time;

import android.app.Service;
import android.content.Intent;
import android.os.IBinder;
import android.support.annotation.Nullable;
import android.view.LayoutInflater;
import android.view.View;
import android.widget.TextView;

public class TimeService extends Service {
    private Thread workthread;

    private int h=0;
    private int m=0;
    private  int s=0;

    @Nullable
    @Override
    public IBinder onBind(Intent intent) {
        return null;
    }

    @Override
    public void onCreate(){
        super.onCreate();

        workthread = new Thread(null,backgroudWork,"workThread");

    }

    @Override
    public void onStart(Intent intent,int startID){
        super.onStart(intent,startID);
        h = intent.getIntExtra("h",0);
        m = intent.getIntExtra("m",0);
        s = intent.getIntExtra("s",0);
        if(!workthread.isAlive()){
            workthread.start();
        }
    }

    @Override
    public void onDestroy(){
        workthread.interrupt();
    }

    private Runnable backgroudWork = new Runnable() {
        @Override
        public void run() {
                try {


                    while (!Thread.interrupted()) {
                        Thread.sleep(10);
                        s++;
                        if (s >= 100) {
                            m++;
                            s = 0;
                            if (m >= 60) {
                                h++;
                                m = 0;
                                if (h >= 60) {
                                    h = 0;
                                }
                            }
                        }
                        MainActivity.UpdateGUI(h, m, s);
                    }
                }
                catch (InterruptedException e){
                    e.printStackTrace();

                }
            }

    };
}

```


```
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.asus.time">

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

        <service android:name=".TimeService"></service>
    </application>

</manifest>
```


```
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
        android:orientation="vertical"
        tools:layout_editor_absoluteY="4dp">

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="0dp"
            android:layout_weight="1">

            <TextView
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:gravity="center"
                android:text="秒表"
                android:textSize="10pt" />
        </LinearLayout>

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="0dp"
            android:layout_weight="20"
            android:gravity="center"
            android:orientation="horizontal"

            >

            <TextView
                android:id="@+id/hour"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="00"
                android:textSize="15pt" />

            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text=":"
                android:textSize="15pt" />

            <TextView
                android:id="@+id/minute"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="00"
                android:textSize="15pt" />

            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text=":"
                android:textSize="15pt" />

            <TextView
                android:id="@+id/second"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="00"
                android:textSize="15pt" />

        </LinearLayout>

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="0dp"
            android:layout_weight="2"
            android:gravity="center"
            android:orientation="horizontal">

            <Button
                android:id="@+id/zero"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="清零" />

            <Button
                android:id="@+id/start"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="计时" />

            <Button
                android:id="@+id/stop"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="停止" />

        </LinearLayout>
    </LinearLayout>
</android.support.constraint.ConstraintLayout>
```
