
# Spring 自定义注解介绍

## 什么是自定义注解？

注解是一种特殊的标记，可以添加到代码的类、方法、字段等位置，用于提供元数据。自定义注解是开发者自己定义的注解，可以用于特定的业务逻辑或功能。

## 为什么要使用自定义注解？

自定义注解可以帮助我们：
1. **简化代码**：通过注解来标记特定的行为，而不是编写大量的重复代码。
2. **提高可读性**：注解可以使代码更具表达力，容易理解。
3. **增强灵活性**：可以根据注解来动态处理逻辑，而不是硬编码。

## 如何定义和使用自定义注解？

### 1. 定义自定义注解

首先，我们需要定义一个自定义注解。假设我们要创建一个注解 `@LogExecutionTime`，用于记录方法的执行时间。

```java
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface LogExecutionTime {
}
```

- `@Retention(RetentionPolicy.RUNTIME)`：注解在运行时可用。
- `@Target(ElementType.METHOD)`：注解可以应用于方法。

### 2. 创建注解处理器

接下来，我们需要创建一个处理器来处理这个注解。我们可以使用 Spring AOP 来实现这一点。创建一个切面类 `LogExecutionTimeAspect`。

```java
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.springframework.stereotype.Component;

@Aspect
@Component
public class LogExecutionTimeAspect {

    @Around("@annotation(LogExecutionTime)")
    public Object logExecutionTime(ProceedingJoinPoint joinPoint) throws Throwable {
        long start = System.currentTimeMillis();

        Object proceed = joinPoint.proceed();

        long executionTime = System.currentTimeMillis() - start;

        System.out.println(joinPoint.getSignature() + " executed in " + executionTime + "ms");
        return proceed;
    }
}
```

- `@Aspect`：标记该类为一个切面。
- `@Component`：将该类注册为 Spring 的 Bean。
- `@Around("@annotation(LogExecutionTime)")`：定义一个环绕通知，匹配所有被 `@LogExecutionTime` 注解的方法。

### 3. 使用自定义注解

在需要记录执行时间的方法上使用 `@LogExecutionTime` 注解。

```java
import org.springframework.stereotype.Service;

@Service
public class MyService {

    @LogExecutionTime
    public void serve() throws InterruptedException {
        // 模拟方法执行时间
        Thread.sleep(2000);
    }
}
```

### 4. 配置 Spring 应用

确保 Spring 应用启用了 AOP 支持。可以在配置类中添加 `@EnableAspectJAutoProxy` 注解。

```java
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.EnableAspectJAutoProxy;

@Configuration
@EnableAspectJAutoProxy
public class AppConfig {
}
```

### 运行示例

启动 Spring 应用并调用 `MyService` 的 `serve` 方法。

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class Application implements CommandLineRunner {

    @Autowired
    private MyService myService;

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

    @Override
    public void run(String... args) throws Exception {
        myService.serve();
    }
}
```

### 输出结果

```
void com.example.MyService.serve() executed in 2000ms
```

## 总结

通过以上示例，我们展示了如何在 Spring 中创建和使用自定义注解。自定义注解可以极大地增强代码的可读性和可维护性，同时也能简化配置和开发工作。以下是自定义注解的一些常见应用场景：

1. **自定义验证**：例如，创建自定义注解来验证输入参数。
2. **自定义拦截器**：例如，创建自定义注解来记录日志、监控性能、处理事务等。
3. **自定义配置**：例如，创建自定义注解来简化配置文件的加载和处理。

通过合理使用自定义注解，可以使代码更加简洁、清晰，并且更容易维护。希望这个分享能帮助大家更好地理解和利用 Spring 的自定义注解。
```

将上述内容保存为 `.md` 文件，例如 `Spring_Custom_Annotations.md`，即可作为分享材料使用。希望这份介绍能帮助开发者更好地理解和利用 Spring 的自定义注解。
