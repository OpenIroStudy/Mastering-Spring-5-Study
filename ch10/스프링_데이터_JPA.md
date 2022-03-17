#### 스프링 데이터란 무엇일까?
#### 스프링 데이터의 목표는 무엇일까?
#### 스프링 데이터 및 스프링 데이터 JPA를 사용해 관계형 데이터베이스와 통신하는 방법은 무엇인가?
#### 스프링 데이터를 사용해 몽고 DB와 같은 비관계형 데이터베이스와 어떻게 통신할까?


NoSQL의 특징
* 비정형 데이터 : 데이터에 관한 특정 구조가 없다.
* 대용량 : 일반적으로 기존의 데이터베이스에서 처리할 수 있는 것보다 용량이 많다.
* 쉽게 확장 가능 : 일반적으로 수평 및 수직으로 확장할 수 있는 옵션을 제공한다. 
ex) 하둡, 카산드라, 몽고DB  

```java
private static String dburl = "jdbc:mysql://localhost:3306/connectdb?serverTimezone=Asia/Seoul&useSSL=false";
private static String dbUser = "connectuser";
private static String dbpasswd = "connect123!@#";


 String sql = "SELECT description, role_id FROM role order by role_id desc";
        try(Connection conn = DriverManager.getConnection(dburl, dbUser, dbpasswd);
                PreparedStatement ps  = conn.prepareStatement(sql) ) {

                    try(ResultSet rs = ps.executeQuery()){
                        while(rs.next()) {
                            String description =rs.getString(1);
                            int id = rs.getInt("role_id");
                            Role role = new Role(id,description);
                            list.add(role);
                        }
                    }
                    catch(Exception e) {
                        e.printStackTrace();
                    }
                }
        catch(Exception ex){
                    ex.printStackTrace();
        }
```
일반적인 JDBC 코드에는 실행할 쿼리, 명령문의 오브젝트에 쿼리 매개변수를 설정하는 코드 (EX. setString), ResultSet을 bean에 옮기는 코드 
<br><br>

<H1>가장 많이 쓰인 레이어 </H1> 

* 마이바티스 : 마이바티스는 매개변수를 설정하고 결과를 검색하기 위해 코드를 수동으로 작성할 필요가 없다. 자바 POJO를 데이터베이스에 매핑하는 간단한 XML이나 어노테이션 기반으로 구성을 제공한다. 
* 하이버네이트 : 하이버네이트는 ORM 프레임워크다. ORM 프레임워크는 오브젝트를 관계형 데이터베이스의 테이블에 매핑하는 데 유용하다. 하이버네이트의 가장 큰 장점은 개발자가 직접 쿼리를 작성할 필요가 없다는 것이다. 객체와 테이블 간의 관계가 매핑되면 하이버네이트는 매핑을 사용해 쿼리를 생성하고 데이터를 채우거나 검색한다. 


```
스프링 데이터 - 데이터 스토리지 옵션이 일(WORK) 단위로 증가하고 있다. 데이터에 액세스하기 위한 여러 가지 접근 방식을 제공하는 수많은 NoSQL 데이터베이스가 있다. 
관계형 데이터베이스만큼 일반적인 것조차도 데이터에 액세스하는 접근 방식이 여러가지다. 올바른 데이터 액세스 방법을 식별하고 구현하면 애플리케이션 개발의 복잡성이 가중된다. 
```

스프링 데이터는 서로 다른 종류의 데이터 레파지토리에서 데이터에 액세스하기 위한 일관된 모델을 제공하는 것이 목표다. <br>
* 스프링 데이터의 기능
  * 다양한 레파지토리를 통해 여러 데이터 레파지토리와 쉽게 통합
  * 레파지토리 메소드 이름을 기반으로 쿼리를 구문 분석하고 구성하는 기능
  * 기본 CRUD 기능 제공
  * 사용자가 생성한 후 마지막으로 변경된 경우와 같은 감사의 기본 지원
  * 스프링과의 강력한 통합
  * 스프링 데이터 레스트를 통해 REST 컨트롤러를 노출시키는 스프링 MVC와 통합

<br>
* 스프링 데이터 모듈
  * 스프링 데이터 커먼즈 : 모든 스프링 데이터 모듈에 공통 개념을 정의한다.
  * 스프링 데이터 JPA : JPA 레파지토리와 쉽게 통합한다.
  * 스프링 데이터 몽고DB : 문서 기반 데이터 레파지토리인 몽고DB와 쉽게 통합한다. 

