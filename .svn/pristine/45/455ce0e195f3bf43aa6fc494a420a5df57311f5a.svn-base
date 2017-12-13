package com.artwell_hk.inspectionrecord;

import android.os.AsyncTask;
import android.os.PersistableBundle;
import android.os.Bundle;


import org.json.JSONArray;
import org.json.JSONException;
import org.json.JSONObject;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.io.OutputStream;
import java.io.PrintWriter;
import java.net.HttpURLConnection;
import java.net.URL;
import java.net.URLConnection;
import java.nio.charset.StandardCharsets;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;

import android.app.Activity;
import android.util.Base64;
import android.util.Log;
import android.view.KeyEvent;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
import android.widget.ToggleButton;

import java.util.Map;
import java.util.Set;
import java.util.TreeMap;
import java.util.TreeSet;

import com.fasterxml.jackson.annotation.JsonIgnoreProperties;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.zxing.BarcodeFormat;
import com.google.zxing.DecodeHintType;
import com.journeyapps.barcodescanner.DecoratedBarcodeView;
import com.journeyapps.barcodescanner.BarcodeCallback;
import com.journeyapps.barcodescanner.BarcodeResult;
import com.journeyapps.barcodescanner.DefaultDecoderFactory;
import com.google.zxing.ResultPoint;
import com.journeyapps.barcodescanner.camera.CameraSettings;

import static android.content.ContentValues.TAG;


public class MainActivity extends Activity  {
    private DecoratedBarcodeView mDBV;
    private TextView tvResult;
    private AccessToken token;
    private LoginInfo loginInfo;
    private String barcode;
    private boolean isCameraRunning = true;

    private final static String apiUrl = "http://10.52.0.52:8080/app/rest/v2/";

    @JsonIgnoreProperties(ignoreUnknown = true)
    public static class DefectType  {

        protected String name;
        protected String id;

        public DefectType() {
        }

        public void setName(String name) {
            this.name = name;
        }

        public String getName() {
            return name;
        }

        public void setId(String id) {
            this.id = id;
        }
        public String getId() {
            return id;
        }
    }

    public static class LoginInfo  {
        public String employeeName;
        public String employeeId;
        public Integer htEmplId;
        public String jobName;
        public String jobId;
        public Integer htJobId;
        public List<DefectType> defectTypeList;

        public LoginInfo() {
        }

        @Override
        public String toString() {
            return "LoginInfo{" +
                    "employeeName='" + employeeName + '\'' +
                    ", employeeId=" + employeeId +
                    ", htEmplId=" + htEmplId +
                    ", jobName='" + jobName + '\'' +
                    ", jobId=" + jobId +
                    ", htJobId=" + htJobId +
                    ", defectTypeList=" + defectTypeList +
                    '}';
        }

        public String getEmployeeName() {
            return employeeName;
        }

        public void setEmployeeName(String employeeName) {
            this.employeeName = employeeName;
        }

        public String getEmployeeId() {
            return employeeId;
        }

        public void setEmployeeId(String employeeId) {
            this.employeeId = employeeId;
        }

        public Integer getHtEmplId() {
            return htEmplId;
        }

        public void setHtEmplId(Integer htEmplId) {
            this.htEmplId = htEmplId;
        }

        public String getJobName() {
            return jobName;
        }

        public void setJobName(String jobName) {
            this.jobName = jobName;
        }

        public String getJobId() {
            return jobId;
        }

        public void setJobId(String jobId) {
            this.jobId = jobId;
        }

        public Integer getHtJobId() {
            return htJobId;
        }

        public void setHtJobId(Integer htJobId) {
            this.htJobId = htJobId;
        }

        public List<DefectType> getDefectTypeList() {
            return defectTypeList;
        }

        public void setDefectTypeList(List<DefectType> defectTypeList) {
            this.defectTypeList = defectTypeList;
        }

    }

    public static class AccessToken {
        public String access_token;
        public String refresh_token;
        public long expires_in;
        public String token_type;
        public String scope;

        public AccessToken() {
        }

        public String getAccess_token() {
            return access_token;
        }

        public void setAccess_token(String access_token) {
            this.access_token = access_token;
        }

        public String getRefresh_token() {
            return refresh_token;
        }

        public void setRefresh_token(String refresh_token) {
            this.refresh_token = refresh_token;
        }

        public long getExpires_in() {
            return expires_in;
        }

        public void setExpires_in(long expires_in) {
            this.expires_in = expires_in;
        }

        public String getToken_type() {
            return token_type;
        }

        public void setToken_type(String token_type) {
            this.token_type = token_type;
        }

        public String getScope() {
            return scope;
        }

