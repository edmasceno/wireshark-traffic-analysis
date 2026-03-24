# Análise de Protocolo: Captura de Credenciais em Tráfego HTTP (Cleartext) 🦈

Validação prática da insegurança do protocolo HTTP através da interceptação e análise de pacotes em rede local, evidenciando a exposição de dados sensíveis em tráfego sem cifragem.

## Metodologia e Ferramentas
* **Sniffer:** Wireshark (Análise de tráfego em tempo real).
* **Endpoint Alvo:** `testphp.vulnweb.com` (Aplicação deliberadamente insegura para testes de penetração).
* **Foco da Análise:** Inspeção da Camada 7 (Aplicação) para identificar payloads de autenticação.

## Execução e Filtragem
Durante a sessão de monitoramento, o maior desafio foi isolar o evento de login em meio ao ruído de rede (broadcasts, tráfego ARP e TCP secundário). 

Para otimizar a análise, apliquei o seguinte filtro de exibição:
```bash
http.request.method == "POST"
```
Este filtro isolou especificamente as requisições de envio de formulários, onde as credenciais costumam ser transmitidas no corpo da mensagem.

### Evidência de Interceptação
Ao inspecionar o pacote capturado, os dados de autenticação foram identificados imediatamente na seção `HTML Form URL Encoded`. Como o protocolo HTTP não implementa TLS/SSL, os parâmetros de `uname` e `pass` trafegam em **texto claro (cleartext)**.

![Evidência do Wireshark](evidence.png)

## Insights Técnicos
1. **Exposição de Payload:** A prática confirmou que, no HTTP, a segurança não existe mesmo que os dados não apareçam na URL (como no método GET). O payload do POST é facilmente reconstruído por qualquer analista com acesso ao segmento de rede.
2. **Análise de Camadas:** A navegação pelas camadas do pacote permitiu visualizar o encapsulamento desde o Frame (L2) até o dado da aplicação (L7).
3. **Mitigação:** O laboratório reforça a obrigatoriedade da implementação de HTTPS (TLS) para garantir a integridade e confidencialidade, impedindo ataques de *Man-in-the-Middle* (MitM) simples como este.

---
*Laboratório focado em Network Security e Análise de Protocolos.*
