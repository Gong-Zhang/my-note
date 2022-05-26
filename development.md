### java List转成Tree
``` 
List<KnowledgeTree> newTreeList = knowledgeTreeList.stream().filter(tree -> tree.getParentId()==null).peek(menu -> {
            menu.setChildren(getChildren(menu, knowledgeTreeList));
        }).collect(Collectors.toList());
       
private List<KnowledgeTree> getChildren(KnowledgeTree root, List<KnowledgeTree> all) {
        return all.stream().filter(m1 -> root.getId().equals(m1.getParentId()))
                .peek(m1 -> m1.setChildren(getChildren(m1, all)))
                .collect(Collectors.toList());
    } 
```
## hutools
### 读取json文件
```
String path = "classpath:menuTree.json";
FileReader fileReader = new FileReader(path);
String s = fileReader.readString();
JSONArray jsonArray = JSONUtil.readJSONArray(fileReader.getFile(), Charset.defaultCharset());
List<MenuTree> menuTreeList = JSONUtil.toList(jsonArray, MenuTree.class);
```
### 获取文件路径
```
 public static String getProjectConfPath(String fileName) {
        String filePath = "";
        Properties props = System.getProperties();
        String osName = props.getProperty("os.name");
        if (StringUtils.containsIgnoreCase(osName, "windows")) {
            filePath = System.getProperty("user.dir") + "\\conf" + "\\" + fileName;
        } else {
            filePath = System.getProperty("user.dir") + File.separator + "conf" + "/" + fileName;
        }
        log.info("【----------------------------》" + filePath + "】");
        return filePath;
    }
```
### Springboot自定义拦截器实现登录拦截，并解决跨域问题
```
@Configuration
public class CrosConfig implements WebMvcConfigurer {
    @Override
    public void addCorsMappings(CorsRegistry registry) {
        // 允许所有前端站点调用
        registry.addMapping("/**").allowedOrigins("*")
                .allowCredentials(true).allowedMethods("OPTIONS", "GET", "POST", "DELETE", "PUT").maxAge(1728000);
    }
}

@Slf4j
@WebFilter(urlPatterns = {"/process/*","/user/*","/knowledge/*"})
public class SessionFilter implements Filter {
    @Autowired
    private IAuthService authService;
    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
        //
        HttpServletResponse httpServletResponse = (HttpServletResponse) response;
        httpServletResponse.setHeader("Access-Control-Allow-Origin", "*");  //允许跨域的源
        httpServletResponse.setHeader("Access-Control-Allow-Methods", "*");  //跨域支持的方法
        httpServletResponse.setHeader("Access-Control-Allow-Headers", "*");   //表示服务器允许请求的字段，可以写多个
        httpServletResponse.setHeader("Access-Control-Max-Age", "false");  //下次预检的缓存时间
        httpServletResponse.setHeader("Access-Control-Allow-Credentials", "10");  //是否允携带凭证（cookie），True 和 上面的 * 不能共存
        if (authService.isExpire((HttpServletRequest) request,httpServletResponse)) {
            httpServletResponse.setContentType("application/json");
            Gson gson = new Gson();
            String responseJson = gson.toJson(ResultUtils.LoginExpire());
            try {
                httpServletResponse.setCharacterEncoding("utf-8");
                httpServletResponse.setStatus(HttpStatus.UNAUTHORIZED.value());
                httpServletResponse.getWriter().println(responseJson);
            } catch (IOException e) {
                e.printStackTrace();
            }
            return;
        }
        chain.doFilter(request,response);
    }
}

@SpringBootApplication(exclude = {DataSourceAutoConfiguration.class})
@ServletComponentScan(basePackages = {"com.h3c.ywrj.dzkf.itsm.filter"})
public class ItsmApplication {
    public static void main(String[] args) {
        SpringApplication.run(ItsmApplication.class, args);
    }
}
```
