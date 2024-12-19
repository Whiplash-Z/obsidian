# JPA - 다양한 데이터베이스 방언(Dialect) 지원

![[Pasted image 20241219154142.png]]

![[Pasted image 20241219154151.png]]


# JPA 구동 방식
![[Pasted image 20241219154236.png]]


# JPA 동작 확인
![[Pasted image 20241219154309.png]]





# Member 테이블에 데이터 추가
```java
public class JpaMain {  
  
    public static void main(String[] args) {  
  
        EntityManagerFactory emf = Persistence.createEntityManagerFactory("hello");  
        EntityManager em = emf.createEntityManager();  
  
        EntityTransaction transaction = em.getTransaction();  
        transaction.begin();  
        try {  
            Member member = new Member();  
            member.setId(2L);  
            member.setName("HelloB");  
  
            em.persist(member);  
  
            transaction.commit();  
        } catch (Exception e) {  
            transaction.rollback();  
        } finally {  
            em.close();  
        }  
        emf.close();  
    }  
}
```
```
Hibernate: 
    /* insert for
        hellojpa.Member */
	insert into
        Member (name, id) 
    values
        (?, ?)
```

# Member 테이블에 있는 데이터 수정
```java
public class JpaMain {  
  
    public static void main(String[] args) {  
  
        EntityManagerFactory emf = Persistence.createEntityManagerFactory("hello");  
        EntityManager em = emf.createEntityManager();  
  
        EntityTransaction transaction = em.getTransaction();  
        transaction.begin();  
        try {  
            Member findMember = em.find(Member.class, 1L);  
            findMember.setName("Hello JPA");  
  
            transaction.commit();  
        } catch (Exception e) {  
            transaction.rollback();  
        } finally {  
            em.close();  
        }  
        emf.close();  
    }  
}
```
```
Hibernate: 
    select
        m1_0.id,
        m1_0.name 
    from
        Member m1_0 
    where
        m1_0.id=?
Hibernate: 
    /* update
        for hellojpa.Member */
	update Member 
    set
        name=? 
    where
        id=?
```

>[!Warning] EntityManagerFactory는 하나만 생성해서 애플리케이션 전체에서 공유
>- EntityManager는 스레드간에 공유 X (사용하고 버려야 한다.)
>- JPA의 모든 데이터 변경은 트랜잭션 안에서 실행

# JPQL
- JPA는 SQL을 추상화한 JPQL이라는 객체지향 쿼리 언어를 제공한다.
- SQL 문법과 유사하고, SELECT, FROM, WHERE, GROUP BY, HAVING, JOIN 지원
- JPQL은 **엔티티 객체를 대상**으로 쿼리
- SQL은 **데이터베이스 테이블을 대상**으로 쿼리

```java
public class JpaMain {  
  
    public static void main(String[] args) {  
  
        EntityManagerFactory emf = Persistence.createEntityManagerFactory("hello");  
        EntityManager em = emf.createEntityManager();  
  
        EntityTransaction transaction = em.getTransaction();  
        transaction.begin();  
        try {  
//            Member findMember = em.find(Member.class, 1L);  
  
            List<Member> result = em.createQuery("select m from Member as m", Member.class)  
                    .getResultList();  
  
            for (Member member : result) {  
                System.out.println("member.getName() = " + member.getName());  
            }  
  
            transaction.commit();  
        } catch (Exception e) {  
            transaction.rollback();  
        } finally {  
            em.close();  
        }  
        emf.close();  
    }  
}
```
```
Hibernate: 
    /* 
    select
        m 
    from
        Member as m */ 
        select
            m1_0.id,
            m1_0.name 
        from
            Member m1_0
member.getName() = Hello JPA
member.getName() = HelloB
```
