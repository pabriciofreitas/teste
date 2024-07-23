# Dependência Circular

## Introdução

Dependências circulares ocorrem quando dois ou mais beans em uma aplicação Spring Boot dependem uns dos outros, resultando em um ciclo que impede o Spring de criar os beans corretamente. Este repositório demonstra como evitar dependências circulares utilizando soluções como a criação de um terceiro serviço ou o uso de herança com uma `BaseService`.

#### Exemplo

Suponha que temos dois serviços, `ServiceA` e `ServiceB`, que dependem um do outro.

```java
@Service
public class ServiceA {
    @Autowired
    private final ServiceB serviceB;
  
    public void methodA() {
        serviceB.methodB();
    }
}

@Service
public class ServiceB {
    @Autowired
    private final ServiceA serviceA;

    public void methodB() {
        serviceA.methodA();
    }
}

```

## Soluções para Dependência Circular

### 1. Criação de um Terceiro Serviço

Uma maneira eficaz de resolver dependências circulares é introduzir um terceiro serviço que contenha a lógica compartilhada, evitando assim a dependência direta entre os dois serviços originais.

```java
@Service
public class CommonService {
    public void commonMethod() {
        // Lógica compartilhada
    }
}

@Service
public class ServiceA {
    @Autowired
    private final CommonService commonService;

    public void methodA() {
        commonService.commonMethod();
    }
}

@Service
public class ServiceB {
    @Autowired
    private final CommonService commonService;

    public void methodB() {
        commonService.commonMethod();
    }
}
```


### 2. Utilização de uma BaseService

Outra abordagem é centralizar a lógica comum em uma classe base que será estendida pelos serviços específicos.


```java
public abstract class BaseService {
    // Métodos comuns
    protected void logInfo(String message) {
        System.out.println("INFO: " + message);
    }
}


@Service
public class UserService extends BaseService {
    public void createUser() {
        logInfo("Creating user...");
        // Lógica específica para criar usuário
    }
}

@Service
public class ProductService extends BaseService {
    public void createProduct() {
        logInfo("Creating product...");
        // Lógica específica para criar produto
    }
}
```
