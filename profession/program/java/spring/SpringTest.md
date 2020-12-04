# Spring Test
***

## 错误记录
### javax.websocket.server.ServerContainer not available

```java
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
```

### Circular view path [index]: would dispatch back to the current handler URL [/index] again. Check your ViewResolver setup!

```java
@RunWith(SpringRunner.class)
public class IndexControllerTest {

    private MockMvc mockMvc;

    @Before
    public void setUp() {
        InternalResourceViewResolver viewResolver = new InternalResourceViewResolver();
        viewResolver.setPrefix("/WEB-INF/jsp/view/");
        viewResolver.setSuffix(".jsp");
        this.mockMvc = MockMvcBuilders.standaloneSetup(new IndexController()).setViewResolvers(viewResolver).build();
    }

    @Test
    public void testIndex() throws Exception {
        this.mockMvc.perform(get("/index"))
                .andExpect(status().isOk())
                .andExpect(model().attribute("domain", SoulDomain.getInstance().getHttpPath()))
                .andReturn();
    }
}
```

## 参考链接
- [如何写好单元测试：Mock脱离数据库+不使用@SpringBootTest](https://blog.csdn.net/qq_36688143/article/details/97393949)