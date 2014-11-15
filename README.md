Android004
==========

JSON数据的读取
---------------
   import org.apache.http.Header;
   import org.apache.http.HttpEntity;
   org.apache.http.HttpResponse;
   import org.apache.http.NameValuePair;
   import org.apache.http.client.ClientProtocolException;
   import org.apache.http.client.entity.UrlEncodedFormEntity;
   import org.apache.http.client.methods.HttpPost;
   import org.apache.http.entity.StringEntity;
   import org.apache.http.impl.client.DefaultHttpClient;
   import org.apache.http.message.BasicHeader;
   import org.apache.http.message.BasicNameValuePair;
   import org.apache.http.params.BasicHttpParams;
   import org.apache.http.params.HttpConnectionParams;
   import org.apache.http.params.HttpParams;
   import org.apache.http.protocol.HTTP;
   import org.json.JSONException;
   import org.json.JSONObject;
 
   import android.app.Activity;
   import android.content.Context;
   import android.os.Bundle;
   import android.util.Log;
   import android.widget.TextView;
   import java.io.BufferedReader;
   import java.io.IOException;
   import java.io.InputStream;
   import java.io.InputStreamReader;
   import java.io.UnsupportedEncodingException;
   import java.net.HttpURLConnection;
   import java.util.ArrayList;
   import java.util.List;
 
   public class json extends Activity {
    public Context context;
    private TextView textView1;
    public static String URL = "http://webservice.webxml.com.cn/WebServices/WeatherWebService.asmx?wsdl";
    private DefaultHttpClient httpClient;
    StringBuilder result = new StringBuilder();
    private static final int TIMEOUT = 60;
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);
        HttpParams paramsw = createHttpParams();
        httpClient = new DefaultHttpClient(paramsw);
        HttpPost post = new HttpPost(
                "http://webservice.webxml.com.cn/WebServices/WeatherWebService.asmx?wsdl");
        List<NameValuePair> params = new ArrayList<NameValuePair>();
        params.add(new BasicNameValuePair("name", "this is post"));
        try {
            //向服务器写json
            JSONObject json = new JSONObject();
            Object email = null;
            json.put("email", email);
            Object pwd = null;
            json.put("password", pwd);
            StringEntity se = new StringEntity( "JSON: " + json.toString());
            se.setContentEncoding(new BasicHeader(HTTP.CONTENT_TYPE, "application/json"));
            post.setEntity(se);
 
            post.setEntity(new UrlEncodedFormEntity(params, HTTP.UTF_8));
            HttpResponse httpResponse = httpClient.execute(post);
            int httpCode = httpResponse.getStatusLine().getStatusCode();
            if (httpCode == HttpURLConnection.HTTP_OK&&httpResponse!=null) {
                Header[] headers = httpResponse.getAllHeaders();
                HttpEntity entity = httpResponse.getEntity();
                Header header = httpResponse.getFirstHeader("content-type");
                //读取服务器返回的json数据（接受json服务器数据）
                InputStream inputStream = entity.getContent();
                InputStreamReader inputStreamReader = new InputStreamReader(inputStream);
                BufferedReader reader = new BufferedReader(inputStreamReader);// 读字符串用的。
                String s;
                while (((s = reader.readLine()) != null)) {
                    result.append(s);
                }
                reader.close();// 关闭输入流
                //在这里把result这个字符串个给JSONObject。解读里面的内容。
                JSONObject jsonObject = new JSONObject(result.toString());
                String re_username = jsonObject.getString("username");
                String re_password = jsonObject.getString("password");
                int re_user_id = jsonObject.getInt("user_id");
                setTitle("用户id_"+re_user_id);
                Log.v("url response", "true="+re_username);
                Log.v("url response", "true="+re_password);
            } else {
                textView1.setText("Error Response" + httpResponse.getStatusLine().toString());
            }
        } catch (UnsupportedEncodingException e) {
        } catch (ClientProtocolException e) {
        } catch (IOException e) {
        } catch (JSONException e) {
            e.printStackTrace();
        } finally {
            if (httpClient != null) {
                httpClient.getConnectionManager().shutdown();// 最后关掉链接。
                httpClient = null;
            }
        }
    }
 
    public static final HttpParams createHttpParams() {
        final HttpParams params = new BasicHttpParams();
        HttpConnectionParams.setStaleCheckingEnabled(params, false);
        HttpConnectionParams.setConnectionTimeout(params, TIMEOUT * 1000);
        HttpConnectionParams.setSoTimeout(params, TIMEOUT * 1000);
        HttpConnectionParams.setSocketBufferSize(params, 8192 * 5);
        return params;
    }
}