        public void setScope(String scope) {
            this.scope = scope;
        }
    }
    /**
     * Logs in with a user name and password and saves the access token for subsequent REST API invocations.
     */
    private void obtainToken() throws IOException, JSONException {
        String formData = "username=admin&password=admin&grant_type=password";
        String header = "Basic "+ Base64.encodeToString("client:secret".getBytes(),Base64.URL_SAFE);

        HttpURLConnection connection = (HttpURLConnection) new URL(apiUrl+"oauth/token").openConnection();
        connection.setDoOutput(true);
        connection.addRequestProperty("Authorization", header);
        connection.addRequestProperty("Content-Type", "application/x-www-form-urlencoded");
        connection.setRequestMethod("POST");
        connection.setRequestProperty("charset", "utf-8");
        connection.setRequestProperty("Content-Length", Integer.toString(formData.length()));

        OutputStream out = connection.getOutputStream();
        out.write(formData.getBytes(StandardCharsets.UTF_8));

        InputStream in = connection.getInputStream();
        token = new ObjectMapper().readValue(in, AccessToken.class);
    }

    @Override
    protected void onPause() {
        super.onPause();
        mDBV.pause();
    }

    @Override
    protected void onResume() {
        super.onResume();
        if (isCameraRunning) {
            mDBV.resume();
        }
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
    }

    @Override
    public void onSaveInstanceState(Bundle outState, PersistableBundle outPersistentState) {
        super.onSaveInstanceState(outState, outPersistentState);
    }

    @Override
    public boolean onKeyDown(int keyCode, KeyEvent event) {
        return mDBV.onKeyDown(keyCode, event) || super.onKeyDown(keyCode, event);
    }

    public class SubmitInspectionRecordTask extends AsyncTask<String, Void, String> {
        protected String SubmitInspectionRecord(String code) throws IOException, JSONException {
           Map<String,Object> param = new HashMap<>();
            param.put("employeeId",loginInfo.getHtEmplId());
            param.put("barcode",code);
            param.put("defectTypeList", getCheckedDefectButtons());
            Gson gson = new GsonBuilder().enableComplexMapKeySerialization().create();
            String formData=gson.toJson(param);
            Log.i(TAG, "formData:"+formData);

           /*  HttpURLConnection connection = (HttpURLConnection) new URL(apiUrl+"services/inspectionrecord_InspectionRecordService/recordInspection").openConnection();

            connection.setDoOutput(true);
            connection.addRequestProperty("Authorization", "Bearer "+token.access_token);
            Log.i(TAG, "token.access_token: "+token.access_token);
            connection.addRequestProperty("Content-Type", "application/x-www-form-urlencoded");
            connection.setRequestMethod("POST");
            connection.setRequestProperty("charset", "utf-8");
           connection.setRequestProperty("Content-Length", Integer.toString(formData.length()));

            OutputStream out = connection.getOutputStream();
            out.write(formData.getBytes(StandardCharsets.UTF_8));

            InputStream in = connection.getInputStream();
            String result = new ObjectMapper().readValue(in, String.class);*/
            PrintWriter out = null;
            BufferedReader in = null;
            String result = "";
            URL realUrl = new URL(apiUrl+"services/inspectionrecord_InspectionRecordService/recordInspection");
            // 打开和URL之间的连接
            URLConnection conn = realUrl.openConnection();
            // 设置通用的请求属性
            conn.addRequestProperty("Authorization", "Bearer "+token.access_token);
            conn.addRequestProperty("Content-Type", "application/json");
            conn.setRequestProperty("charset", "utf-8");
            conn.setRequestProperty("Content-Length", Integer.toString(formData.length()));;
            // 发送POST请求必须设置如下两行
            conn.setDoOutput(true);
            conn.setDoInput(true);
            // 获取URLConnection对象对应的输出流
            out = new PrintWriter(conn.getOutputStream());
            // 发送请求参数
            out.print(formData);
            // flush输出流的缓冲
            out.flush();
            // 定义BufferedReader输入流来读取URL的响应
            in = new BufferedReader(
                    new InputStreamReader(conn.getInputStream()));
            String line;
            while ((line = in.readLine()) != null) {
                result += line;
            }
            Log.i(TAG, "result: "+result);
            return result;
        }

        @Override
        protected String doInBackground(String... codes){
            String result="";
            try {
                Log.i(TAG, "doInBackground: 开始");
                result=SubmitInspectionRecord(codes[0]);
            } catch (IOException e) {
                e.printStackTrace();
            } catch (JSONException e) {
                e.printStackTrace();
            }
            return result;
        }
    }

