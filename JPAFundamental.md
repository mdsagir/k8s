# JPA Fundamental
## Core concept
### Annotation
```java
@Entity
public class Product {
}
```
> `product` table will map with entity
```java
@Entity(name = "MyProduct")
public class Product {
}
```
> `my_product` table will map with entity
```java
@Table(name = "ProductTable")
@Entity(name = "MyProduct")
public class Product {
}
```
> `product_table` table will map with entity, `@Table` are optional, `@Entity` name are used
> for the `JPQL`.  Ex `entityManager.createQuery("from my_product").getResultList()`
### Id Generator
> `AUTO` : Default generation type, select based on dialect, **MYSQL** used _sequence_
```java
@Id
@GeneratedValue(strategy = GenerationType.AUTO)
private Long id;
```
> `IDENTITY`: Its insert every row and return the generated value, batch operation are not possible
```java
@Id
@GeneratedValue(strategy = GenerationType.IDENTITY)
private Long id;
```
> `SEQUENCE`: Maintain table for allocate size _next_val_ to regenerate id's, having good database
> performance because It's not look each time _next_val_ for insert, But if insert batch size is 
> less than allocate size id order may be varied. After database migration may face data insertion
> may face some issue.
```java
@Id
@GeneratedValue(strategy = GenerationType.SEQUENCE)
private Long id;

or custom,

@Id 
@GeneratedValue(generator = "productSequenceGenerator")
@SequenceGenerator(name = "productSequenceGenerator", sequenceName = "productSequenceGenerator",allocationSize = 50)
private Long id;
```
> `TABLE`: Its be like SEQUENCE, but they do the row-level locking approach can become a performance issue.
```java
@Id
@GeneratedValue(generator = "tableSequence")
@TableGenerator(name = "tableSequence")
private Long id;
```
### Enum
```java
@Entity
public class Product {
    @Enumerated(EnumType.STRING)
    private Status status;

    public enum Status {
        CART,
        ORDER,
        OUT_FOR_DELIVERY,
        COMPLETED
    }
}
```
> Its store enum value as _String_ **CART** , **ORDER**.....
### AttributeConverter
```java
@Converter
public class ProductStatusAttributeConverter implements AttributeConverter<Product.Status, Character> {
    @Override
    public Character convertToDatabaseColumn(Product.Status attribute) {
        return Objects.isNull(attribute) ? null : attribute.value.toCharArray()[0];
    }

    @Override
    public Product.Status convertToEntityAttribute(Character dbData) {
        return Arrays.stream(Product.Status.values())
                .filter(status -> status.value.toCharArray()[0] == dbData).findFirst()
                .orElseThrow();

    }
}
@Entity
public class Product {
    @Convert(converter = ProductStatusAttributeConverter.class)
    private Status status;

    public enum Status {
        CART("C"),
        ORDER("O"),
        DELIVERY("D");

        public final String value;

        Status(String value) {
            this.value = value;
        }
    }
}
```