스프링 데이터 커먼즈는 데이터 저장소와 상관없이 공통 인터페이스를 정의한다. 
CrudRepository 인터페이스는 기본 CRUD 메소드를 제공한다. PagingAndSorting Repository 인터페이스는 CrudRepository를 확장해 결과를 정렬하고 페이지를 매기는 기능을 추가한다. 

* @Entity : 해당 클래스가 엔티티음을 지정하는 어노테이션이다.
* @Id : ID가 엔티티의 기본 키임을 지정한다.
* @GeneratedValue(strategy = GenerationType.AUTO) : GeneratedValue 어노테이션은 기본 키 생성 방법을 지정하는 데 사용된다. -> autoIncrement
* @ManyToOne(fetch = FetchType.LAZY)는 User와 Todo 사이의 다대일 관계를 나타낸다. 관계의 한쪽에서 @ManyToOne 관계가 사용된다.
* @JoinColumn : 외래 키 열의 이름을 지정한다.

```java
@Entity
@Table(name = "member")
@Builder
@Getter
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@AllArgsConstructor(access = AccessLevel.PROTECTED)
class MemberEntity {
    @Id
    @GeneratedValue
    private final long id = 0;
    
    // 단방향 매핑.
    /* cascade 설정 하지 않고 자식 entity까지 연관 관계를 통해 insert시 
       "object references an unsaved transient instance" 와 같은 에러 발생.
    */
    @OneToMany(fetch = FetchType.EAGER, cascade = CascadeType.PERSIST)
    @JoinColumn(name = "member_id") // 자식 Entity가 가지는 외래키 이름.
    Set<MemberHistoryEntity> memberHistoryEntitySet = new HashSet<>();
    
    String email;
}
```



```java
RunWith(SpringRunner.class)
@DataJpaTest
@ActiveProfiles("...")
@AutoConfigureTestDatabase(replace = 
@AutoConfigureTestDatabase.Replace.NONE)
public class JpaTest {

}
```
* @DataJpaTest : jpa 레파지토리 단위 테스트에서 SpringRunner와 함께 사용된다. JPA 관련 자동 설정만 활성화한다. 테스트는 기본적으로 인메모리 데이터베이스를 사용한다. 
* @RunWith(SpringRunner.class) : SpringRunner는 SpringJUnit4ClassRunner의 간단한 별칭이다. 스프링 컨텍스트를 시작한다.
* @Autowired : 테스트에 사용될 class를 오토와이어링한다.


#### Optional은 null일 수도 있는 객체의 컨테이너 객체를 나타낸다.
 * Optional의 중요한 메소드
  * isPresent() : Optional에 null이 아닌 값이 포함돼 있는지 확인한다.
  * orElse() : 포함된 객체가 null인 경우의 기본값이다.
  * ifPresent() : 포함된 객체가 null이 아닌 경우 ifPresent의 코드가 실행된다.
  * get() : 포함된 객체를 검색한다. 
  
  



```java
@Autowired
TestEntityManager entityManager;

@Test
public void save() {
 Todo todo = todoRepository.findById(101L);
 todo.setDescription("Todo Desc Updated");
 todoRepository.save(todo);
 
 entityManager.flush();
 
 Todo updatedTodo = todoRepository,findById(101L).get();
 
 assertEquals("Todo Desc Updated", updatedTodo.getDescription());
```


PagingAndSortingRepository
정렬과 페이징을 도우는 Repository이다.

