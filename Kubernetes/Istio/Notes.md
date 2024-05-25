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
## Canary deploy


