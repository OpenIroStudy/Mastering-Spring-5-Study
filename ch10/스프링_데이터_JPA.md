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
* @GeneratedValue(strategy = GenerationType.AUTO) : GeneratedValue 어노테이션은 기본 키 생성 방법을 지정하는 데 사용된다.
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



