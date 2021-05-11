<h3>@Required - 필수 프로퍼터명시(setXxx()에만 적용)</h3>

  - RequiredAnnotationBeanPostProcessor
  - 5.x 버젼부터 Deprecated

<h3>@Autowired - 의존관계 자동설정(생성자, 필드, 메소드)</h3>

  - AutowiredAnnotationBeanPostProcessor
    byType기준!!!!! 만약에 동일한 객체 여러개인경우에는
    id와 property명이 일치하는 객체를 찾아서 주입(byName)

<h3>@Qualifier - 동일한 타입의 빈 객체들 중에서 특정 빈 사용하도록 설정</h3>

<h3>@Resource - 어플리케이션에서 필요로 하느 자원을 자동 연결</h3>

  -CommonAnnotationBeanPostProcessor


 * RequiredAnnotationBeanPostProcessor

 * AutowiredAnnotationBeanPostProcessor

 * CommonAnnotationBeanPostProcessor

   => 위 3가지를 동시에 적용하기 위한 태그
     <context:annotation-config/>

--------------------------------------------------

@Component 설정 : <bean class="" id=""/> 대신

 <context:component-scan base-package="경로"/>

    context:component-scan를 설정하면 
       context:annotation-config 설정하지 않아도
    기본적으로 @Rrequired, @Autowired, @Resource 모두 적용한다

 -> 객체를 생성!! (xml문서에 bean을 생략!!!)

  *@Component를 상속받은 서브클래스.
    -@Controller
    -@Service
    -@Repository


     new 객체(); 같은 기능

