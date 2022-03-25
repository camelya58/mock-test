# mock-test
Article about mock tests

### Case 1
To set the value for a field with @Value use ReflectionTestUtils.setField(bean, field, value).

```properties
my.property="some property"

```

```java
@RestController
@RequiredArgsConstructor
public class MyController {

  @Value("${my.property}")
  private String myProperty; 
  
  private final MyService service;
  
  public String getInfo() {
    return service.getInfo(myProperty); // for some reason the field mustn't be null
  }
}
```


```java
import org.junit.Before
import org.junit.Test
import org.mockito.Mockito.*;

import static org.mockito.Mock

public class MyControllerTest {

  private String myProperty; 
  
  @Mock 
  private final MyService service = mock(MyService.class);
  private final MyController controller = new MyController(service);
  
  @Before
  public void init() {
    ReflectionTestUtils.setField(this, "myProperty", "someProperty");
    ReflectionTestUtils.setField(controller, "myProperty", "someProperty");
  }  
  
  @Test
  public void getInfoTest() {
    String expected = "someInfo";
    when(service.getInfo(myProperty)).thenReturn(expected);
    String actual = controller.getInfo();
    assertEquals(expected, actual);
    verify(service, times(1)).getInfo(eq(myProperty));
    
  }
}
```
