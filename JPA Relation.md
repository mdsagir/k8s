# JPA

### Enum, AttributeConverter
* User entity

```java
@Getter
@Setter
@ToString
@RequiredArgsConstructor
@Entity
public class User {


    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String username;
    private String password;

    //@Enumerated(EnumType.STRING)
    @Convert(converter = StatusConverter.class)
    private Status status;

    @Convert(converter = PortfolioConverter.class)
    private Portfolio portfolio;

    public enum Status {
        ACTIVE("A"),
        INACTIVE("I"),
        EXPIRED("E"),
        LOCKED("L");
        public final String value;

        Status(String value) {
            this.value = value;
        }
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || Hibernate.getClass(this) != Hibernate.getClass(o)) return false;
        User user = (User) o;
        return id != null && Objects.equals(id, user.id);
    }

    @Override
    public int hashCode() {
        return getClass().hashCode();
    }
}
```
* PortfolioConverter
```java
@Converter
public class PortfolioConverter implements AttributeConverter<Portfolio, String> {
    @SneakyThrows
    @Override
    public String convertToDatabaseColumn(Portfolio attribute) {
        return new ObjectMapper().writeValueAsString(attribute);
    }

    @SneakyThrows
    @Override
    public Portfolio convertToEntityAttribute(String dbData) {
        return new ObjectMapper().readValue(dbData, Portfolio.class);
    }
}
```
* StatusConverter
```java
@Converter
public class StatusConverter implements AttributeConverter<User.Status, String> {
    @Override
    public String convertToDatabaseColumn(User.Status attribute) {
        return Objects.isNull(attribute) ? null : attribute.value;
    }

    @Override
    public User.Status convertToEntityAttribute(String dbData) {
        if (Objects.isNull(dbData)) {
            return null;
        }
        return Arrays.stream(User.Status.values())
                .filter(data -> data.value.equals(dbData))
                .findFirst()
                .orElseThrow();
    }
}
```
* Test case
```java
class Test {
    @Test
    public void save_test() {

        Portfolio portfolio = new Portfolio();
        portfolio.setPortfolioId(UUID.randomUUID().toString());
        portfolio.setStock("RELIANCE");
        portfolio.setTransactionId(UUID.randomUUID().toString());

        User user = new User();
        user.setUsername("AAA");
        user.setPassword("*********");
        user.setStatus(User.Status.LOCKED);
        user.setPortfolio(portfolio);
        userService.save(user);

    }
    @Test
    public void test_user_find() {
        User user = userService.findById(1L);
        System.out.println(user);
    }
}  
```
# Relationship
## ONE TO ONE
### Single directional shared FK 
```java
public class User {
    
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String username;
    private String password;

    //@Enumerated(EnumType.STRING)
    @Convert(converter = StatusConverter.class)
    private Status status;

    @Convert(converter = PortfolioConverter.class)
    private Portfolio portfolio;

    @OneToOne(cascade = CascadeType.ALL, fetch = FetchType.EAGER)
    @JoinColumn(name = "wallet_id")
    private Wallet wallet;

    public enum Status {
        ACTIVE("A"),
        INACTIVE("I"),
        EXPIRED("E"),
        LOCKED("L");
        public final String value;

        Status(String value) {
            this.value = value;
        }
    }
}
@Entity
public class Wallet {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private Double amount;
    private String qrCode;

}
```
> When try to fetch data lazy loading are can't work in one to oen default is eager loading.
### Single directional shared PK
```java
@Entity
public class User {
    
    @Id
    private Long id; // skip id generator, create first Wallet id then map it.
    private String username;
    private String password;
    
    // skips some other fields
    
    @OneToOne(cascade = CascadeType.ALL)
    @JoinColumn(name = "id")
    @MapsId // it's not create extra foreign rather than map PK as FK 
    private Wallet wallet;
}
```
### Bi Directional shared FK
```java
@Entity
public class User {


    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String username;
    private String password;

    @OneToOne(cascade = CascadeType.ALL)
    @JoinColumn(name = "wallet_id")
    private Wallet wallet;
}
@Entity
public class Wallet {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private Double amount;
    private String qrCode;

    @OneToOne(mappedBy = "wallet")
    private User user;

}
```
### Bi Directional shared PK
```java
@Entity
public class User {
    
    @Id
    private Long id;
    private String username;
    private String password;

    @OneToOne(cascade = CascadeType.ALL)
    @MapsId
    @JoinColumn(name = "id")
    private Wallet wallet;
}
@Entity
public class Wallet {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private Double amount;
    private String qrCode;

    @OneToOne(mappedBy = "wallet")
    private User user;

}
```