    public class LoginInfoTask extends AsyncTask<String, Void, LoginInfo> {
        private LoginInfo getLoginInfobyCode(String code) throws IOException, JSONException {
            HttpURLConnection connection = (HttpURLConnection) new URL(apiUrl+"services/inspectionrecord_EmployeeService/getLoginInfobyCode?code="+code).openConnection();
            connection.addRequestProperty("Authorization", "Bearer "+token.access_token);
            connection.addRequestProperty("Content-Type", "application/x-www-form-urlencoded");
            connection.setRequestMethod("GET");
            int responseCode = connection.getResponseCode();
            String responseMessage = connection.getResponseMessage();
            InputStream in = connection.getInputStream();
            LoginInfo loginInfo = new ObjectMapper().readValue(in, LoginInfo.class);
            return loginInfo;
        }

        @Override
        protected LoginInfo doInBackground(String... codes) {
            try {
                obtainToken();
                loginInfo = getLoginInfobyCode(codes[0]);
            } catch (Exception e) {
                e.printStackTrace();
            }
            return loginInfo;
        }

        protected void onPostExecute(LoginInfo loginInfo) {
            if (loginInfo != null) {
                findViewById(R.id.layoutButtons).setVisibility(View.VISIBLE);
                findViewById(R.id.layoutLogin).setVisibility(View.VISIBLE);
                ((TextView) findViewById(R.id.textViewEmployeeName)).setText(loginInfo.getEmployeeName());
                ((TextView) findViewById(R.id.textViewJobName)).setText(loginInfo.getJobName());
                setDefectButtons(loginInfo.getDefectTypeList());
                ((TextView)findViewById(R.id.textBarcode)).setText("");
            } else {
                ((TextView)findViewById(R.id.textBarcode)).setText("No login info for this barcode");
            }
            isCameraRunning = true;
            mDBV.resume();
        }
    }

    protected void setDefectButtons(List<DefectType> defectTypeList) {
        ToggleButton[] buttons = {
                (ToggleButton) findViewById(R.id.button11), (ToggleButton) findViewById(R.id.button12), (ToggleButton) findViewById(R.id.button13),
                (ToggleButton) findViewById(R.id.button21), (ToggleButton) findViewById(R.id.button22), (ToggleButton) findViewById(R.id.button23),
                (ToggleButton) findViewById(R.id.button31), (ToggleButton) findViewById(R.id.button32), (ToggleButton) findViewById(R.id.button33),
        };
        for (int ii=0; ii<buttons.length; ii++) {
            ToggleButton button = buttons[ii];
            if(defectTypeList.size() <= ii) {
                button.setVisibility(View.INVISIBLE);
            } else {
                DefectType defectType = defectTypeList.get(ii);
                button.setVisibility(View.VISIBLE);
                button.setTextOff(defectType.getName());
                button.setTag(defectType.getId());
                Log.i(TAG, "defectType.getId(): "+defectType.getId());
                button.setTextOn(defectType.getName());
                button.setChecked(false);
            }
        }
    }

    protected void clearDefectButtons() {
        ToggleButton[] buttons = {
                (ToggleButton) findViewById(R.id.button11), (ToggleButton) findViewById(R.id.button12), (ToggleButton) findViewById(R.id.button13),
                (ToggleButton) findViewById(R.id.button21), (ToggleButton) findViewById(R.id.button22), (ToggleButton) findViewById(R.id.button23),
                (ToggleButton) findViewById(R.id.button31), (ToggleButton) findViewById(R.id.button32), (ToggleButton) findViewById(R.id.button33),
        };
        for (int ii=0; ii<buttons.length; ii++) {
            ToggleButton button = buttons[ii];
            button.setChecked(false);
        }
    }

    protected List getCheckedDefectButtons() {
        List<Map<String,String>> defectTypeMapList=new ArrayList<Map<String,String>>();
        ToggleButton[] buttons = {
                (ToggleButton) findViewById(R.id.button11), (ToggleButton) findViewById(R.id.button12), (ToggleButton) findViewById(R.id.button13),
                (ToggleButton) findViewById(R.id.button21), (ToggleButton) findViewById(R.id.button22), (ToggleButton) findViewById(R.id.button23),
                (ToggleButton) findViewById(R.id.button31), (ToggleButton) findViewById(R.id.button32), (ToggleButton) findViewById(R.id.button33),
        };
        for (int ii=0; ii<buttons.length; ii++) {
            ToggleButton button = buttons[ii];
           if (button.isChecked()){
               Map<String,String> defectTypeMap=new HashMap<>();

               defectTypeMap.put("id",button.getTag().toString());
               defectTypeMapList.add(defectTypeMap);
           }
        }
        return defectTypeMapList;
    }

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        //TODO auto updater https://github.com/mitmel/AppUpdateChecker
        setContentView(R.layout.activity_main);
        mDBV = (DecoratedBarcodeView) findViewById(R.id.dbv_custom);
        tvResult = (TextView)findViewById(R.id.tv_result);

