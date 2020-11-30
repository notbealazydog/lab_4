# 自定义WebView验证隐式Intent的使用

### 实验包含两个应用（建两个工程）：

###  ◼ 第一个应用：获取URL地址并启动隐式Intent的调用。

###  ◼ 第二个应用：自定义WebView来加载URL



## 一、新建一个工程用来获取URL地址并启动Intent

主要代码：

```
    <EditText
        android:id="@+id/et_url"
        android:layout_width="307dp"
        android:layout_height="62dp"
        android:text=""

        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintVertical_bias="0.022" />

    <Button
        android:id="@+id/btn_go"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="点击浏览网址"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/et_url"
        app:layout_constraintVertical_bias="0.381" />
```

```
public class MainActivity extends AppCompatActivity {

    private Button btn_go;
    private EditText et_url;
    private String urlHead="https://";
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        et_url = (EditText) findViewById(R.id.et_url);
        btn_go = (Button) findViewById(R.id.btn_go);
        btn_go.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                // 创建Intent
                Intent intent = new Intent();
                // 为Intent设置Action属性
                intent.setAction(Intent.ACTION_VIEW);
                // 获取网址并解析为uri对象，设置Data属性
                intent.setData(Uri.parse(urlHead+et_url.getText().toString()));
                startActivity(intent);
            }
        });
    }
}

```

[![Dgq1v8.png](https://s3.ax1x.com/2020/11/30/Dgq1v8.png)](https://imgchr.com/i/Dgq1v8)

## 新建一个工程使用WebView来加载URL

```
public class MyBrowser extends AppCompatActivity {

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_webview);

        WebView webView = (WebView)findViewById(R.id.my_webView);
        Intent intent = getIntent();
        //获取Intent的Data属性
        Uri data = intent.getData();
        URL url = null;
        try {
            //创建一个URL对象，参数为协议，主机名，路径
            url = new URL(data.getScheme(), data.getHost(), data.getPath());
        } catch (Exception e) {
            e.printStackTrace();
        }
        //WebView加载web资源
        webView.loadUrl(url.toString());
        //覆盖WebView默认使用第三方或系统默认浏览器打开网页的行为，使网页用WebView打开
        webView.setWebViewClient(new WebViewClient() {
            @Override
            public boolean shouldOverrideUrlLoading(WebView view, String url) {
                view.loadUrl(url);
                return true;
            }
        });
        webView.getSettings().setJavaScriptEnabled(true);//true允许使用JavaScript脚本
    }

}
```

#### 在**AndroidManifest.xml**文件中注册**MyBrowser**

```
   <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT"/>
                <category android:name="android.intent.category.BROWSABLE"/>
                <data android:scheme="http"></data>
                <data android:scheme="https"></data>
            </intent-filter>
```

[![DgqQ8P.png](https://s3.ax1x.com/2020/11/30/DgqQ8P.png)](https://imgchr.com/i/DgqQ8P)