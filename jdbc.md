# 依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>
```

## 案例

### 实体类

```java
public class User {
    private Long id;
    private String username;
    private String address;
    //省略getter/setter
}
```

## Service

```java
@Service
public class UserService {
    @Autowired
    JdbcTemplate jdbcTemplate;
    public int addUser(User user) {
    return jdbcTemplate.update("insert into user (username,address) values (?,?);", user.getUsername(), user.getAddress());
    }
}
```

## 复杂Sql操作（主键回填）

```java
public int addUser2(User user) {
    KeyHolder keyHolder = new GeneratedKeyHolder();
    int update = jdbcTemplate.update(new PreparedStatementCreator() {
        @Override
        public PreparedStatement createPreparedStatement(Connection connection) throws SQLException {
            PreparedStatement ps = connection.prepareStatement("insert into user (username,address) values (?,?);", Statement.RETURN_GENERATED_KEYS);
            ps.setString(1, user.getUsername());
            ps.setString(2, user.getAddress());
            return ps;
        }
    }, keyHolder);
    user.setId(keyHolder.getKey().longValue());
    System.out.println(user);
    return update;
}
```

### 删

```java
public int deleteUserById(Long id) {
    return jdbcTemplate.update("delete from user where id=?", id);
}
```

### 改

```java
public int updateUserById(User user) {
    return jdbcTemplate.update("update user set username=?,address=? where id=?", user.getUsername(), user.getAddress(),user.getId());
}
```

### 查（复杂）

```java
public List<User> getAllUsers() {
    return jdbcTemplate.query("select * from user", new RowMapper<User>() {
        @Override
        public User mapRow(ResultSet resultSet, int i) throws SQLException {
            String username = resultSet.getString("username");
            String address = resultSet.getString("address");
            long id = resultSet.getLong("id");
            User user = new User();
            user.setAddress(address);
            user.setUsername(username);
            user.setId(id);
            return user;
        }
    });
}
```

### 查(简单)

```java
public List<User> getAllUsers2() {
    return jdbcTemplate.query("select * from user", new BeanPropertyRowMapper<>(User.class));
}
```