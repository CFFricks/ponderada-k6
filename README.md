# Relatório sobre k6

## Introdução ao k6

O k6 da Grafana é uma ferramenta de teste de carga open-source que simplifica e torna os testes de desempenho acessíveis e produtivos para equipes de engenharia. Como uma solução gratuita, centrada no desenvolvedor e extensível, o k6 destaca-se no ecossistema de ferramentas de teste de desempenho.
Com o k6, você pode avaliar a confiabilidade e o desempenho de seus sistemas de maneira simplificada, permitindo a identificação precoce de regressões de desempenho e outros problemas. Essa ferramenta é projetada para auxiliar no desenvolvimento de aplicações resilientes e de alto desempenho, que não apenas atendam às exigências atuais mas também sejam escaláveis para suportar o crescimento futuro.
O k6 é projetado para facilitar a vida dos desenvolvedores e equipes de teste, oferecendo uma abordagem simplificada para a criação de scripts de teste de carga. Escrevendo testes como código em JavaScript, o k6 permite uma integração fácil com fluxos de trabalho de CI/CD, promovendo práticas de DevOps e agilizando o ciclo de vida de desenvolvimento de software.
Ao focar na experiência do desenvolvedor e na extensibilidade, o k6 oferece uma plataforma robusta para testar não apenas aplicações web, mas também APIs e serviços de microserviços, garantindo que seus usuários tenham a melhor experiência possível, mesmo sob carga pesada.

## Conceitos Aprendidos

Realizei os testes utilizando o k6 na rota de "get users". Essa rota é destinada a recuperar todos os usuários cadastrados de uma empresa específica.
Com isso, pude aprender vários conceitos:

### 1. **Scripting de Teste**

A habilidade de escrever scripts em JavaScript que definem o fluxo dos testes de carga. Isso inclui realizar requisições HTTP(S) para endpoints específicos, passar parâmetros e headers necessários, e tratar as respostas.