```java
@DataJpaTest
@Runwith(SpringRunner.class)
public class UserRepository.class {
 @Autowired
 UserRepository userRepository;
 
 @Autowired
 TestEntityManager entityManager;
 }
 
 @Test
 public void testing() {
  Sort sort = new Sort(Sort.Description.DESC, "name")
                   .and(new Sort(Sort.Direction.ASC, "userid"));
  Iterator<User> users = userRepository.findAll(sort);
  }
  ```
  
  * new Sort(Sort.Description.DESC - name을 내림차순으로 정렬한다.
  * and(new Sort(Sort.Direction.ASC - and() 메소드는 서로 다른 메서드는 서로 다른 정렬 구성을 결합하는 연결 메소드다. 
  * userRepository(sort) : 정렬 기준은 findAll 메서드에 매개변수로 전달된다.


```java

@Test
public void paging() {
 PageRequest pageable = new PageRequest(0,2);
 Page<User> userPage = userRepository.findAll(pageable);

}
```
* new PageRequest(0,2)첫번째 페이지를 요청하고 2개씩 보내준다.
* userRepository.findAll(pageable) findAll 메소드에 pageable을 전달해준닫.

#### pageRequest의 주의사항
- PageRequest 객체에는 페이지를 탐색하는 next(), previous(), first() 메소드가 있다. <br>
- pageRequest 생성자(public PageRequest(int page, int size, Sort sort)) 이다.

#### 반환 타입에 따른 페이징 결과
Spring Data JPA 에는 반환 타입에 따라서 각기 다른 결과를 제공한다. <br>

 

* Page<T> 타입
* Slice<T> 타입
* List<T> 타입
각자 다른 결과를 반환해준다.

* Page<T> 타입 : Page<T> 타입을 반환 타입으로 받게 된다면 offset과 totalPage 를 이용하여 서비스를 제공할 수 있게된다.
* Page<T> 는 일반적인 게시판 형태의 페이징에서 사용된다.
 
 ![image](https://user-images.githubusercontent.com/43237961/158751445-c23c9558-45fe-45a4-8268-22db761a8fa5.png)
<br> 
* 여기서 중요한 정보는 총 페이지 수 이다. 그 정보를 포함하여 반환한다.
* Page<T> 타입은 count 쿼리를 포함하는 페이징으로 카운트 쿼리가 자동으로 생성되어 함께 나간다.
 ```java
 {
  "content": [
    { "id": 13, "username": "User 12", "address": "Korea", "age": 12 },
    { "id": 14, "username": "User 13", "address": "Korea", "age": 13 },
    { "id": 15, "username": "User 14", "address": "Korea", "age": 14 },
    { "id": 16, "username": "User 15", "address": "Korea", "age": 15 }
  ],
  "pageable": {
    "sort": { "sorted": false, "unsorted": true, "empty": true },
    "pageNumber": 3,
    "pageSize": 4,
    "offset": 12,
    "paged": true,
    "unpaged": false
  },
  "totalPages": 25,
  "totalElements": 100,
  "last": false,
  "numberOfElements": 4,
  "number": 3,
  "sort": { "sorted": false, "unsorted": true, "empty": true },
  "size": 4,
  "first": false,
  "empty": false
}
 ```
 
* Slice<T> 타입 : Slice<T> 타입을 반환 타입으로 받게 된다면 더보기 형태의 페이징에서 사용된다. 
![image](https://user-images.githubusercontent.com/43237961/158751617-83534d3f-7031-4cca-b6d5-c829791d57e8.png)

 <br> 
 
 ```java
{
  "content": [
    { "id": 13, "username": "User 12", "address": "Korea", "age": 12 },
    { "id": 14, "username": "User 13", "address": "Korea", "age": 13 },
    { "id": 15, "username": "User 14", "address": "Korea", "age": 14 },
    { "id": 16, "username": "User 15", "address": "Korea", "age": 15 }
  ],
  "pageable": {
    "sort": { "sorted": false, "unsorted": true, "empty": true },
    "pageNumber": 3,
    "pageSize": 4,
    "offset": 12,
    "paged": true,
    "unpaged": false
  },
  "number": 3,
  "numberOfElements": 4,
  "first": false,
  "last": false,
  "size": 4,
  "sort": { "sorted": false, "unsorted": true, "empty": true },
  "empty": false
}
 ```
 
 Page<T> 타입의 반환에 없는 것들이 존재한다. number과 numberOfElements 그리고 Page<T> 에 존재하던 totalPages, totalElements 가 없어졌다.
<br> 
Slice<T> 타입은 추가 count 쿼리 없이 다음 페이지 확인 가능하다. 내부적으로 limit + 1 조회를 해서 totalCount 쿼리가 나가지 않아서 성능상 조금 이점을 볼 수도 있다.
 
 <br><br>
 
 
 #### 커스텀 JPQL 쿼리 작성하기
 ```java
 @Query("select u from User u where u.name = ?1")
 List<User> findUsersByNameUsingQuery(String name);

 @Query("select u from User u where u.name = :name")
 List<User> findUsersByNameUsingQuery(@Param("name") String name);
 
 ```
 
 * @Query : 레파지토리 메소드의 쿼리를 정의하는 어노테이션이다. 
 * name 매개변수는 인스 리스트에서 명명된 매개변수를 정의한다. 
 
 ##### 네이티브 SQL 쿼리
 
