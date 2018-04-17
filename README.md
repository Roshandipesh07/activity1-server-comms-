# activity1-server-comms-
import android.content.Context;

import android.media.MediaPlayer;

import android.net.Uri;

import android.net.wifi.WifiManager;

import android.os.AsyncTask;

import android.support.v7.app.AppCompatActivity;

import android.os.Bundle;

import android.view.View;

import android.widget.Button;

import android.widget.EditText;

import android.widget.ProgressBar;

import android.widget.TextView;

import android.widget.Toast;

import android.widget.VideoView;



import java.io.BufferedInputStream;

import java.io.BufferedReader;

import java.io.IOException;

import java.io.InputStream;

import java.io.InputStreamReader;

import java.io.UnsupportedEncodingException;

import java.net.HttpURLConnection;

import java.net.MalformedURLException;

import java.net.NetworkInterface;

import java.net.ProtocolException;

import java.net.URL;

import java.util.Collections;

import java.util.EventObject;

import java.util.List;


import javax.net.ssl.HttpsURLConnection;


public class MainActivity extends AppCompatActivity {

    private TextView txtmsg,macid;
    EditText regno;
    Button btn;
    //ProgressBar pb;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        //pb=(ProgressBar)findViewById(R.id.progressBar);
        //pb.setVisibility(View.GONE);
        macid = (TextView) findViewById(R.id.MacAddress);
        txtmsg = (TextView) findViewById(R.id.httpsreq);
        btn = (Button) findViewById(R.id.btn);
        regno = (EditText) findViewById(R.id.regno);
        final StringBuilder res1 = new StringBuilder();
        try {
            List<NetworkInterface> all = Collections.list(NetworkInterface.getNetworkInterfaces());
            for (NetworkInterface nif : all) {
                if (!nif.getName().equalsIgnoreCase("wlan0")) continue;

                byte[] macBytes = nif.getHardwareAddress();
                if (macBytes == null) {

                }
                for (byte b : macBytes) {
                    res1.append(Integer.toHexString(b & 0xFF) + ":");
                }

                if (res1.length() > 0) {
                    res1.deleteCharAt(res1.length() - 1);
                }
            }
        } catch (Exception ex) {
        }
        macid.setText("MAC: "+res1.toString());

        btn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                String r =regno.getText().toString();
                String mac =res1.toString();
                new serve().execute(r,mac);

            }
        });
    }
    public class serve extends AsyncTask<String, Integer, String> {

        @Override
        protected void onPreExecute(){
            super.onPreExecute();
            //pb.setVisibility(View.VISIBLE);
            txtmsg.setVisibility(View.GONE);
            //pb.setMax(100);
        }

        @Override
        protected void onProgressUpdate(Integer... values) {
            super.onProgressUpdate(values);
           // pb.setProgress(10);
        }

        @Override
        protected String doInBackground(String... input) {
            String r=input[0];
            String m=input[1];
            String u="https://android-club-project.herokuapp.com/upload_details?reg_no="+r.trim()+"&mac="+m.trim();
            try {
                URL url=new URL(u);

                HttpsURLConnection httpURLConnection= (HttpsURLConnection) url.openConnection();
                httpURLConnection.setRequestMethod("GET");





                //InputStreamReader inputStreamReader=new InputStreamReader(httpURLConnection.getInputStream());
                InputStream inputStreamReader=new BufferedInputStream(httpURLConnection.getInputStream());
                //Log.d("what hapen", inputStreamReader.toString());
                BufferedReader reader=new BufferedReader(new InputStreamReader(inputStreamReader));
                StringBuilder stringBuilder = new StringBuilder();

                //Log.d("what url",u);
                String reading;
                //Log.d("what buffer",reader.readLine());
                if((reading=reader.readLine())!=null)
                    stringBuilder.append(reading);
                reader.close();
                String res=stringBuilder.toString().substring(0,m.length());
                httpURLConnection.disconnect();

                return res;
            } catch (UnsupportedEncodingException e) {
                e.printStackTrace();
            } catch (ProtocolException e) {
                e.printStackTrace();
            } catch (MalformedURLException e) {
                e.printStackTrace();
            } catch (IOException e) {
                e.printStackTrace();
            }

            return "Not successful";
        }

        @Override
        protected void  onPostExecute(String res){
            super.onPostExecute(res);
            //pb.setVisibility(View.GONE);
            txtmsg.setVisibility(View.VISIBLE);
            txtmsg.setText(res);
        }

    }

}



//XML
<TextView

    android:id="@+id/MacAddress"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:textAlignment="center"
    android:textSize="30sp"
    app:layout_constraintBottom_toBottomOf="parent"
    app:layout_constraintHorizontal_bias="0.471"
    app:layout_constraintLeft_toLeftOf="parent"
    app:layout_constraintRight_toRightOf="parent"
    app:layout_constraintTop_toTopOf="parent"
    app:layout_constraintVertical_bias="0.178" />

<TextView
    android:id="@+id/MacAddresstext"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_marginTop="128dp"
    android:text="MAC Address:"
    android:textAlignment="center"
    android:textSize="30sp"
    app:layout_constraintLeft_toLeftOf="parent"
    app:layout_constraintRight_toRightOf="parent"
    app:layout_constraintTop_toTopOf="parent"
    app:layout_constraintHorizontal_bias="0.398" />

<EditText
    android:id="@+id/regno"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_marginLeft="66dp"
    android:layout_marginTop="42dp"
    android:ems="10"
    android:hint="Enter Your Reg"
    android:inputType="textPersonName"
    app:layout_constraintLeft_toLeftOf="parent"
    app:layout_constraintTop_toTopOf="parent" />

<Button
    android:id="@+id/btn"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_marginLeft="138dp"
    android:layout_marginTop="320dp"
    android:text="ID"
    app:layout_constraintLeft_toLeftOf="parent"
    app:layout_constraintTop_toTopOf="parent" />

<TextView
    android:id="@+id/httpsreq"
    android:layout_width="285dp"
    android:layout_height="35dp"
    android:layout_marginLeft="31dp"
    android:layout_marginTop="217dp"
    android:textAlignment="center"
    android:textSize="30sp"
    app:layout_constraintLeft_toLeftOf="parent"
    app:layout_constraintTop_toTopOf="parent" />

<TextView
    android:id="@+id/textView"
    android:layout_width="177dp"
    android:layout_height="31dp"
    android:layout_marginBottom="8dp"
    android:layout_marginLeft="104dp"
    android:layout_marginTop="8dp"
    android:text="Your Unique ID is"
    android:textSize="20dp"
    app:layout_constraintBottom_toBottomOf="parent"
    app:layout_constraintLeft_toLeftOf="parent"
    app:layout_constraintTop_toTopOf="parent"
    app:layout_constraintVertical_bias="0.84"
    tools:text="Your Unique ID is" />
