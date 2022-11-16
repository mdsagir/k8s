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
+ **Remove state** : Entity manger remove the rocord from data base.
```java
User user=em.find(User.class,1);
em.remove(user)
```
## Merge v/s persist

+ persist used for the insert operation while merge used for the insert or update
+ after persist enity are in persit state while merge are not in perisit state rather that return copy entiy which is manage
+ merge are required all perorty to update in case null value will update for partial update
```java
Employee e=new Emplaoyee();
em.persist(e);
e.setName("rahul"); // effeted to table

Employee e=new Emplaoyee();
em.merge(e);
e.setName("rahul"); // not effeted to table

Employee e=new Emplaoyee();
Employee e2=em.persist(e);
e2.setName("anajli"); // effeted to table
```
## Transaction