        CameraSettings cameraSettings = new CameraSettings();
        cameraSettings.setFocusMode(CameraSettings.FocusMode.AUTO);
        cameraSettings.setExposureEnabled(true);
        mDBV.getBarcodeView().setCameraSettings(cameraSettings);
        Set<BarcodeFormat> decodeFormats = new TreeSet<BarcodeFormat>();
        decodeFormats.add(BarcodeFormat.QR_CODE);
        Map<DecodeHintType, Object> decodeHints = new TreeMap<DecodeHintType, Object>();
        //TODO only numbers, only fix length
        mDBV.getBarcodeView().setDecoderFactory(new DefaultDecoderFactory(decodeFormats, decodeHints, "UTF-8"));
        mDBV.decodeContinuous( new BarcodeCallback(){
            @Override
            public void barcodeResult(BarcodeResult result) {
                isCameraRunning = false;
                mDBV.pause();
                if (result.getText() != null) {
                    String code = result.getText();
                    if (loginInfo == null) {
                        new LoginInfoTask().execute(code);
                        ((Button)findViewById(R.id.buttonSubmit)).setEnabled(false);
                        ((Button)findViewById(R.id.buttonCancel)).setEnabled(false);
                    }
                    else {
                        barcode = code;
                        ((TextView)findViewById(R.id.textBarcode)).setText(barcode);
                        ((Button)findViewById(R.id.buttonSubmit)).setEnabled(true);
                        ((Button)findViewById(R.id.buttonCancel)).setEnabled(true);
                    }
                }
            }

            @Override
            public void possibleResultPoints(List<ResultPoint> resultPoints) {
            }
        });

        ((Button)findViewById(R.id.buttonLogout)).setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                loginInfo = null;
                barcode = null;
                findViewById(R.id.layoutButtons).setVisibility(View.INVISIBLE);
                findViewById(R.id.layoutLogin).setVisibility(View.INVISIBLE);
            }
        });

        ((Button)findViewById(R.id.buttonSubmit)).setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Log.i(TAG, "buttonSubmit 开始 ");
                 new SubmitInspectionRecordTask().execute(barcode);
                barcode = null;
                ((TextView)findViewById(R.id.textBarcode)).setText(barcode);
                ((Button)findViewById(R.id.buttonSubmit)).setEnabled(false);
                ((Button)findViewById(R.id.buttonCancel)).setEnabled(false);
                clearDefectButtons();
                isCameraRunning = true;
                mDBV.resume();
            }
        });
        ((Button)findViewById(R.id.buttonCancel)).setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                barcode = null;
                ((TextView)findViewById(R.id.textBarcode)).setText(barcode);
                ((Button)findViewById(R.id.buttonSubmit)).setEnabled(false);
                ((Button)findViewById(R.id.buttonCancel)).setEnabled(false);
                clearDefectButtons();
                isCameraRunning = true;
                mDBV.resume();
            }
        });

    }
}

/*
public class MainActivity extends Activity {
    private Button btnClick;
    private TextView tvResult;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        initView();
        initEvent();
    }

    private void initView() {
        btnClick = (Button) findViewById(R.id.btn_click);
        tvResult = (TextView) findViewById(R.id.tv_result);
    }

    private void initEvent() {
        this.btnClick.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                //假如你要用的是fragment进行界面的跳转
                //IntentIntegrator intentIntegrator = IntentIntegrator.forSupportFragment(ShopFragment.this).setCaptureActivity(CustomScanAct.class);
                IntentIntegrator intentIntegrator = new IntentIntegrator(MainActivity.this);
                intentIntegrator
                        .setDesiredBarcodeFormats(IntentIntegrator.ALL_CODE_TYPES)
                        .setPrompt("将二维码/条码放入框内，即可自动扫描")//写那句提示的话
                        .setOrientationLocked(false)//扫描方向固定
                        .setCaptureActivity(CustomScanAct.class) // 设置自定义的activity是CustomActivity
                        .initiateScan(); // 初始化扫描
            }
        });
    }

    //获取扫描的结果
    @Override
    public void onActivityResult(int requestCode, int resultCode, Intent data) {
        IntentResult intentResult = IntentIntegrator.parseActivityResult(requestCode, resultCode, data);
        if (intentResult != null) {
            if (intentResult.getContents() == null) {

            } else {
                // ScanResult 为获取到的字符串
                String ScanResult = intentResult.getContents();
                tvResult.setText(ScanResult);
            }
        } else {
            super.onActivityResult(requestCode, resultCode, data);
        }
    }
}
*/
