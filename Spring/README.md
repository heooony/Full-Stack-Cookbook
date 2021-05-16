<h1>Annotation</h1>

<h3>@Qualifier - 동일한 타입의 빈 객체들 중에서 특정 빈 사용하도록 설정</h3>

<h3>@Required - 필수 프로퍼터명시(setXxx()에만 적용)</h3>

  - RequiredAnnotationBeanPostProcessor
  - 5.x 버젼부터 Deprecated

<h3>@Autowired - 의존관계 자동설정(생성자, 필드, 메소드)</h3>

  - AutowiredAnnotationBeanPostProcessor
  - byType이 우선적으로 실행되고, 동일한 클래스의 객체를 여러 가지 갖고 있는 경우
    id와 property명이 일치하는 byName으로 실행이 된다.

<h3>@Resource - 어플리케이션에서 필요로 하느 자원을 자동 연결</h3>

- CommonAnnotationBeanPostProcessor

> <context:annotation-config/>
>
> 위 3가지를 동시에 적용하기 위한 태그, 
>
>  * RequiredAnnotationBeanPostProcessor
>  * AutowiredAnnotationBeanPostProcessor
>  * CommonAnnotationBeanPostProcessor

아래 태그는 <b>xml에서 bean설정을 하지 않고도</b> 객체를 생성할 수 있다.

```java
<bean class="" id=""/> 대신
<context:component-scan base-package="경로"/>
```

    context:component-scan를 설정하면 
    context:annotation-config 설정하지 않아도
    기본적으로 @Rrequired, @Autowired, @Resource 모두 적용한다

<h3>@Component를 상속받은 서브클래스</h3>

Controller : @Controller
Service : @Service
Dao : @Repository

```java
@Repository("productDao")
public class ProductDAOImpl implements ProductDAO {
  	
    //productDTO가 있을 경우 다음과 같은 선언도 가능하다.
  	//물론 ProductDTO 에 대한 bean설정을 필요로 한다.
    @Autowired
		List<ProductDTO> list;
 		.
    .
    .
      
//and
  
@Service("productService")
public class ProductServiceImpl implements ProductService {
  
  //각 영역에서 변수선언은 @Autowired으로 받는다.
  @Autowired
	private ProductDAO productDao;
  	.
    .
    .
      
//and
  
@Controller
public class ProductController {
  
  //각 영역에서 변수선언은 @Autowired으로 받는다.
  @Autowired
	private ProductService productService;
 	 	.
    .
    .

```

<h3>@RequestMapping("/경로")</h3>

```java
@RequestMapping(value = {"/{type}/{id}.do"})
public String aa(@PathVariable String type, 
      					@PathVariable String id) {
  System.out.println("type : " + type);
  System.out.println("id : " + id);
  System.out.println("{type}/{id}.do가 요청되었습니다.");
  return "result";
}
```



### @PostMapping("/경로")

### @GetMapping("/경로")

<h3>
  @RequestParam(value = "name", required = false)<br>
	@RequestParam(value = "id", defaultValue = "Guest")
</h3>


<h3>@ModelAttribute("")</h3>

메소드위에 선언하면 리턴하는 값 자체가 현재 컨트롤러를 실행하고 뷰를 이동하는 모든 뷰들이

공통으로 사용하는 모델정보가 된다.

```java
//post 방식으로 오는 /a.do의 경로를 받는다.
@PostMapping("/a.do")

//원래는 이 메소드를 지나고 나면 jsp에서 ${member.name} 과 같은 방식으로 사용해야 하지만,
//ModelAttribute 어노테이션으로 인해 ${m.name} 과 같은 방법으로 사용이 가능해진다.
//이때 member으로는 사용이 불가능하다.
public void cc(@ModelAttribute("m")  Member member) {
	System.out.println("member : " + member);
}
```

```java
//
@ModelAttribute("hobbies")
public List<String> hobbies() {
	List<String> list = new ArrayList<String>();
	list.add("등산");
	list.add("수영");
	list.add("낚시");
	list.add("골프");
	return list;
}
```
