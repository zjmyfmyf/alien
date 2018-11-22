
```java
package com.example.asus.sy1;

import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.Button;

public class MainActivity extends AppCompatActivity {

    private static String TAG = "LIFECYCLE";

    @Override
    protected void onDestroy() {
        super.onDestroy();
        Log.i(TAG, "(9) onDestory()");
    }

    @Override
    protected void onStop() {
        super.onStop();
        Log.i(TAG, "(8) onStop()");
    }

    @Override
    protected void onRestoreInstanceState(Bundle savedInstanceState) {
        super.onRestoreInstanceState(savedInstanceState);
        Log.i(TAG, "(3) onRestoreInstanceState()");
    }

    @Override
    protected void onSaveInstanceState(Bundle outState) {
        super.onSaveInstanceState(outState);
        Log.i(TAG, "(5) onSaveInstanceState()");
    }

    @Override
    protected void onPause() {
        super.onPause();
        Log.i(TAG, "(7) onPause()");
    }

    @Override
    protected void onRestart() {
        super.onRestart();
        Log.i(TAG, "(6) onRestart()");
    }

    @Override
    protected void onResume() {
        super.onResume();
        Log.i(TAG, "(4) onResume()");
    }

    @Override
    protected void onStart() {
        super.onStart();
        Log.i(TAG, "(2) onStart()");
    }

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        Log.i(TAG, "(1) onCreate()");

        Button button = (Button) findViewById(R.id.btn_finish);
        button.setOnClickListener(new View.OnClickListener() {
            public void onClick(View view) {
                finish();
            }
        });


    }


}

```
