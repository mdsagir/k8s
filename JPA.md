## Entity lifecycle
+ **Transient state** : Create a entity to persist till entity is not attached with persistence context.
```java
User user=new User();
setter
```
+ **Persist state** : Entity is attached with persist context through persist or find the entity
``` java 
User user=new User();
setter
em.persist(user)
or
User user=  em.find(User.class,1);
 ```
 changing state of user effect to table
 + **Detached state** : Entity is detached with persist context
 ```java
 em.detached(user);
 em.close();
 em.clear();
 session.evict(user);
 ```
