# Scanner de Arquivos e Credenciais Expostas

[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![Verificador de Arquivos Expostos](https://img.shields.io/badge/TAVARDT-Superficie%20de%20Ataque-5b3cf5.svg)](https://tools.tavardt.com/br/verificador-arquivos-expostos/)
[![Python](https://img.shields.io/badge/Python-3.8%2B-blue.svg)](https://www.python.org/)

Scanner de superficie de ataque para deteccao automatizada de arquivos de ambiente, backups, repositorios git e credenciais expostas em servidores web.

Desenvolvido e mantido pela divisao de seguranca ofensiva da **TAVARDT**.

***

## Escopo de Deteccao de Superficie

* **Variaveis de Ambiente e Segredos**: Verificacao de arquivos `.env`, `.env.local` e `.env.production`.
* **Metadados de Versionamento**: Inspecao de diretorios e configuracoes expostas em `/.git/HEAD` e `/.git/config`.
* **Copias de Seguranca de Banco de Dados**: Deteccao de despejos SQL como `backup.sql`, `dump.sql` e `database.sql`.
* **Orquestracao e Infraestrutura**: Checagem de configuracoes em `docker-compose.yml` e vazamento de informacoes em `phpinfo.php`.
* **Alinhamento Normativo**: Estruturado em conformidade com as diretrizes do Diretrizes de Seguranca da Informacao da ANPD e LGPD.

***

## Instalacao e Execucao

Requer Python 3.8 ou superior sem dependencias externas.

```bash
git clone https://github.com/TAVARDT-Brasil/exposed-files-scanner-br.git
cd exposed-files-scanner-br
python scanner.py empresa.com.br
```

Para gerar saida estruturada em JSON:

```bash
python scanner.py empresa.com.br -json
```

***

## Diagnostico Completo na Web

Para varreduras automatizadas e relatorios graficos avancados, acesse a ferramenta oficial:

* **Verificador de Arquivos Expostos**
* **[Portal Corporativo TAVARDT](https://ag.tavardt.com/br/)**

***

## Consultoria em Seguranca Ofensiva

Para testes de intrusao, gestao de vulnerabilidades e endurecimento de perimetro no Brasil, consulte os especialistas da **TAVARDT**.

***

## Links Úteis

* **Portal Corporativo:** https://ag.tavardt.com/br/
* **Suíte de Diagnósticos:** https://tools.tavardt.com/br/
* **Contato Técnico:** https://ag.tavardt.com/br/contato/

***

### Licença
Distribuído sob a Licença MIT. Consulte o arquivo [LICENSE](LICENSE) para obter detalhes completos.

***

© 2026 TAVARDT. Todos os direitos reservados.
