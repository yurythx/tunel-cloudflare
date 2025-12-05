# Cloudflare Tunnel (Zero Trust) com Docker

Este projeto configura um contêiner Docker seguro e otimizado para rodar o **Cloudflare Tunnel (cloudflared)**. 

## 🛡️ Por que usar Cloudflare Tunnel?

O Cloudflare Tunnel cria uma conexão segura e criptografada entre o seu servidor e a rede da Cloudflare, sem precisar abrir portas no seu firewall (como a porta 80 ou 443) e sem expor seu IP público.

**Principais Benefícios:**
*   **Segurança Zero Trust:** Permite expor aplicações locais para a internet de forma segura, controlando quem acessa através de políticas de acesso (e-mail, IP, etc).
*   **Sem Portas Abertas:** Elimina a superfície de ataque tradicional, pois não há portas de entrada abertas no seu roteador/firewall.
*   **Proteção DDoS:** Todo o tráfego passa pela rede da Cloudflare, que filtra ataques antes de chegarem ao seu servidor.
*   **HTTPS Automático:** A Cloudflare gerencia os certificados SSL/TLS para você.

## 🚀 Como Usar

### Pré-requisitos
*   Docker e Docker Compose instalados.
*   Uma conta na Cloudflare com um domínio configurado.
*   Um token de túnel gerado no painel Zero Trust.

### Instalação

1.  **Obtenha o Token:**
    *   Acesse o [Cloudflare Zero Trust Dashboard](https://one.dash.cloudflare.com/).
    *   Vá em **Networks > Tunnels > Create a Tunnel**.
    *   Escolha o ambiente **Docker** e copie o token gerado.

2.  **Configure o Token:**
    *   Abra o arquivo `.env` neste diretório.
    *   Cole o seu token na variável `TUNNEL_TOKEN`.
    ```env
    TUNNEL_TOKEN=eyJhIjoi...
    ```

3.  **Inicie o Serviço:**
    Execute o comando abaixo para subir o contêiner:
    ```powershell
    docker-compose up -d
    ```

4.  **Verifique o Status:**
    Para garantir que tudo está rodando corretamente:
    ```powershell
    docker-compose ps
    ```
    O status deve estar `Up (healthy)`.

## ⚙️ Detalhes da Configuração (Boas Práticas)

O arquivo `docker-compose.yml` foi configurado seguindo rígidos padrões de segurança e performance:

*   **🔒 Segurança (Hardening):**
    *   `read_only: true`: O sistema de arquivos do contêiner é somente leitura, impedindo modificações maliciosas.
    *   `no-new-privileges`: Impede escalada de privilégios dentro do contêiner.
    *   `tmpfs`: Uso de memória volátil para arquivos temporários necessários.

*   **🩺 Confiabilidade:**
    *   **Healthcheck:** Monitora ativamente se o túnel está respondendo. Se travar, o Docker é notificado.
    *   **Restart Policy:** Reinicia automaticamente (`unless-stopped`) em caso de falhas ou reboot do servidor.
    *   **Versão Fixa:** Usa uma versão específica da imagem (`2025.9.1`) para evitar que atualizações automáticas quebrem a compatibilidade.

*   **📊 Recursos e Logs:**
    *   **Limites de CPU/RAM:** Impede que o túnel consuma todos os recursos do servidor (Max: 0.5 CPU / 256MB RAM).
    *   **Log Rotation:** Limita o tamanho dos logs para não encher o disco (Max: 3 arquivos de 10MB).

*   **🌐 Rede:**
    *   Cria uma rede dedicada `cloudflare-net` para facilitar a comunicação segura com outros contêineres da sua aplicação.

## 📝 Comandos Úteis

*   **Ver logs em tempo real:**
    ```powershell
    docker logs -f cloudflared-tunnel
    ```
*   **Parar o serviço:**
    ```powershell
    docker-compose down
    ```
