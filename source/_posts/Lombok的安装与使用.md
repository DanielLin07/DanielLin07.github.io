---
title: Lombok的安装与使用
date: 2019-05-23 22:39:07
tags:
  - Java
  - Lombok
  - 插件
categories: Java笔记
description: 使用注解简化Java开发的工具Lombok。
photos:
  - "https://i.loli.net/2019/05/23/5ce6c23da01b546908.png"
---

# Lombok是什么

引用Lombok官方文档的解释：

> Project Lombok is a java library that automatically plugs into your editor and build tools, spicing up your java.
Never write another getter or equals method again, with one annotation your class has a fully featured builder, Automate your logging variables, and much more.
From：https://projectlombok.org/

Lombok项目是一个可以自动插入编辑器和构建工具的Java库，且无需额外再写`getter`或者`equals`方法，通过注释的方式使类有一个功能齐全的生成器，自动化日志记录变量等等。

简单说，Lombok可以在编译时为`bean`自动生成`getter/setter`、`equals`、`toString`等方法。

# Lombok的安装

## Lombok在Maven中的安装

在pom中引入Lombok依赖：

```java
<dependencies>
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <version>1.18.6</version>
        <scope>provided</scope>
    </dependency>
</dependencies>
```

## Lombok插件安装

单单在pom中引入依赖使用Lombok会使得IDE报错，我们需要为IDE加入Lombok插件，以IDEA为例：

点击菜单栏的File -> Settings

