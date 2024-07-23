# Resolução de Problemas de Dependência Circular em Spring Boot

## Introdução

Dependências circulares ocorrem quando dois ou mais beans em uma aplicação Spring Boot dependem uns dos outros, resultando em um ciclo que impede o Spring de criar os beans corretamente. Este repositório demonstra como evitar dependências circulares utilizando soluções como a criação de um terceiro serviço ou o uso de herança com uma `BaseService`.

#### Exemplo

Suponha que temos dois serviços, `ServiceA` e `ServiceB`, que dependem um do outro.

```java
@Service
public class ServiceA {
    private final ServiceB serviceB;

    @Autowired
    public ServiceA(ServiceB serviceB) {
        this.serviceB = serviceB;
    }

    public void methodA() {
        serviceB.methodB();
    }
}

@Service
public class ServiceB {
    private final ServiceA serviceA;

    @Autowired
    public ServiceB(ServiceA serviceA) {
        this.serviceA = serviceA;
    }

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
    private final CommonService commonService;

    @Autowired
    public ServiceA(CommonService commonService) {
        this.commonService = commonService;
    }

    public void methodA() {
        commonService.commonMethod();
    }
}

@Service
public class ServiceB {
    private final CommonService commonService;

    @Autowired
    public ServiceB(CommonService commonService) {
        this.commonService = commonService;
    }

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

    protected void logError(String message, Exception e) {
        System.err.println("ERROR: " + message);
        e.printStackTrace();
    }

    // Qualquer outra lógica comum aos serviços
}


@Service
public class UserService extends BaseService {
    public void createUser() {
        logInfo("Creating user...");
        // Lógica específica para criar usuário
    }

    public void deleteUser() {
        logInfo("Deleting user...");
        // Lógica específica para deletar usuário
    }
}

@Service
public class ProductService extends BaseService {
    public void createProduct() {
        logInfo("Creating product...");
        // Lógica específica para criar produto
    }

    public void deleteProduct() {
        logInfo("Deleting product...");
        // Lógica específica para deletar produto
    }
}
```