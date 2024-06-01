Camada de rede adicionada ao cluster visando monitorar e modificar em tempo real o tráfego das aplicações, visando elevar o nível de segurança e confiabilidade de todo ecossistema.

# Istio

Projeto open source que implementa service mesh visando diminuir a complexidade no gerenciamento de aplicações distruídas independente de qual linguagem ou tecnologia elas foram desenvolvidas.

### Principais recursos

* Gerenciamento de tráfego
	* Gateways (ingress e egress)
	* Load balancing
	* Gerenciamento de timeout
	* Politicas de retry
	* Circuit breaker
	* Fault injection

* Observabilidade
	* Métricas
	* Traces distribuídos
	* Logs

* Segurança
	* Main in the middle
	* mTLS
	* AAA (Authentication, authorization e audit)

# Arquitetura do Istio

O Istio utiliza com conceito chamado sidecar proxy. O istio adiciona um novo container em todos os pods gerenciados por ele, esse novo container é um proxy (envoy). Toda a comunicação é utilizada através de proxies.

A configuração dos proxies é configurada automaticamente por um serviço chamado IstioD.

<hr>

# Gerenciamento de tráfego

* Ingress Gateway - Recebe tráfego de fora do cluster
* Virtual Service - Roteador de tráfego interno
	* Match - Baseado em path, header, etc
	* Retries - Politica de retry para envio de tráfego para o Pod
	* Fault Injection - Podemos simular falhas no serviço
	* Timeout 
	* Subsets (v1, v2...)

	 * Destination rule
		 * Podemos usar destination rule para fazer balanceamento de tráfego entre subsets
		* Ingress gateway -> Virtual Service -------> Destination rule (v1) -> Workload v1
		                              -------> Destination rule (v2) -> Workload v2

<hr>
## Traffic shifting

A configuração do traffic shifting é realizada através do virtual service, por exemplo:

```
---
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: hello
spec:
  hosts:
  - "hello"
  http:
  - route:
    - destination:
        host: hello
        subset: v1
      weight: 80
    - destination:
        host: hello
        subset: v2
      weight: 20
```

```yaml
---
apiVersion: networking.istio.io/v1beta1
kind: DestinationRule
metadata:
  name: hello
spec:
  host: hello
  subsets:
  - name: v1
    labels:
      version: v1
  - name: v2
    labels:
      version: v2
```

## Tipo de load balancers no ingress

No ingress, podemos especificar o time de load balancer que sera utilizado, o default é o algoritmo round robin.

```yaml
---
apiVersion: networking.istio.io/v1beta1
kind: DestinationRule
metadata:
  name: hello
spec:
  host: hello
  trafficPolicy:
	loadBalancer:
	  simple: ROUND_ROBIN
  subsets:
  - name: v1
    labels:
      version: v1
  - name: v2
    labels:
      version: v2
```

Também é possível implementar traffic policy nos subets.

```yaml
---
apiVersion: networking.istio.io/v1beta1
kind: DestinationRule
metadata:
  name: hello
spec:
  host: hello
  trafficPolicy:
	loadBalancer:
	  simple: ROUND_ROBIN
  subsets:
  - name: v1
    labels:
      version: v1
    trafficPolicy:
	  loadBalancer:
	    simple: LEST_CONN

  - name: v2
    labels:
      version: v2
```

## Stick session e consistent hash

Podemos usar o conceito de consistent hash, que a partir disso, mantem os usuários acessando a mesma versão, fazendo que a experiência se mantenha.

OBS: Consistent hash não funciona com traffic shifting

Podemos usar consistent hash com base nas seguintes informações:

* httpHeaderName
* httpCookie
* UseSourceIP
* httpQueryParameterName

```yaml

```

## Fault injection

Podemos utilizar o conceito de fault injection para simular falhas nos serviços.

## Circuit breaker

Circuit breaker permite cortar a conexão com microsservico que está causando gargalo.

Exemplo: Funciona como um disjuntor, quando recebe mais garga do que aguenta, ele corta o circuito.

<hr>

## Gateways

O gateway no istio é a borda do cluster. O gateway também possui um sidecard, possibilidando termos controle total do tráfego de rede.