![菜单](https://i.loli.net/2019/05/24/5ce6c4a4a2a5d19978.png)

在弹出的Settings菜单栏中，选中plugins，在Marketplace中搜索lombok，看到Lombok插件，点击Install即可完成安装

![插件菜单](https://i.loli.net/2019/05/24/5ce6c638e538378724.png)

# Lombok的使用

Lombok常用的注解如下表所示：

#|注解名|用途
:-:|:-:|:-:
1|`@Data`|生成getter/setter、equals、canEqual、hashCode、toString方法
2|`@Getter`|生成getter方法
3|`@Setter`|成setter方法
4|`@NonNull`|属性不能为空
5|`@NoArgsConstructor`|生成无参构造方法
6|`@RequiredArgsConstructor`|生成部分参数构造方法
7|`@AllArgsConstructor`|生成全参数构造方法
8|`@ToString`|生成toString方法
9|`@Value`|生成含所有参数的构造方法，getter、equals、hashCode、toString方法

## @Data

`@Data`注解用于`Bean`上，可为`Bean`的所有属性自动生成`getter/setter`、`equals`、`canEqual`、`hashCode`、`toString`方法，如为`final`属性，则不会为该属性生成`setter`方法。注意，`@Data`并不会自动生成无参构造方法，如果使用了有参构造，则需要手动添加无参构造或者使用`@NoArgsConstructor`来生成无参构造，示例如下：

```java
@Data
public class User {

    private Long id;

    private String username;

    private String phone;
}
```

编译后`User.class`文件会是如下所示：

```java
public class User {
    private Long id;
    private String username;
    private String phone;

    public User() {
    }

    public Long getId() {
        return this.id;
    }

    public String getUsername() {
        return this.username;
    }

    public String getPhone() {
        return this.phone;
    }

    public void setId(final Long id) {
        this.id = id;
    }

    public void setUsername(final String username) {
        this.username = username;
    }

    public void setPhone(final String phone) {
        this.phone = phone;
    }

    public boolean equals(final Object o) {
        if (o == this) {
            return true;
        } else if (!(o instanceof User)) {
            return false;
        } else {
            User other = (User)o;
            if (!other.canEqual(this)) {
                return false;
            } else {
                label47: {
                    Object this$id = this.getId();
                    Object other$id = other.getId();
                    if (this$id == null) {
                        if (other$id == null) {
                            break label47;
                        }
                    } else if (this$id.equals(other$id)) {
                        break label47;
                    }

                    return false;
                }

                Object this$username = this.getUsername();
                Object other$username = other.getUsername();
                if (this$username == null) {
                    if (other$username != null) {
                        return false;
                    }
                } else if (!this$username.equals(other$username)) {
                    return false;
                }

                Object this$phone = this.getPhone();
                Object other$phone = other.getPhone();
                if (this$phone == null) {
                    if (other$phone != null) {
                        return false;
                    }
                } else if (!this$phone.equals(other$phone)) {
                    return false;
                }

                return true;
            }
        }
    }

    protected boolean canEqual(final Object other) {
        return other instanceof User;
    }

    public int hashCode() {
        int PRIME = true;
        int result = 1;
        Object $id = this.getId();
        int result = result * 59 + ($id == null ? 43 : $id.hashCode());
        Object $username = this.getUsername();
        result = result * 59 + ($username == null ? 43 : $username.hashCode());
        Object $phone = this.getPhone();
        result = result * 59 + ($phone == null ? 43 : $phone.hashCode());
        return result;
    }

    public String toString() {
        return "User(id=" + this.getId() + ", username=" + this.getUsername() + ", phone=" + this.getPhone() + ")";
    }
}
```

## @Getter / @Setter

相比`@Data`注解，在`Bean`属性上使用的`@Getter/@Setter`注解更加细粒度，它会为注解属性生成`getter/setter`方法，示例如下：

```java
public class User {

    @Getter
    @Setter
    private Long id;

    private String username;

    private String phone;
}
```

编译后`User.class`文件会是如下所示：
```java
public class User {
    private Long id;
    private String username;
    private String phone;

    public User() {
    }

    public Long getId() {
        return this.id;
    }

    public void setId(final Long id) {
        this.id = id;
    }
}
```

## @NonNull

使用`@NonNull`注解的属性不能为`null`。当使用`@NonNull`注解于一个属性上的时候，将会为该属性生成一个带有非空检查的`setter`方法，当调用该`setter`方法存入`null`的时候，将会抛出`NullPointerException`。此外，如果使用Lombok生成带有该参数的构造方法，Lombok会在构造方法中对该参数进行非空校验，代码示例如下：

```java
public class User {

    @Getter
    @Setter
    @NonNull
    private Long id;

    @Getter
    private String username;

    private String phone;
}
```

编译后`User.class`文件会是如下所示：
```java
public class User {
    @NonNull
    private Long id;
    private String username;
    private String phone;

    public User() {
    }

    @NonNull
    public Long getId() {
        return this.id;
    }

    public void setId(@NonNull final Long id) {
        if (id == null) {
            throw new NullPointerException("id is marked @NonNull but is null");
        } else {
            this.id = id;
        }
    }

    public String getUsername() {
        return this.username;
    }
}
```

## @NoArgsConstructor / @RequiredArgsConstructor / @AllArgsConstructor

`@NoArgsConstructor`注解用于类上，使用该注解的类可以生成无参构造方法，同理，`@AllArgsConstructor`则是带所有参数构造方法。而`@RequiredArgsConstructor`则是生成使用了`@NonNull`注解和以`final`修饰的未经初始化的属性的构造方法，在下面的示例中，`@RequiredArgsConstructor`只会生成一个含有id属性与username属性的构造方法。
在这里，`@NoArgsConstructor`的意义主要是与其他两个注解配合使用，因为在使用其他两个注解后会覆盖原有生成的无参构造方法。

```java
@NoArgsConstructor
@RequiredArgsConstructor
@AllArgsConstructor
public class User {

    @Getter
    @Setter
    @NonNull
    private Long id;

    @Getter
    private String username;

    private String phone;
}
```

编译后`User.class`文件会是如下所示：

```java
public class User {
    @NonNull
    private Long id;
    private String username;
    private String phone;

    public User() {
    }

    public User(@NonNull final Long id) {
        if (id == null) {
            throw new NullPointerException("id is marked @NonNull but is null");
        } else {
            this.id = id;
        }
    }

    public User(@NonNull final Long id, final String username, final String phone) {
        if (id == null) {
            throw new NullPointerException("id is marked @NonNull but is null");
        } else {
            this.id = id;
            this.username = username;
            this.phone = phone;
        }
    }

    @NonNull
    public Long getId() {
        return this.id;
    }

    public void setId(@NonNull final Long id) {
        if (id == null) {
            throw new NullPointerException("id is marked @NonNull but is null");
        } else {
            this.id = id;
        }
    }

    public String getUsername() {
        return this.username;
    }
}
```

## @ToString

`@toString`注解使用在类上，可以为该类生成包含类名、所有属性（会按照属性定义顺序）并用逗号分割的`toString`方法。示例如下：

```java
@ToString
public class User {

    @Getter
    @Setter
    private Long id;

    @Getter
    private String username;

    @Getter
    private String phone ;

}
```

编译后`User.class`文件会是如下所示：

```java
public class User {
    private Long id;
    private String username;
    private String phone;

    public User() {
    }

    public String toString() {
        return "User(id=" + this.getId() + ", username=" + this.getUsername() + ", phone=" + this.getPhone() + ")";
    }

    public Long getId() {
        return this.id;
    }

    public void setId(final Long id) {
        this.id = id;
    }

    public String getUsername() {
        return this.username;
    }

    public String getPhone() {
        return this.phone;
    }
}
```

`@toString`注解还有几个可选属性，通过`exclude`属性可以排除掉`toString`方法中的指定字段，`includeFieldNames`属性来控制输出中是否包含的属性名称，示例如下：

```java
@ToString(exclude = "id")
public class User {

    @Getter
    @Setter
    private Long id;

    @Getter
    private String username;

    @Getter
    private String phone ;

}
```

编译后`User.class`文件会是如下所示：

```java
public class User {
    private Long id;
    private String username;
    private String phone;

    public User() {
    }

    public String toString() {
        return "User(username=" + this.getUsername() + ", phone=" + this.getPhone() + ")";
    }

    public Long getId() {
        return this.id;
    }

    public void setId(final Long id) {
        this.id = id;
    }

    public String getUsername() {
        return this.username;
    }

    public String getPhone() {
        return this.phone;
    }
}
```

## @Value

`@Value`注解用在类上，生成含所有参数的构造方法，`getter`、`equals`、`hashCode`、`toString`方法，但没有`setter`方法，示例如下：

```java
@Value
public class User {

    private Long id;

    private String username;

    private String phone ;
}
```

编译后`User.class`文件会是如下所示：

```java
public final class User {
    private final Long id;
    private final String username;
    private final String phone;

    public User(final Long id, final String username, final String phone) {
        this.id = id;
        this.username = username;
        this.phone = phone;
    }

    public Long getId() {
        return this.id;
    }

    public String getUsername() {
        return this.username;
    }

    public String getPhone() {
        return this.phone;
    }

    public boolean equals(final Object o) {
        if (o == this) {
            return true;
        } else if (!(o instanceof User)) {
            return false;
        } else {
            User other;
            label44: {
                other = (User)o;
                Object this$id = this.getId();
                Object other$id = other.getId();
                if (this$id == null) {
                    if (other$id == null) {
                        break label44;
                    }
                } else if (this$id.equals(other$id)) {
                    break label44;
                }

                return false;
            }

            Object this$username = this.getUsername();
            Object other$username = other.getUsername();
            if (this$username == null) {
                if (other$username != null) {
                    return false;
                }
            } else if (!this$username.equals(other$username)) {
                return false;
            }

            Object this$phone = this.getPhone();
            Object other$phone = other.getPhone();
            if (this$phone == null) {
                if (other$phone != null) {
                    return false;
                }
            } else if (!this$phone.equals(other$phone)) {
                return false;
            }

            return true;
        }
    }

    public int hashCode() {
        int PRIME = true;
        int result = 1;
        Object $id = this.getId();
        int result = result * 59 + ($id == null ? 43 : $id.hashCode());
        Object $username = this.getUsername();
        result = result * 59 + ($username == null ? 43 : $username.hashCode());
        Object $phone = this.getPhone();
        result = result * 59 + ($phone == null ? 43 : $phone.hashCode());
        return result;
    }

    public String toString() {
        return "User(id=" + this.getId() + ", username=" + this.getUsername() + ", phone=" + this.getPhone() + ")";
    }
}
```

Lombok的更新速度较快，更多使用方式可参考[官方文档](http://jnb.ociweb.com/jnb/jnbJan2010.html)。

# Lombok的优缺点

## Lombok的优点

- 减少代码的编写（虽然可以使用IDE自动生成），让代码变得简洁。
- 在属性修改时Lombok可以维护它的修改。

## Lombok的缺点
- 降低阅读源代码的可读性。
- 不支持特定参数构造方法的重载。
