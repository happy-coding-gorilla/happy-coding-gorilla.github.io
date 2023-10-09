### HttpUtil客户端工具类-HttpUtil

```java
import com.alibaba.fastjson.JSONObject;
import org.apache.http.NameValuePair;
import org.apache.http.client.config.RequestConfig;
import org.apache.http.client.entity.UrlEncodedFormEntity;
import org.apache.http.client.methods.CloseableHttpResponse;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.client.utils.URIBuilder;
import org.apache.http.entity.StringEntity;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.message.BasicNameValuePair;
import org.apache.http.util.EntityUtils;

import java.io.IOException;
import java.net.URI;
import java.util.ArrayList;
import java.util.List;
import java.util.Map;

/**
 * Http客户端工具类
 */
public class HttpUtil {
    public static final Integer OK = 200;
    public static final Integer TIMEOUT_MSEC = 5 * 1000;

    /**
     * GET方式请求
     *
     * @param url      请求地址
     * @param paramMap 请求参数
     * @return 响应体
     */
    public static String get(String url, Map<String, String> paramMap) throws IOException {
        // 创建HttpClient对象
        CloseableHttpClient httpClient = HttpClients.createDefault();
        CloseableHttpResponse response = null;
        String result = "";
        try {
            // 含请求参数请求路径
            URIBuilder uriBuilder = new URIBuilder(url);
            if (paramMap != null) {
                for (Map.Entry<String, String> entry : paramMap.entrySet()) {
                    uriBuilder.addParameter(entry.getKey(), entry.getValue());
                }
            }
            URI uri = uriBuilder.build();
            HttpGet httpGet = new HttpGet(uri);
            response = httpClient.execute(httpGet);
            if (response.getStatusLine().getStatusCode() == OK) {
                // 获取String类型响应结果体
                result = EntityUtils.toString(response.getEntity(), "UTF-8");
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            try {
                response.close();
                httpClient.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        return result;
    }

    /**
     * POST方式请求
     *
     * @param url      请求地址
     * @param paramMap 请求参数
     * @return 响应体
     */
    public static String post(String url, Map<String, Object> paramMap) throws IOException {
        CloseableHttpClient httpClient = HttpClients.createDefault();
        CloseableHttpResponse response = null;
        String body = "";
        try {
            // 创建HttpPost请求
            HttpPost httpPost = new HttpPost(url);
            // 创建参数列表
            if (paramMap != null) {
                List<NameValuePair> paramList = new ArrayList<>();
                for (Map.Entry<String, Object> entry : paramMap.entrySet()) {
                    paramList.add(new BasicNameValuePair(entry.getKey(), (String) entry.getValue()));
                }
                // 模拟表单
                UrlEncodedFormEntity entity = new UrlEncodedFormEntity(paramList);
                httpPost.setEntity(entity);
            }
            httpPost.setConfig(builderRequestConfig());
            response = httpClient.execute(httpPost);
            if (response.getStatusLine().getStatusCode() == OK) {
                // 获取String类型响应结果体
                body = EntityUtils.toString(response.getEntity(), "UTF-8");
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            try {
                response.close();
                httpClient.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        return body;
    }

    /**
     * POST方式请求
     *
     * @param url      请求地址
     * @param paramMap 请求参数
     * @return 响应体
     */
    public static String post4Json(String url, Map<String, Object> paramMap) throws IOException {
        CloseableHttpClient httpClient = HttpClients.createDefault();
        CloseableHttpResponse response = null;
        String body = "";
        HttpPost httpPost = new HttpPost(url);
        try {
            if (paramMap != null) {
                JSONObject jsonObject = new JSONObject();
                for (Map.Entry<String, Object> entry : paramMap.entrySet()) {
                    jsonObject.put(entry.getKey(), entry.getValue());
                }
                StringEntity entity = new StringEntity(jsonObject.toString(), "UTF-8");
                entity.setContentEncoding("UTF-8");
                entity.setContentType("application/json");
                httpPost.setEntity(entity);
            }
            httpPost.setConfig(builderRequestConfig());
            response = httpClient.execute(httpPost);
            body = EntityUtils.toString(response.getEntity());
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            try {
                response.close();
                httpClient.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        return body;
    }

    private static RequestConfig builderRequestConfig() {
        return RequestConfig.custom()
                .setConnectTimeout(TIMEOUT_MSEC)
                .setConnectionRequestTimeout(TIMEOUT_MSEC)
                .setSocketTimeout(TIMEOUT_MSEC).build();
    }
}
```