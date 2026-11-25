# Scanner de Arquivos e Credenciais Expostas

[![Verificador de Arquivos Expostos](https://img.shields.io/badge/TAVARDT-Superficie%20de%20Ataque-5b3cf5.svg)](https://tools.tavardt.com/br/verificador-arquivos-expostos/)
[![Python](https://img.shields.io/badge/Python-3.8%2B-blue.svg)](https://www.python.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

Utilitário de varredura automatizada de superfície de ataque para detecção de arquivos de ambiente expostos, backups SQL, metadados de repositórios git e credenciais de nuvem.

O mecanismo inspeciona perímetros web para identificar falhas de configuração em servidores web e vazamentos inadvertidos de arquivos críticos antes que invasores possam explorá-los. Construído sem nenhuma dependência externa, opera nativamente em Python CLI e pipelines de segurança.

***

## Escopo de Detecção de Superfície e Arquitetura de Ameaças

A exposição acidental de ativos confidenciais na raiz de servidores web constitui um dos vetores de Acesso Inicial mais críticos da infraestrutura moderna. Configurações inadequadas em Nginx, Apache, Caddy ou buckets S3 frequentemente servem arquivos ocultos e despejos SQL diretamente a clientes não autenticados.

* **Variáveis de Ambiente e Segredos**: Verificação ativa de arquivos `.env`, `.env.local` e `.env.production` contendo credenciais de banco de dados, chaves criptográficas e tokens de APIs.
* **Metadados de Versionamento**: Varredura de caminhos como `/.git/HEAD` e `/.git/config`, impedindo que atacantes realizem a reconstrução integral do código-fonte proprietário.
* **Cópias de Segurança de Banco de Dados**: Identificação de arquivos SQL críticos como `backup.sql`, `dump.sql` e `database.sql`, que expõem tabelas e dados sensíveis de clientes.
* **Orquestração e Infraestrutura**: Inspeção de definições de contêineres em `docker-compose.yml` e vazamentos de diagnóstico via `phpinfo.php` e `config.json`.
* **Credenciais de Provedores em Nuvem**: Verificação de arquivos `/.aws/credentials`, prevenindo o comprometimento administrativo total de ambientes de computação em nuvem.
* **Alinhamento Normativo**: Estruturado em conformidade com as diretrizes do Diretrizes de Segurança da Informação da ANPD e LGPD.

***

## Matriz de Inspeção de Superfície de Ataque

<table>
  <thead>
    <tr>
      <th align="left">Rota Monitorada</th>
      <th align="left">Severidade</th>
      <th align="left">Vetor de Risco e Tipo de Ativo</th>
      <th align="left">Controle Defensivo Recomendado</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><code>/.env</code></td>
      <td>CRÍTICO</td>
      <td>Variáveis de ambiente, senhas de banco de dados e chaves privadas de API.</td>
      <td>Bloquear acesso a arquivos de ponto no servidor web ou mover para fora da raiz web.</td>
    </tr>
    <tr>
      <td><code>/.env.local</code></td>
      <td>CRÍTICO</td>
      <td>Sobregravações de desenvolvedores e segredos de homologação commitados em produção.</td>
      <td>Aplicar regras rígidas de .gitignore e rejeitar solicitações a dotfiles no proxy reverso.</td>
    </tr>
    <tr>
      <td><code>/.env.production</code></td>
      <td>CRÍTICO</td>
      <td>Credenciais ativas do sistema de produção e segredos de integração de serviços.</td>
      <td>Substituir arquivos estáticos por gerenciadores de segredos baseados em cofre seguro.</td>
    </tr>
    <tr>
      <td><code>/.git/HEAD</code></td>
      <td>CRÍTICO</td>
      <td>Ponteiro de branch do repositório git revelando a estrutura interna do projeto.</td>
      <td>Bloquear rotas /.git em nível de servidor web e remover pastas git das imagens de deploy.</td>
    </tr>
    <tr>
      <td><code>/.git/config</code></td>
      <td>CRÍTICO</td>
      <td>URLs de repositórios remotos, caminhos internos e possíveis tokens de acesso embutidos.</td>
      <td>Restringir requisições a metadados de controle de versão em todos os hosts virtuais.</td>
    </tr>
    <tr>
      <td><code>/backup.sql</code></td>
      <td>CRÍTICO</td>
      <td>Despejo integral de banco de dados com hashes de senhas e dados confidenciais.</td>
      <td>Armazenar backups exclusivamente em armazenamento em nuvem privado com criptografia.</td>
    </tr>
    <tr>
      <td><code>/dump.sql</code></td>
      <td>CRÍTICO</td>
      <td>Exportação direta de dados gerada durante manutenções manuais ou rotinas de teste.</td>
      <td>Executar scripts automáticos pós-deploy para remoção de artefatos SQL temporários.</td>
    </tr>
    <tr>
      <td><code>/database.sql</code></td>
      <td>CRÍTICO</td>
      <td>Estrutura de esquemas relacionais e dados iniciais da base de dados.</td>
      <td>Proibir a persistência de scripts de banco de dados dentro de diretórios públicos.</td>
    </tr>
    <tr>
      <td><code>/docker-compose.yml</code></td>
      <td>ALTO</td>
      <td>Topologia de contêineres, apelidos de rede interna e variáveis operacionais.</td>
      <td>Excluir manifestos de infraestrutura dos pacotes de publicação web durante o build.</td>
    </tr>
    <tr>
      <td><code>/phpinfo.php</code></td>
      <td>MÉDIO</td>
      <td>Versão do interpretador PHP, módulos ativos, caminhos de diretório e ambiente do host.</td>
      <td>Desativar chamadas phpinfo() no php.ini de produção e eliminar arquivos de teste.</td>
    </tr>
    <tr>
      <td><code>/config.json</code></td>
      <td>ALTO</td>
      <td>Configurações internas do aplicativo, endpoints de infraestrutura e parâmetros do sistema.</td>
      <td>Restringir a leitura direta de arquivos JSON ou embutir configurações no binário da aplicação.</td>
    </tr>
    <tr>
      <td><code>/.aws/credentials</code></td>
      <td>CRÍTICO</td>
      <td>Identificadores de chaves de acesso AWS e segredos de gerenciamento de nuvem.</td>
      <td>Adotar papéis IAM e perfis de instância em vez de gravar credenciais estáticas no disco.</td>
    </tr>
  </tbody>
</table>

***

## Instalação e Guia Rápido de Execução

Requer Python 3.8 ou superior utilizando exclusivamente bibliotecas padrão (`sys`, `json`, `ssl`, `argparse`, `urllib.request`). Nenhuma dependência externa ou instalação via pip é necessária.

Clone o repositório e execute a varredura contra um domínio alvo:

```bash
git clone https://github.com/TAVARDT-Brasil/exposed-files-scanner-br.git
cd exposed-files-scanner-br
python scanner.py exemplo.com.br
```

Para gerar a saída estruturada em formato JSON para pipelines automatizados:

```bash
python scanner.py exemplo.com.br -j
```

Inspecione domínios informando o protocolo explicitamente:

```bash
python scanner.py https://exemplo.com.br -j
```

Exemplo de payload JSON retornado:

```json
{
  "target": "https://exemplo.com.br",
  "status": "Critical (Severe secret or data exposure detected)",
  "inspected_routes": 12,
  "exposed_count": 1,
  "exposed_files": [
    {
      "path": "/.env",
      "url": "https://exemplo.com.br/.env",
      "status_code": 200,
      "severity": "CRITICAL",
      "description": "Environment variables and API secrets"
    }
  ],
  "recommendation": "Immediately decommission exposed files and enforce web server hardening controls."
}
```

***

## Perguntas Frequentes (FAQ Técnico)

### Como o scanner diferencia a exposição real de arquivos de respostas de páginas de erro 404 personalizadas?
O scanner valida o código de resposta HTTP 200 OK nas rotas inspecionadas. Se o servidor web responder com HTTP 200 para páginas inexistentes em vez de emitir HTTP 404 ou 403, a equipe técnica deve configurar respostas padronizadas no servidor para evitar falsos positivos de auditoria.

### Quais são os riscos reais da exposição pública do diretório `/.git/`?
A exposição das rotas `/.git/HEAD` e `/.git/config` permite que invasores façam o download de todos os objetos do repositório utilizando ferramentas automatizadas. Isso possibilita reconstruir o código-fonte integral, ler histórico de commits, recuperar senhas antigas e mapear vulnerabilidades lógicas.

### Por que o mecanismo de auditoria utiliza contextos SSL não verificados durante a verificação?
O script configura um contexto SSL unificado (`ssl.CERT_NONE`) com verificação de hostname desativada para permitir a auditoria de domínios em homologação, certificados autoassinados e servidores em transição de chaves, sem interromper o processo de varredura.

### Como a saída em JSON pode ser conectada a esteiras automatizadas de DevSecOps?
Utilizando o parâmetro `-j`, o script envia dados JSON limpos para a saída padrão. Pipelines de CI/CD podem filtrar o campo `exposed_count` via utilitários como jq, bloqueando deploys e gerando alertas automáticos caso qualquer arquivo sensível retorne código HTTP 200.

### Quais ações imediatas de hardening devem ser executadas para mitigar essas falhas?
Recomenda-se mover arquivos confidenciais para fora da pasta pública do servidor e implementar regras de bloqueio no Nginx (`location ~ /\.(?!well-known).* { deny all; }`) ou no Apache (`RedirectMatch 404 /\.(git|env)`). Backups de banco de dados nunca devem residir na raiz pública.

***

## Consultoria em Segurança Ofensiva

Para testes de intrusão, mitigação de vulnerabilidades e defesa perimetral zero trust no Brasil, consulte os especialistas da **TAVARDT Brasil**.

***

## Links Úteis

* **Portal Corporativo:** https://ag.tavardt.com/br/
* **Suíte de Diagnósticos:** https://tools.tavardt.com/br/
* **Contato Técnico:** https://ag.tavardt.com/br/contato/

***

### Licença
Distribuído sob a Licença MIT.

***

© 2026 TAVARDT. Todos os direitos reservados.
