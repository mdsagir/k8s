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

#### Mandatory
#### Required
#### Required New
#### Support
#### Not supported
#### Never
#### Nested

### REQUIRED (default)
If exist transaction there use it otherwise create new transaction
```java

    @Override
    @Transactional(propagation = Propagation.REQUIRED)
    public void process() {
        likeService.saveUserLike();
        commentService.userComment();
    }
    
    @Override
    @Transactional(propagation = Propagation.REQUIRED)
    public void saveUserLike() {
        Like like=new Like();
        like.setClick("yes");
        entityManager.persist(like);
    }
    @Override
    @Transactional(propagation = Propagation.REQUIRED)
    public void userComment() {
        Comment comment=new Comment();
        comment.setComment("hello");
        entityManager.persist(comment);
    }
```
### REQUIRES_NEW
If exiting transaction available suspend it crate new transaction and use it
```java
    @Override
    @Transactional(propagation = Propagation.REQUIRED)
    public void process() {
        likeService.saveUserLike();
        commentService.userComment();
    }
    @Override
    @Transactional(propagation = Propagation.REQUIRES_NEW) // suspend current transaction create new transaction
    public void saveUserLike() {
        Like like=new Like();
        like.setClick("yes");
        entityManager.persist(like);
    }
    @Override
    @Transactional(propagation = Propagation.REQUIRES_NEW) // suspend current transaction create new transaction
    public void userComment() {
        Comment comment=new Comment();
        comment.setComment("hello");
        entityManager.persist(comment);
    }
```
### MANDATORY
If current transaction available use it otherwise throw the exception
```java
    CASE 1
    @Override
    public void process() {
        likeService.saveUserLike();
        commentService.userComment();
    }
    @Override
    @Transactional(propagation = Propagation.MANDATORY) // throws exception because there are no any transaction
    public void saveUserLike() {
        Like like=new Like();
        like.setClick("yes");
        entityManager.persist(like);
    }
    CASE 2
    @Override
    @Transactional(propagation = Propagation.REQUIRED)
    public void process() {
        likeService.saveUserLike();
        commentService.userComment();
    }
    @Override
    @Transactional(propagation = Propagation.MANDATORY) // excute by existing transaction
    public void saveUserLike() {
        Like like=new Like();
        like.setClick("yes");
        entityManager.persist(like);
    }
```
### SUPPORTS
If any active transaction use it otherwise run code without transaction
```java
   @Override
    @Transactional(propagation = Propagation.REQUIRED)
    public void process() {
        likeService.saveUserLike();
        commentService.userComment();
    }
    @Override
    @Transactional(propagation = Propagation.SUPPORTS) // use existing transaction 
    public void userComment() {
        Comment comment=new Comment();
        comment.setComment("hello");
        entityManager.persist(comment);
    }
    @Override
    @Transactional(propagation = Propagation.SUPPORTS) // use existing transaction 
    public void saveUserLike() {
        Like like=new Like();
        like.setClick("yes");
        entityManager.persist(like);
    }
```
### NOT_SUPPORTED
If any active transaction suspend it run code without transaction
```java
    @Override
    @Transactional(propagation = Propagation.REQUIRED)
    public void process() {
        likeService.saveUserLike();
        commentService.userComment();
    }
    @Override
    @Transactional(propagation = Propagation.NOT_SUPPORTED)// current transaction are suspended 
    public void saveUserLike() {
        Like like=new Like();
        like.setClick("yes");
        entityManager.persist(like);
    }
    @Override
    @Transactional(propagation = Propagation.NOT_SUPPORTED) //  current transaction are suspended 
    public void userComment() {
        Comment comment=new Comment();
        comment.setComment("hello");
        entityManager.persist(comment);
    }
```
### NEVER
If any active transaction is there throw exception
```java
     Case 1
    @Override
    @Transactional(propagation = Propagation.NEVER)
    public void process() {
        commentService.userComment();
    } 
    @Override
    @Transactional(propagation = Propagation.NEVER) // no active transaction
    public void userComment() {
        Comment comment=new Comment();
        comment.setComment("hello");
        entityManager.persist(comment);
    }
    case 2
    @Override
    @Transactional(propagation = Propagation.REQUIRED)
    public void process() {
        likeService.saveUserLike();
    }
    @Override
    @Transactional(propagation = Propagation.NEVER) // activ transaction there so throw exception
    public void saveUserLike() {
        Like like=new Like();
        like.setClick("yes");
        entityManager.persist(like);
    }
```
### NESTED
If active transaction is there use it if any exception happed it create savepoint and rollaback to transaction at savepoint,
 if transaction not there create it and use it \
[Note :JpaDialect does not support savepoints]

## ISOLATION
### READ_UNCOMMITTED
Dirty read probelm when tx1 update record but not commited by the time tx2 will get the record later tx1 may rollback the tx2 having the old wrong data.
```sql
update order set price= price -20 where id=1; // by tx 1
select * from order where id=1; // by tx 2
rollback; // tx1
```
### READ_COMMITTED
To fixes the problem can use READ_COMMITTED by the tx2 only get reord if tx1 have commited
### REPEATABLE_READ
```sql
select * from order where id=1; // by tx 1
update order set price= price -20 where id=1; // by tx 2
select * from order where id=1; // by tx 1  get updated order price
```
tx1 having the wrong price value because by the time tx2 are updated, REPEATABLE_READ are can solve by locking the rows
### SERIALIZABLE
```sql
select * from order where price between 100 to 2000; // 4 rows by tx1
insert into order value(1, 150)                      // 
select * from order where price between 100 to 2000; // 5 row by tx2
```
Fantom problem are happen if in range of data are updated by some other tx, SERIALIZABLE are locked the record which fall in between record
