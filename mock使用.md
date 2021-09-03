# mock使用

1、

![image-20200524022536773](https://gitee.com/top20chenql/md_imgs/raw/master/img/20200623113201.png)



#### 2、多次触发返回不同值

![image-20200524022557201](https://gitee.com/top20chenql/md_imgs/raw/master/img/20200623113202.png)

#### 3、模拟抛出异常

![image-20200524022626012](https://gitee.com/top20chenql/md_imgs/raw/master/img/20200623113203.png)

4、![image-20200524022702839](https://gitee.com/top20chenql/md_imgs/raw/master/img/20200623113204.png)

#### 5、参数匹配

![image-20200524022744877](https://gitee.com/top20chenql/md_imgs/raw/master/img/20200623113205.png)

#### 6、自定义参数匹配

![image-20200524022808844](https://gitee.com/top20chenql/md_imgs/raw/master/img/20200623113206.png)

#### 7、预期回调接口生成期望值

![image-20200524022829259](https://gitee.com/top20chenql/md_imgs/raw/master/img/20200623113207.png)

#### 8、预期回调接口生成期望值（直接执行）

![image-20200524022851223](https://gitee.com/top20chenql/md_imgs/raw/master/img/20200623113208.png)

#### 9、修改对未预设的调用返回默认期望（指定返回值）

![image-20200524022915200](https://gitee.com/top20chenql/md_imgs/raw/master/img/20200623113209.png)

#### 10、用spy监控真实对象,设置真实对象行为

![image-20200524022935278](https://gitee.com/top20chenql/md_imgs/raw/master/img/20200623113210.png)

#### 11、调用真实的方法

![image-20200524022945288](https://gitee.com/top20chenql/md_imgs/raw/master/img/20200623113211.png)

### 二、实际应用

> 但是如果 userDao 还没写好，又想先测 userService 的话，就需要使用 Mockito 去模拟一个假的 userDao 出来。
>
> 使用方法是在 userDao 上加上一个 @MockBean 注解，当 userDao 被加上这个注解之后，表示 Mockito 会帮我们创建一个假的 Mock 对象，替换掉 Spring 中已存在的那个真实的 userDao Bean，也就是说，注入进 userService 的 userDao Bean，已经被我们替换成假的 Mock 对象了，所以当我们再次调用 userService 的方法时，会去调用的实际上是 mock userDao Bean 的方法，而不是真实的 userDao Bean。
>
> 当我们创建了一个假的 userDao 后，我们需要为这个 mock userDao 自定义方法的返回值，这里有一个公式用法，下面这段代码的意思为，当调用了某个 Mock 对象的方法时，就回传我们想要的自定义结果。
> 

```java
package com.chenqiao.fight.mock.user;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

@Component
public class UserService {
    
    @Autowired
    private UserDao userDao;
    
    public User getUserById(Integer id) {
        return userDao.getUserById(id);
    }
    
    public Integer insertUser(User user) {
        return userDao.insertUser(user);
    }
    
    public void print() {
        
    }
}
```

```java
package com.chenqiao.fight.mock.user;

/**
 * @Author: qiaole
 * @Description:
 * @Date: Created in 1:49 2020/5/24
 * @Modified By:
 */
public class UserDao {
    public User getUserById(Integer id) {
        return null;
    }
    
    public Integer insertUser(User user) {
        return null;
    }
}

```

```java
package com.chenqiao.fight.mock.user;

import com.chenqiao.fight.mock.MockApplication;
import org.junit.Assert;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.mockito.Mockito;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.mock.mockito.MockBean;
import org.springframework.test.context.junit4.SpringRunner;

/**
 * @Author: qiaole
 * @Description:
 * @Date: Created in 1:50 2020/5/24
 * @Modified By:
 */

@RunWith(SpringRunner.class)
@SpringBootTest(classes = MockApplication.class)
public class UserServiceTest {
    
    //先普通的注入一个userService bean
    @Autowired
    private UserService userService;
    
    @MockBean
    private UserDao userDao;
    
    @Test
    public void getUserById() {
        User user = userService.getUserById(1);
        //检查结果
        Assert.assertNotNull(user);
        Assert.assertEquals(user.getId(), new Integer(1));
        Assert.assertEquals(user.getName(), "John");
    }
    
    @Test
    public void mockGetUserById() {
        Mockito.when(userDao.getUserById(3)).thenReturn(new User(200, "i am mock 3"));
        User user = userService.getUserById(3);
        //检查结果
        Assert.assertNotNull(user);
        Assert.assertEquals(user.getId(), new Integer(200));
        Assert.assertEquals(user.getName(), "i am mock 3");
    }
    
    //    thenReturn 系列方法
    @Test
    public void mock1() {
        //    当使用任何整数值调用 userService 的 getUserById() 方法时，就回传一个名字为 I'm mock3 的 User 对象。
        Mockito.when(userService.getUserById(Mockito.anyInt())).thenReturn(new User(3, "I am mock"));
        User user1 = userService.getUserById(3);
        User user2 = userService.getUserById(200);
        System.out.println(user1.toString());
        System.out.println(user2.toString());
        
    }
    
    //    限制只有当参数的数字是 3 时，才会回传名字为 I'm mock 3 的 user 对象。
    @Test
    public void mock2() {
        Mockito.when(userService.getUserById(3)).thenReturn(new User(3, "i am mock"));
        User user1 = userService.getUserById(3);
        User user2 = userService.getUserById(200);
        System.out.println(user1.toString());
        System.out.println(user2);
    }
    
    //当调用 userService 的 insertUser() 方法时，不管传进来的 user 是什么，都回传 100。
    @Test
    public void mock3() {
        Mockito.when(userService.insertUser(Mockito.any(User.class))).thenReturn(100);
        Integer num = userService.insertUser(new User());
        System.out.println(num);
    }
    
    //    thenThrow 系列方法
    @Test
    public void mock4() {
        //当调用 userService 的 getUserById() 时的参数是 9 时，抛出一个 RuntimeException。
        Mockito.when(userService.getUserById(9)).thenThrow(new RuntimeException("mock throw exception"));
        userService.getUserById(9);
    }
    
    @Test
    public void mock5(){
        //如果方法没有返回值的话（即是方法定义为 public void myMethod() {...}），要改用 doThrow() 抛出 Exception。
        Mockito.doThrow(new RuntimeException("mock throw exception")).when(userService).print();
        userService.print();
    }
    
    //verify 系列方法
    public void mock6(){
        //检查调用 userService 的 getUserById()、且参数为3的次数是否为1次。
        Mockito.verify(userService,Mockito.times(1)).getUserById(Mockito.eq(3));
    }
}

```



```java
package com.nd.elearning.library.business.service;

import com.nd.egoods.GoodsApi;
import com.nd.egoods.SkuApi;
import com.nd.egoods.vo.GoodsVo;
import com.nd.egoods.vo.SkuVo;
import com.nd.elearning.library.business.constants.GoodsMqConstant;
import com.nd.esales.SalesApi;
import com.nd.esales.vo.GoodsPromotionVo;
import com.nd.esales.vo.PromotionVo;
import org.junit.Assert;
import org.junit.Before;
import org.junit.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.Mockito;
import org.mockito.MockitoAnnotations;

import java.math.BigDecimal;
import java.util.*;

import static org.mockito.Matchers.any;
import static org.mockito.Matchers.anyInt;
import static org.mockito.Mockito.when;

public class GoodsMqServiceTest {

    @Mock
    private GoodsApi goodsApi;
    @Mock
    private SkuApi skuApi;
    @Mock
    private SalesApi salesApi;
    @InjectMocks
    private GoodsMqService goodsMqService = Mockito.mock(GoodsMqService.class, Mockito.CALLS_REAL_METHODS);

    @Before
    public void before() {
        MockitoAnnotations.initMocks(this);
    }

    @Test
    public void testUpdateExtraCommodity() {
        UUID goodsId = UUID.fromString("018ab8c3-e7d9-449f-97cc-ed53335f5639");
        GoodsVo goodsVo = new GoodsVo();
        goodsVo.setGoodsId(goodsId);
        goodsVo.setStatus(GoodsMqConstant.STATUS_ON);
        when(this.goodsApi.getByResource(anyInt(), any(UUID.class))).thenReturn(goodsVo);

        UUID skuId = UUID.fromString("f43ea3a4-568d-4294-8b2a-aa28d01d972b");
        List<SkuVo> skuVos = new ArrayList<>();
        SkuVo skuVo = new SkuVo();
        skuVo.setSkuId(skuId);
        skuVo.setStatus(GoodsMqConstant.STATUS_ON);
        skuVo.setAmount(new BigDecimal("1"));
        skuVos.add(skuVo);
        when(this.skuApi.findByGoodsId(any(UUID.class))).thenReturn(skuVos);

        List<PromotionVo> promotionVos = new ArrayList<>();
        GoodsPromotionVo goodsPromotionVo = new GoodsPromotionVo();
        goodsPromotionVo.setSalesType(PromotionVo.SALES_TYPE_SINGLE);
        goodsPromotionVo.setSalesSubType(GoodsPromotionVo.SALES_SUB_TYPE_TJ);
        Map salesConfig = new HashMap();
        salesConfig.put("amount", 11);
        salesConfig.put("amount_type", "CHANNEL_CASH");
        goodsPromotionVo.setSalesConfig(salesConfig);
        goodsPromotionVo.setStatus(PromotionVo.STATUS_IN);
        promotionVos.add(goodsPromotionVo);
        when(this.salesApi.getSkuPromotions(any(UUID.class))).thenReturn(promotionVos);

        Map<String, Object> extra = new HashMap<>();
        goodsMqService.updateExtraCommodity(UUID.randomUUID(), extra);
        Assert.assertFalse("error", extra.isEmpty());
    }

    @Test
    public void testUpdateExtraCommodityOther() {
        goodsMqService.updateExtraCommodity(UUID.randomUUID(), null);

        UUID goodsId = UUID.fromString("018ab8c3-e7d9-449f-97cc-ed53335f5639");
        GoodsVo goodsVo = new GoodsVo();
        goodsVo.setGoodsId(goodsId);
        goodsVo.setStatus(GoodsMqConstant.STATUS_OFF);
        when(this.goodsApi.getByResource(anyInt(), any(UUID.class))).thenReturn(goodsVo);


        goodsVo.setStatus(GoodsMqConstant.STATUS_ON);
        when(this.goodsApi.getByResource(anyInt(), any(UUID.class))).thenReturn(goodsVo);
        goodsMqService.updateExtraCommodity(UUID.randomUUID(), null);

        UUID skuId = UUID.fromString("f43ea3a4-568d-4294-8b2a-aa28d01d972b");
        List<SkuVo> skuVos = new ArrayList<>();
        SkuVo skuVo = new SkuVo();
        skuVo.setSkuId(skuId);
        skuVo.setStatus(GoodsMqConstant.STATUS_OFF);
        skuVo.setAmount(new BigDecimal("1"));
        skuVos.add(skuVo);
        when(this.skuApi.findByGoodsId(any(UUID.class))).thenReturn(skuVos);
        goodsMqService.updateExtraCommodity(UUID.randomUUID(), null);

        skuVo = new SkuVo();
        skuVo.setSkuId(skuId);
        skuVo.setStatus(GoodsMqConstant.STATUS_ON);
        skuVo.setAmount(new BigDecimal("0"));
        skuVos.clear();
        skuVos.add(skuVo);
        when(this.skuApi.findByGoodsId(any(UUID.class))).thenReturn(skuVos);
        goodsMqService.updateExtraCommodity(UUID.randomUUID(), null);
    }
}

```