```javascript
//10 usuários, 5 minutos
import http from 'k6/http';
import { check, sleep } from 'k6';
const id = "ae13cff2-56fe-4141-ae4d-34e34fdd714e"

export const options = {
  vus: 10,
  duration: '300s',
}

const params = {
  headers: {
    'Content-Type': 'application/json',
  },
};

export default function () {
  let response = http.get(`http://localhost:3000/employer/customer/${id}`, params);

  check(response, {
    'status is 200': (r) => r.status === 200,
  });
  sleep(10)
}
```
### 2. **Virtual Users (VUs)**

Entender o conceito de Usuários Virtuais (VUs), que simulam usuários reais acessando o sistema. Isso inclui a capacidade de ajustar a carga de trabalho variando o número de VUs e o tempo de duração dos testes para simular diferentes cenários de uso.
No meu caso testei 6 cenarios diferentes e cada um teve um resultado diferente
:
- 10 usuários, 5 minutos:
  ![k6 - script1](https://github.com/CFFricks/ponderada-k6/assets/99102201/df9e64a6-eed5-4125-a4a2-5e96723aa6e4)

- 100 usuários, 5 minutos:
 ![k6 - script2](https://github.com/CFFricks/ponderada-k6/assets/99102201/42d633df-5607-4950-8a4c-e7234d2c366c)

- 1.000 usuários, 15 minutos:
 ![k6 - script3](https://github.com/CFFricks/ponderada-k6/assets/99102201/3ea39e85-45ea-4db5-b206-62cfacdb4ab8)

- 10.000 usuários, 15 minutos:
  ![k6 - script4](https://github.com/CFFricks/ponderada-k6/assets/99102201/67f90a76-839c-4f2d-b237-23ff9ff77e38)

- 1.000 usuários, 30 minutos:
  ![k6 - script5](https://github.com/CFFricks/ponderada-k6/assets/99102201/4b75b074-5eb1-4328-9fe3-d346fc4dea56)

- 10.000 usuários, 30 minutos:
![k6 - script6](https://github.com/CFFricks/ponderada-k6/assets/99102201/33fe582f-b558-452c-aa2f-855b1a3473b3)        

### 3. **Entender as métricas de cada teste**:
Pude entender o que cada uma das métricas seginificam:

- checks: Representa a porcentagem de verificações ("checks") personalizadas que passaram durante o teste. Os checks são asserções que você pode definir para validar se as respostas das requisições HTTP estão de acordo com as expectativas, como verificar o status do código de resposta ou o conteúdo da resposta.
  
- data_received e data_sent: Mostra a quantidade total de dados recebidos e enviados respectivamente pelos usuários virtuais do servidor testado durante todo o período do teste.
  
- http_req_blocked: Mostra o tempo que o usuário virtual passou bloqueado antes de iniciar a requisição HTTP. Isso pode incluir o tempo gasto em filas devido a limitações de conexões simultâneas.
  
- http_req_connecting: Representa o tempo gasto estabelecendo a conexão TCP durante as requisições HTTP.
  
- http_req_duration: É a métrica que indica o tempo total da requisição HTTP, do início ao fim, incluindo tempo de conexão, envio, espera e recebimento.
  
- http_req_failed: A porcentagem de requisições HTTP que falharam. No seu caso, essa métrica é 0%, o que significa que todas as requisições feitas tiveram sucesso.
  
- http_req_receiving: O tempo gasto recebendo a resposta do servidor.
  
- http_req_sending: O tempo gasto enviando a requisição ao servidor.
  
- http_req_tls_handshaking: O tempo gasto no handshake do TLS (se aplicável) ao estabelecer uma conexão segura.

- http_req_waiting: O tempo gasto esperando pela resposta do servidor, também conhecido como "time to first byte" (TTFB).
- 
- http_reqs: O número total de requisições HTTP feitas durante o teste.
  
- iteration_duration: A duração média de cada iteração do script de teste do k6. Uma iteração inclui a execução de todas as ações definidas no script.
  
- iterations: O número total de vezes que o script de teste foi executado.
  
- vus: O número de usuários virtuais (VUs) ativos no momento da captura da métrica.
  
- vus_max: O número máximo de usuários virtuais que foram configurados para o teste.

**Por exemplo no primeiro script que era para testar 10 usuários em 5 minutos:**

- Status 200: Indica que todas as verificações ("checks") para o status HTTP 200 (sucesso) passaram, o que significa que todas as respostas das requisições HTTP estavam corretas de acordo com os critérios do teste.
  
- Checks: 100% das checagens configuradas passaram, o que é excelente.
  
- Data received/sent: Foram recebidos 244 kB e enviados 50 kB de dados durante o teste, a uma taxa de 811 B/s e 166 B/s, respectivamente.
  
- http_req_duration: A duração média das requisições HTTP foi de aproximadamente 11.97ms, com a mais rápida sendo 503.1µs e a mais lenta 20.35ms. As percentagens (p90 e p95) indicam que 90% e 95% das requisições foram concluídas em menos de 15.07ms e 16.22ms, respectivamente.
  
- http_reqs: Foram feitas 300 requisições HTTP no total.
  
- iteration_duration: A duração média de uma iteração foi de 10.02ms, com todas as iterações sendo concluídas.
  
- VUs e vus_max: O número de VUs ativos era 10, que é o máximo configurado para este teste.

**Já no ultimo script de 10000 usuarios em 30 minutos as métricas encontradas foram:**

- Status 200: Indica que todas as verificações ("checks") para o status HTTP 200 (sucesso) passaram, o que significa que todas as respostas das requisições HTTP estavam corretas de acordo com os critérios do teste.
  
- Checks: 0% das checagens configuradas passaram, o que é ruim.
  
- http_req_failed: 100% das requisições HTTP falharam. Nenhuma das requisições feitas obteve sucesso, o que é indicado por essa métrica ser 100% e o indicador de "checks" ser 0%. Há algo impedindo as requisições de serem bem-sucedidas, como um erro de configuração, problemas de rede, ou o endpoint testado pode estar inoperante.
  
- http_reqs: 1790763. Esta é a quantidade total de requisições feitas durante o teste. No entanto, como mencionado acima, todas falharam.
  
- vus: 189. Este número representa a quantidade de Usuários Virtuais (VUs) que estavam ativos no final do teste.
  
- vus_max: 10000. Este é o número máximo de VUs que o teste poderia escalar se necessário.
  
- iteration_duration: A duração média de uma iteração é de cerca de 10 segundos, com algumas levando até 20.12 segundos no pior caso.
