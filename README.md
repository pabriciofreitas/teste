# Resolução de Problemas de Dependência Circular em Spring Boot

## Introdução

Dependências circulares ocorrem quando dois ou mais beans em uma aplicação Spring Boot dependem uns dos outros, resultando em um ciclo que impede o Spring de criar os beans corretamente. Este repositório demonstra como evitar dependências circulares utilizando soluções como a criação de um terceiro serviço ou o uso de herança com uma `BaseService`.

## Soluções para Dependência Circular

### 1. Criação de um Terceiro Serviço

Uma maneira eficaz de resolver dependências circulares é introduzir um terceiro serviço que contenha a lógica compartilhada, evitando assim a dependência direta entre os dois serviços originais.

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

Para resolver a dependência circular, criamos um terceiro serviço, CommonService.
