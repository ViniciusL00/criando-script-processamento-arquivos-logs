# 🐧 Módulo 1: Configurando Scripts e Permissões de Arquivos

## 📁 Criação de Diretórios no Linux

### ✅ Comando básico:

```bash
mkdir nome_do_diretorio
```

### ⚠️ Erro comum:

```bash
mkdir myapps/logs
# Saída: mkdir: cannot create directory ‘myapps/logs’: No such file or directory
```

Isso acontece porque o diretório `myapps` ainda **não existia**. O sistema não cria diretórios "pais" automaticamente.

### 🛠️ Soluções:

#### 🔸 Opção 1: Criar os diretórios um a um

```bash
mkdir myapps
mkdir myapps/logs
```

#### 🔸 Opção 2: Criar tudo de uma vez com `-p`

```bash
mkdir -p myapps/logs
```

O parâmetro `-p` cria todos os diretórios intermediários necessários.

---

## 📄 Criando e Editando Arquivos de Log

### ✏️ Criar um arquivo vazio:

```bash
touch myapp-backend.log
```

Ou crie com conteúdo direto:

```bash
echo "2024-09-01 10:05:21 ERROR: Database connection failed." > myapp-backend.log
```

### 📋 Inserir conteúdo grande de forma eficiente:

Usando redirecionamento com `cat`:

```bash
cat << EOF > myapp-backend.log
2024-09-01 10:05:21 ERROR: Database connection failed.
2024-09-01 10:06:10 INFO: Retrying database connection...
2024-09-01 10:06:15 INFO: Database connection established.
...
EOF
```

Tudo entre `<< EOF` e `EOF` será salvo dentro do arquivo.

### 🧠 Cuidado: Não execute arquivos `.log`

```bash
./myapp-backend.log
# Resultado: várias mensagens de "command not found"
```

Arquivos `.log` **não são scripts executáveis**! São apenas textos para leitura. O terminal tenta rodar cada linha como se fosse um comando. Resultado: erro em cima de erro!

### 📝 Editar manualmente (modo old school):

```bash
nano myapp-backend.log
```

Comandos no `nano`:

* `Ctrl+O` para salvar
* `Ctrl+X` para sair

---

## ⚙️ Criando Script para Processamento de Logs

### 📂 Estrutura inicial:

1. Criar um diretório para scripts:

```bash
mkdir scripts-linux
cd scripts-linux/
```

2. Criar o script com `vim`:

```bash
vim monitoramento-logs.sh
```

### 🧠 Modo de edição no `vim`:

* Pressione `i` para entrar no modo **insert** (edição)
* Digite o conteúdo do script
* Pressione `ESC` para sair do modo insert
* Para **salvar**: `:w`
* Para **sair**: `:q`

### 🖊️ Conteúdo básico do script:

```bash
#!/bin/bash

# Caminho do diretório de logs
LOG_DIR="../myapps/logs"

# Mensagem de verificação
echo "verificando logs no diretório $LOG_DIR"
```

### 🔐 Permissão de execução:

Após criar o script, torne-o executável:

```bash
chmod +x monitoramento-logs.sh
```

E execute com:

```bash
./monitoramento-logs.sh
```

---

## 🚀 Dicas do Terminal

* Sempre use `mkdir -p` para evitar erros com diretórios aninhados
* Para criar logs de teste, use `cat << EOF` com conteúdo dentro
* Nunca execute `.log` direto — visualize com `cat`, `less`, `nano` ou `tail`
* Use `vim` se quiser editar arquivos 😎

---

# 📁 Gerenciando Permissões no Linux

Este documento é um resumo detalhado da aula sobre permissões no Linux, com foco em scripts, notações e gerenciamento de acesso. 🎓🐧

---

## 🛠️ Alterando Permissões com `chmod`

### ✅ Comando usado:
```bash
chmod 755 monitoramento-logs.sh
```

Esse comando altera as permissões do arquivo `monitoramento-logs.sh`, permitindo que ele seja executado como um script.

### 📊 Significado do `755`:
- O número `755` está na **notação octal**.
- Ele representa as permissões separadas por **classes de usuários**:

| Classe       | Permissões                        | Descrição                          |
|--------------|-----------------------------------|------------------------------------|
| Usuário (7)  | rwx (leitura, escrita e execução) | Dono do arquivo tem total controle |
| Grupo (5)    | r-x (leitura e execução)          | Pode ler e executar                |
| Outros (5)   | r-x (leitura e execução)          | Pode ler e executar                |

---

## 👥 Classes de Usuário

| Classe       | Símbolo | Descrição                                     |
|--------------|---------|-----------------------------------------------|
| Usuário      | `u`     | O dono do arquivo                             |
| Grupo        | `g`     | Usuários que pertencem ao mesmo grupo do dono |
| Outros       | `o`     | Todos os outros usuários                      |
| Todos        | `a`     | Todos os usuários (`u`, `g` e `o`)            |

---

## 🔢 Notação OCTAL

| Número | Permissões | Significado            |
|--------|------------|------------------------|
| 0      | ---        | Nenhuma permissão      |
| 1      | --x        | Apenas execução        |
| 2      | -w-        | Apenas escrita         |
| 3      | -wx        | Escrita + execução     |
| 4      | r--        | Apenas leitura         |
| 5      | r-x        | Leitura + execução     |
| 6      | rw-        | Leitura + escrita      |
| 7      | rwx        | Todas as permissões    |

Exemplo:
```bash
chmod 764 arquivo.txt
```
- `7` (usuário): leitura, escrita, execução
- `6` (grupo): leitura, escrita
- `4` (outros): somente leitura

---

## ✍️ Notação SIMBÓLICA

Usa símbolos ao invés de números para atribuir permissões.

### Formato:
```bash
chmod [classe][operador][permissão] arquivo
```

| Operador | Significado                      |
|----------|----------------------------------|
| `+`      | Adiciona permissão               |
| `-`      | Remove permissão                 |
| `=`      | Define exatamente as permissões  |

### Exemplos:
```bash
chmod u+x script.sh     # Adiciona execução para o dono
chmod g-w script.sh     # Remove escrita do grupo
chmod o=r script.sh     # Define somente leitura para outros
```

---

## 🧠 Comparação: OCTAL vs SIMBÓLICA

| Critério         | Notação OCTAL             | Notação SIMBÓLICA                  |
|------------------|---------------------------|------------------------------------|
| Forma            | Numérica (`755`, `644`)   | Simbólica (`u+x`, `g-w`)           |
| Clareza          | Direta e compacta         | Mais expressiva e legível          |
| Controle fino    | Não muito flexível        | Flexível e específica por classe   |
| Uso mais comum   | Scripts, automações       | Ajustes pontuais e interativos     |

📌 Use **octal** quando quiser definir todas as permissões de uma vez.  
📌 Use **simbólica** quando quiser **modificar** permissões específicas sem mexer no resto.

---

## 📊 Explicando as permissões

### 📊 1: Permissões listadas
```bash
-rwxr-xr-x 1 user user 86 Jun  2 15:57 monitoramento-logs.sh
```
- `-rwxr-xr-x` = permissões:
  - `rwx` → usuário
  - `r-x` → grupo
  - `r-x` → outros
- `user user` → dono e grupo
- `86` → tamanho em bytes

### 📊 2: Executando o script
```bash
./monitoramento-logs.sh
```
- O script executa e exibe:
```
Verificando logs no diretório ../myapps/logs
```

Ou seja, a permissão de execução (`x`) está funcionando como esperado. ✅

---

## ✅ Conclusão

- Criar scripts com permissões corretas
- A diferença entre notações octal e simbólica
- Como usar `chmod` corretamente no terminal! 🥷💻

```bash
chmod +conhecimento você.sh
```

---

# 👥 Gerenciando Grupos e Usuários no Linux

## 🧑‍💻 Criando um Novo Usuário

Para criar um novo usuário no sistema Linux, usamos o comando:

```bash
sudo adduser [nome_do_usuario]
```

## 💡 Esse comando cria:

- Um novo diretório home para o usuário
- As permissões e arquivos padrões dentro desse diretório

---

## ✅ Verificando se o usuário foi criado com sucesso:

```bash
cat /etc/passwd
```
- Esse arquivo contém os registros de todos os usuários do sistema. Use grep pra filtrar mais fácil, exemplo:

```bash
cat /etc/passwd | grep [nome_do_usuario]
```

---

# 👪 Criando e Gerenciando Grupos

## ➕ Criar um grupo:

```bash
sudo groupadd [nome_do_grupo]
```

---

## 🔍 Verificar se o grupo foi criado:

```bash
getent group [nome_do_grupo]
```

---

## ➕ Adicionar usuário a um grupo:

```bash
sudo usermod -aG [nome_do_grupo] [nome_do_usuario]
```
- ⚠️ A flag -aG serve para adicionar o usuário ao grupo sem removê-lo dos outros grupos que ele já pertence.

---

## ✅ Confirmar que o usuário está no grupo:

```bash
getent group [nome_do_grupo]
```

---

# 📂 Dando acesso de grupo a um diretório/projeto

1. Entre no diretório do projeto:

```bash
cd /caminho/do/projeto
```

---

2. Veja permissões do diretório:

```bash
ls -ld
``` 
- Isso mostra a permissão do diretório e a qual usuário e grupo ele pertence.

---

3. Volte pro diretório pai e altere o grupo do projeto:

```bash
sudo chown -R :[nome_do_grupo] /home/diretorio
```
- 🌀 A flag -R aplica a alteração recursivamente a todos os subdiretórios e arquivos.

--=

# 🔄 Alternando entre usuários

- Para mudar de usuário e simular a experiência como se fosse ele, use:

```bash
su - [nome_do_usuario]
```

- 🔐 Isso inicia uma nova sessão de shell como aquele usuário. Perfeito pra testar se as permissões estão certas.

---

* 💡 Dicas finais

- Use groups [nome_do_usuario] para ver todos os grupos de um usuário.

- Você pode mudar permissões com chmod, e donos com chown.

- Gerenciar grupos é essencial para segurança e organização no sistema!

---

## ✅ Conclusão

| Ação                       | Comando                          |
| -------------------------- | -------------------------------- |
| Criar usuário              | `sudo adduser nome`              |
| Verificar criação          | `cat /etc/passwd`                |
| Criar grupo                | `sudo groupadd grupo`            |
| Verificar grupo            | `getent group grupo`             |
| Adicionar usuário ao grupo | `sudo usermod -aG grupo usuario` |
| Verificar associação       | `getent group grupo`             |
| Mudar grupo dono da pasta  | `sudo chown -R :grupo /caminho`  |
| Alternar usuário           | `su - usuario`                   |

---

- Criar um script bash no Linux, para processar logs de uma aplicação, utilizando o editor de texto vim.
- Navegar entre os modos de inserção e comando no editor de texto vim, clicando na tecla i para o modo inserção e na tecla ESC para o modo de comando.
- Salvar arquivos no vim com o comando :w e sair do vim com o comando :q.
- Utilizar o comando chmod para adicionar e remover permissões de arquivos e diretórios.
- Usar as notações octal e a simbólica para gerenciar permissões no Linux.
- Gerenciar proprietários e grupos de arquivos e diretórios, através do comando chown.

---

# 🐧 Módulo 2: Fazendo Busca e Usando Filtros

---
# 🔍 Encontrando Arquivos com `find` e Laços de Repetição no Bash

## 📁 Objetivo da Aula

Aprender a:
- Usar o comando `find` para localizar arquivos.
- Usar `while` para processar os arquivos encontrados.
- Redirecionar a saída com `|` (pipe).
- Entender o uso de `IFS`, `read`, `-print0` e delimitadores nulos.
- Executar scripts com o terminal.

---

## 🔎 Buscando arquivos com `find`

### Comando base:

```bash
find . -name "*.log"
```

🧠 Esse comando busca **todos os arquivos terminados em `.log`** no diretório atual (`.`) e seus subdiretórios.

---

## 📂 Acessando o diretório do script

```bash
cd scripts-linux/
```

E abrimos o script com o editor:

```bash
vim monitoramento-logs.sh
```

---

## ✍️ Editando o script

Dentro do `vim`, começamos com:

```bash
#!/bin/bash

LOG_DIR="../myapps/logs"
echo "Verificando logs no diretório $LOG_DIR"
```

Substituímos o `.` do find pela variável:

```bash
find $LOG_DIR -name "*.log"
```

---

## 🔁 Laços de Repetição (while loop)

### O que é?

Laço de repetição executa **ações em sequência enquanto uma condição for verdadeira**.

### Estrutura:

```bash
while [ condição ]; do
  # comandos
done
```

---

## 🧪 Aplicando o pipe `|`

Queremos pegar a saída do `find` e **redirecionar para um `while`**:

```bash
find $LOG_DIR -name "*.log" | while IFS= read linha; do
  echo "Arquivo: $linha"
done
```

Mas esse método não lida bem com **espaços ou caracteres especiais**.

---

## 🧠 Melhor prática com `-print0` e `read -d ''`

### Explicando a estrutura completa:

```bash
find $LOG_DIR -name "*.log" -print0 | while IFS= read -r -d '' arquivo; do
  echo "Arquivo encontrado: $arquivo"
done
```

### 🔍 Quebrando isso:

|   Parte   |                            Explicação                               |
|-----------|---------------------------------------------------------------------|
| `-print0` | Faz o `find` separar os resultados com um caractere **nulo**        |
| `IFS=`    | Internal Field Separator vazio — evita que espaços quebrem linhas   |
| `read -r` | Lê a linha sem interpretar barras invertidas                        |
| `-d ''`   | Define que o delimitador é nulo                                     |
| `arquivo` | Variável que recebe o caminho de cada arquivo encontrado            |

---

## ✅ Executando o script

```bash
./monitoramento-logs.sh
```

> 🔐 Lembre-se de dar permissão de execução se necessário:

```bash
chmod +x monitoramento-logs.sh
```

---

## 📌 Resumo Final

- `find` é usado pra buscar arquivos recursivamente.
- `while` permite processar cada resultado do `find`.
- O uso de `-print0` + `read -d ''` evita problemas com nomes de arquivos complicados.
- Pipe (`|`) conecta saída e entrada entre comandos.

---

## 🧠 Exemplo Final do Script

```bash
#!/bin/bash

LOG_DIR="../myapps/logs"
echo "Verificando logs no diretório $LOG_DIR"

find $LOG_DIR -name "*.log" -print0 | while IFS= read -r -d '' arquivo; do
  echo "Arquivo encontrado: $arquivo"
done
```
---

# 🔁 Entendendo o Laço de Repetição `while` no Bash

## 📌 O que é?

O `while` é um laço de repetição no Bash que **executa um bloco de comandos repetidamente enquanto uma condição for verdadeira**.

Ele verifica a condição **antes de cada iteração**. Quando a condição se torna falsa, o loop termina.

---

## 🧠 Estrutura básica:

```bash
while [ condição ]; do
    # comandos a serem executados enquanto a condição for verdadeira
done
```

---

## 🧪 Exemplo prático: Contando de 1 a 5

```bash
#!/bin/bash

contador=1

while [ $contador -le 5 ]; do
    echo "Contador: $contador"
    ((contador++))  # incrementa o valor de contador em 1
done
```

---

### 🔍 Explicação do script:

- `contador=1` 👉 Inicializa a variável `contador` com o valor 1.
- `while [ $contador -le 5 ]` 👉 Verifica se `contador` é menor ou igual a 5.
- `echo` 👉 Exibe o valor atual da variável.
- `((contador++))` 👉 Incrementa o valor de `contador` em 1 a cada iteração.
- O loop termina quando `contador` atinge 6.

---

## 📤 Saída esperada:

```
Contador: 1
Contador: 2
Contador: 3
Contador: 4
Contador: 5
```

---

## 💡 Quando usar?

Use o `while` quando:
- Você **não sabe previamente** quantas vezes algo deve se repetir;
- Quer **executar enquanto** uma condição for verdadeira.

---

## ✅ Dica prática

Evite loops infinitos! Sempre certifique-se de que **existe uma condição de parada** ou que a variável de controle será modificada corretamente.

---

📚 **Resumo Rápido**:  
O `while` é seu aliado para repetições baseadas em condições, útil em automações e scripts de verificação.

---

# 🔍 Comandos de Busca no Linux

Além do comando `find`, o Linux oferece **vários comandos poderosos para localizar arquivos e diretórios**. Aqui estão alguns dos principais:

---

## 📁 `locate`

O comando `locate` utiliza um **banco de dados indexado** para encontrar arquivos rapidamente.

✅ **Vantagens:** Super rápido.  
⚠️ **Atenção:** Pode não refletir arquivos criados recentemente, pois depende do banco estar atualizado.

🛠️ **Atualizar banco de dados:**  
```bash
sudo updatedb
```

📌 **Sintaxe:**  
```bash
locate nome_do_arquivo
```

---

## ⚙️ `which`

Usado para localizar **executáveis** nos diretórios definidos na variável de ambiente `PATH`.

📌 **Sintaxe:**  
```bash
which nome_do_programa
```

🔎 **Exemplo:**  
```bash
which bash
```

---

## 🧭 `whereis`

Mais completo que o `which`, o `whereis` procura:

- Executáveis
- Arquivos de manual
- Códigos-fonte

📌 **Sintaxe:**  
```bash
whereis nome_do_programa
```

🔎 **Exemplo:**  
```bash
whereis gcc
```

---

## 🧬 `grep` + `ls`

Embora o `grep` seja usado para buscar **conteúdo dentro de arquivos**, também pode ser usado para filtrar **nomes de arquivos** quando combinado com o `ls`.

📌 **Sintaxe combinada:**  
```bash
ls | grep padrão
```

🔎 **Exemplo:**  
```bash
ls | grep ".log"
```

---

## 🧠 Conclusão

Esses comandos ampliam bastante sua capacidade de **navegar, buscar e administrar arquivos** no Linux:

| Comando   | Função Principal                             |
|-----------|----------------------------------------------|
| `find`    | Busca arquivos de forma detalhada no sistema |
| `locate`  | Busca rápida via banco de dados              |
| `which`   | Localiza executáveis no PATH                 |
| `whereis` | Busca executável, manual e fonte             |
| `grep`    | Filtra nomes de arquivos com padrões         |

---

# 🧹 Filtrando Logs com `grep` no Linux

## 📌 O que é o `grep`?

O comando `grep` é usado para **buscar padrões de texto dentro de arquivos**. É uma das ferramentas mais poderosas e populares para análise de logs no Linux.

---

## 🔍 Buscando por "ERROR" em arquivos de log

```bash
grep "ERROR" myapp-backend.log
```

🟡 Isso vai **buscar todas as linhas** dentro do arquivo `myapp-backend.log` que contenham exatamente a palavra `ERROR` (em maiúsculas).

⚠️ O `grep` é sensível a maiúsculas e minúsculas!  
Se quiser ignorar isso, use a opção `-i`.

---

## 🛠️ Redirecionando a saída com `>` (maior que)

```bash
grep "ERROR" myapp-backend.log > logs-erro
```

📤 Isso **redireciona a saída** do `grep` (ou seja, as linhas com "ERROR") para um novo arquivo chamado `logs-erro`.

📄 Agora, ao rodar:

```bash
cat logs-erro
```

Você verá **somente os erros filtrados**, sem alterar o arquivo original.

---

## ✍️ Editando o script `monitoramento-logs.sh`

1. Abra o script:
```bash
vim monitoramento-logs.sh
```

2. Substitua a linha:
```bash
echo "Arquivo encontrado $arquivo"
```

Por esta:
```bash
grep "ERROR" "$arquivo" > "${arquivo}.filtrado"
```

---

## 🧠 O que essa linha faz?

```bash
grep "ERROR" "$arquivo" > "${arquivo}.filtrado"
```

🔍 **Busca no arquivo** representado pela variável `$arquivo` todas as linhas com "ERROR".  
📂 **Cria um novo arquivo** com o mesmo nome + `.filtrado` contendo apenas os erros.

✅ Exemplo:
Se `$arquivo` for `myapp-backend.log`, será criado:

```
myapp-backend.log.filtrado
```

---

## 📁 Verificando os arquivos filtrados

Navegue até a pasta dos logs:

```bash
cd myapps/logs
```

Liste os arquivos:

```bash
ls
```

Você verá os arquivos `.log` originais e os novos `.filtrado`.

---

## 📌 Resumo

| Comando | Função |
|---------|--------|
| `grep "ERROR" arquivo` | Filtra linhas com "ERROR" |
|    `>`  | Redireciona a saída para outro arquivo   |
| `${arquivo}.filtrado` | Nome do novo arquivo com os erros filtrados |

🧰 Usar `grep` com redirecionamento é uma técnica essencial para **analisar grandes arquivos de log** e **extrair só o que importa**.

---

# 📤 Operador `>` no Linux

O operador `>` é usado para **redirecionar a saída de um comando para um arquivo**.

## ✍️ Exemplos de uso:

### 🔸 Redirecionar saída:
```bash
echo "Olá mundo!" > mensagem.txt
```
📁 Isso cria (ou sobrescreve) o arquivo `mensagem.txt` com o conteúdo `"Olá mundo!"`.

---

### 🔸 Criar um arquivo vazio:
```bash
> novo-arquivo.txt
```
📂 Cria um arquivo chamado `novo-arquivo.txt`.

⚠️ **Se já existir**, o conteúdo será **apagado** (zerado), mas o arquivo continua lá.

---

# 🛠️ Modificando Conteúdo de Arquivos no Linux

Neste conteúdo, aprendemos como **filtrar informações sensíveis** e **modificar arquivos** usando comandos como `grep`, `>>` e `sed`.

---

## 🔍 1. Filtrando dados sensíveis com `grep` e `>>`

```bash
grep "SENSITIVE_DATA" $arquivo >> "${arquivo}.filtrado"
```
---

### ✅ Explicação:

- ```grep "SENSITIVE_DATA"```: procura por linhas que contenham a palavra ```SENSITIVE_DATA``` no arquivo.
- ```$arquivo```: é o nome do arquivo atual (dado pelo while do script).
- ```>> "${arquivo}.filtrado": redireciona a saída para o arquivo ${arquivo}.filtrado```, sem apagar o que já existe nele.
- 👉 Isso é diferente de > que sobrescreve o conteúdo!

---

## ✂️ 2. Substituindo informações sensíveis com sed

**Exemplo direto no terminal:**

```bash
sed 's/User password is .*/User password is REDACTED/g' myapp-backend.log
```

---

### ✅ Explicação:

- ```sed```: editor de fluxo (stream editor) usado para fazer alterações de texto.
- ```'s/.../.../g'```: é a sintaxe de substituição do sed:
- ```s:``` inicia a substituição
- ```User password is .*```: padrão de texto a ser encontrado. 
- O ```.*``` significa "qualquer coisa depois".
- ```User password is REDACTED```: o texto que substituirá o original.
- ```g```: significa global, ou seja, aplica a substituição em todas as ocorrências da linha.

---

## 🔁 3. Substituições múltiplas no script

```bash
sed -i 's/User password is .*/User password is REDACTED/g' "${arquivo}.filtrado"
sed -i 's/User password reset request with token .*/User password reset request with token REDACTED/g' "${arquivo}.filtrado"
sed -i 's/API key leaked: .*/API key leaked: REDACTED/g' "${arquivo}.filtrado"
sed -i 's/User credit card last four digits: .*/User credit card last four digits: REDACTED/g' "${arquivo}.filtrado"
sed -i 's/User session initiated with token: .*/User session initiated with token: REDACTED/g' "${arquivo}.filtrado"
```

---

### ✅ Explicação linha por linha:

- ```-i```: faz a edição diretamente no arquivo, sem precisar redirecionar para outro.
- Cada ```sed``` busca por um padrão específico e substitui pelo termo REDACTED (redigido).
- ```${arquivo}.filtrado```: é o arquivo onde estão sendo feitas as modificações.

---

### 🔐 Isso ajuda a:

- Proteger dados sensíveis.
- Garantir que o arquivo mantido para análise esteja limpo.
- Evitar exposição de senhas, tokens e cartões de crédito.

## 📌 Conclusão

Este processo de filtragem + substituição automatizada:

1. Filtra linhas sensíveis com ```grep + >>```.
2. Aplica várias regras com ```sed``` para mascarar dados. 
3. Mantém os dados organizados e seguros.
4. Gera arquivos ```.filtrado``` prontos para serem analisados ou enviados sem riscos! 🔐🐧

- Ideal para scripts de auditoria, monitoramento de segurança e sistemas que lidam com dados privados.

---

# 📁 Módulo 3: Lidando com Arquivos

## 🗂️ Aula: Ordenando Informações em Arquivos

Aprendemos como **ordenar dados** de arquivos de log usando o comando `sort`, e como incorporar isso em um **script de monitoramento automatizado**.

---

## 🔤 Comando `sort`

O comando `sort` serve para **ordenar linhas de texto** de um arquivo.

### 🧪 Exemplo básico:
```bash
sort myapp-backend.log.filtrado
```
- Ordena as linhas por padrão alfabético e numérico (de A a Z, ou do menor para o maior).
- A **saída é exibida no terminal**, mas **não altera o arquivo original**.

---

## 🔁 Ordem Reversa com `-r`

```bash
sort -r myapp-backend.log.filtrado
```
- Exibe as linhas **da mais recente para a mais antiga** (ordem reversa).
- Ideal para ver os eventos mais recentes primeiro.

---

## 💾 Salvando a ordenação com `-o`

```bash
sort myapp-backend.log.filtrado -o logs-ordenados
```
- Redireciona a saída ordenada para o arquivo `logs-ordenados`.
- Agora é possível usar `cat logs-ordenados` para ver o conteúdo já ordenado.

---

## 🛠️ Incrementando o Script

### 🚀 Script atualizado:
```bash
#!/bin/bash

LOG_DIR="../myapp/logs"

echo "Verificando logs no diretorio $LOG_DIR"

find $LOG_DIR -name "*.log" -print0 | while IFS= read -r -d '' arquivo; do
    grep "ERROR" "$arquivo" > "${arquivo}.filtrado"
    grep "SENSITIVE_DATA" "$arquivo" >> "${arquivo}.filtrado"

    sed -i 's/User password is .*/User password is REDACTED/g' "${arquivo}.filtrado"
    sed -i 's/User password reset request with token .*/User password reset request with token REDACTED/g' "${arquivo}.filtrado"
    sed -i 's/API key leaked: .*/API key leaked: REDACTED/g' "${arquivo}.filtrado"
    sed -i 's/User credit card last four digits: .*/User credit card last four digits: REDACTED/g' "${arquivo}.filtrado"
    sed -i 's/User session initiated with token: .*/User session initiated with token: REDACTED/g' "${arquivo}.filtrado"

    sort "${arquivo}.filtrado" -o "${arquivo}.filtrado"
done
```

### ✅ O que foi adicionado:
- `sort "${arquivo}.filtrado" -o "${arquivo}.filtrado"`:
  - Ordena os dados filtrados.
  - **Sobrescreve** o conteúdo do arquivo com os dados já ordenados.

---

## 💡 Conclusão

✅ Com o uso do `sort`, conseguimos:
- Organizar logs por ordem cronológica.
- Priorizar os eventos mais recentes (com `-r`).
- Automatizar a ordenação diretamente no script.

⚙️ Isso melhora a **legibilidade dos logs**, ajuda na **análise de erros** e mantém os dados organizados para auditoria e segurança.

---

## 📎 Comandos úteis

| Comando | Função |
|--------|--------|
| `sort arquivo` | Ordena o conteúdo |
| `sort -r` | Ordem reversa |
| `sort -o novo-arquivo` | Salva ordenado |
| `cat arquivo` | Visualiza conteúdo |
| `vim monitoramento-logs.sh` | Edita o script |
| `./monitoramento-logs.sh` | Executa o script |

---

### 🧠 **Resumo: Comando `sort` no Linux**  

O comando `sort` é uma ferramenta poderosa no Linux usada para **ordenar linhas de texto** em arquivos ou diretamente da saída de outros comandos. Ele pode ordenar **alfabeticamente** ou **numericamente**, dependendo das opções usadas. É um comando essencial no processamento de dados em arquivos de texto. 🔍📂

---

### ⚙️ **Principais opções do `sort`**:

| Opção | Significado | Exemplo |
|-------|-------------|---------|
| `-r` | 🔄 Ordena em ordem reversa (decrescente) | `sort -r arquivo.txt` |
| `-n` | 🔢 Ordenação numérica (em vez de alfabética) | `sort -n numeros.txt` |
| `-k` | 📊 Especifica a coluna usada para ordenar | `sort -k 2 arquivo.txt` (ordena pela 2ª coluna) |
| `-u` | 🧹 Remove duplicatas (deixa apenas uma instância) | `sort -u arquivo.txt` |
| `-t` | 🧱 Define um delimitador de campo | `sort -t , -k 1 arquivo.csv` |
| `-o` | 💾 Salva a saída em um arquivo especificado | `sort entrada.txt -o saida.txt` |
| `-f` | 🔠 Ignora diferenças entre maiúsculas e minúsculas | `sort -f arquivo.txt` |

---

### 💡 **Por que usar o `sort`?**

- Facilita a **organização de dados** de forma rápida e eficiente.
- Pode ser combinado com outros comandos como `cat`, `grep`, `cut`, etc.
- Ideal para **scripts de automação**, **filtragem de logs**, e **tratamento de arquivos CSV ou TXT**.

---

# 🧹 Removendo Duplicatas em Arquivos de Log com `uniq`

## 📚 Visão Geral

Durante o monitoramento de logs, é comum que algumas linhas apareçam duplicadas. Isso acontece, por exemplo, quando a mesma mensagem contém as palavras-chave **ERROR** e **SENSITIVE_DATA**, fazendo com que seja adicionada duas vezes ao arquivo filtrado.

**Exemplo de duplicação:**

- 2024-09-03 12:00:00 ERROR: SENSITIVE_DATA: Database backup contains sensitive information.
- 2024-09-03 12:00:00 ERROR: SENSITIVE_DATA: Database backup contains sensitive information.


⚠️ Duplicatas tornam a leitura e análise mais difíceis — precisamos removê-las!

---

## 🧰 Utilizando o Comando `uniq`

O comando `uniq` serve para **remover linhas duplicadas consecutivas** de arquivos de texto.

### 🧪 Sintaxe:
```bash
uniq nome_do_arquivo
```

✅ Ele mantém apenas a primeira ocorrência de cada linha.

⚠️ Para funcionar corretamente, o arquivo deve estar ordenado com **sort**, pois **uniq** só reconhece duplicatas consecutivas.

---

## 💾 Salvando sem Duplicatas

```bash
uniq myapp-backend.log.filtrado > logs-sem-duplicatas
```
📝 Isso cria um novo arquivo **logs-sem-duplicatas** contendo apenas linhas únicas.

✅ Verifique com:

```bash
cat logs-sem-duplicatas
```

---

## 🛠️ Atualizando o Script de Monitoramento

Vamos incorporar o **uniq** ao nosso script **monitoramento-logs.sh**:

🔧 Script Completo:

```bash
#!/bin/bash

LOG_DIR="../myapp/logs"

echo "Verificando logs no diretorio $LOG_DIR"

find $LOG_DIR -name "*.log" -print0 | while IFS= read -r -d '' arquivo; do
    grep "ERROR" "$arquivo" > "${arquivo}.filtrado"
    grep "SENSITIVE_DATA" "$arquivo" >> "${arquivo}.filtrado"

    sed -i 's/User password is .*/User password is REDACTED/g' "${arquivo}.filtrado"
    sed -i 's/User password reset request with token .*/User password reset request with token REDACTED/g' "${arquivo}.filtrado"
    sed -i 's/API key leaked: .*/API key leaked: REDACTED/g' "${arquivo}.filtrado"
    sed -i 's/User credit card last four digits: .*/User credit card last four digits: REDACTED/g' "${arquivo}.filtrado"
    sed -i 's/User session initiated with token: .*/User session initiated with token: REDACTED/g' "${arquivo}.filtrado"

    sort "${arquivo}.filtrado" -o "${arquivo}.filtrado"

    uniq "${arquivo}.filtrado" > "${arquivo}.unico"
done
```

---

📌 Com isso:

- 🔍 Logs são filtrados por erro e dados sensíveis.
- ✂️ Dados sensíveis são redigidos com **REDACTED**.
- 📆 Os dados são ordenados por data.
- 🔁 Duplicatas são removidas com **uniq**.

---

## 🧪 Após executar:

1. Execute o script:

```bash
./monitoramento-logs.sh
```

2. Verifique os arquivos:

```bash
ls ../myapp/logs
```

3. Veja o conteúdo sem duplicatas:

```bash
cat myapp-backend.log.unico
```

---

## ✅ Conclusão

O uso combinado de **sort** + **uniq** é uma técnica poderosa para limpar arquivos de log, melhorando a legibilidade e a análise posterior. 🔍📁

Essa prática é essencial para scripts de automação e monitoramento eficazes! 🚀

---

# 🔍 Comparando Arquivos com `diff` no Linux

## 📚 Por que comparar arquivos?

Depois de filtrar, ordenar e remover duplicatas dos logs, temos dois arquivos importantes:

- 📄 `myapp-backend.log` → Arquivo original
- 📄 `myapp-backend.log.unico` → Arquivo final processado

Queremos saber: **o que mudou entre esses dois arquivos?**

Comparar manualmente com `cat` é demorado e propenso a erros. 😩  
👉 O Linux resolve isso com o comando **`diff`**!

---

## 🧰 O que é o `diff`?

`diff` compara **dois arquivos linha por linha** e mostra:

- ❌ Linhas que foram **removidas**
- ✏️ Linhas que foram **alteradas**
- ➕ Linhas que foram **adicionadas**

### 🧪 Sintaxe:
```bash
diff arquivo1 arquivo2
```

Exemplo:
```bash
diff myapp-backend.log myapp-backend.log.unico
```

---

## 🔠 Como interpretar a saída do `diff`?

A saída segue o formato:

- **`[número][letra][número]`**

### Letras usadas:
- `d` → **deleted** (removido)
- `a` → **added** (adicionado)
- `c` → **changed** (modificado)

---

### 🧵 Exemplo detalhado:

```bash
2d1
< 2024-09-01 10:06:10 INFO: Retrying database connection...
```

🧠 Tradução:

- A linha 2 do arquivo original foi **deletada**
- O conteúdo está **apenas no arquivo original**

---

```bash
4,6c3
< 2024-09-01 10:09:55 INFO: Database connection established.
< 2024-09-01 11:00:00 INFO: SENSITIVE_DATA: User password is 12345.
< 2024-09-01 11:00:00 INFO: User logged in with username: admin.
---
> 2024-09-01 11:00:00 INFO: SENSITIVE_DATA: User password is REDACTED
```

🧠 Tradução:

- Linhas 4 a 6 do original foram **modificadas**
- Agora, no arquivo `.unico`, estão representadas por **uma única linha (linha 3)**  
- A senha do usuário foi **redigida (REDACTED)** no arquivo final

---

## 🤫 Quando `diff` não retorna nada?

Se você comparar um arquivo com ele mesmo:

```bash
diff myapp-backend.log myapp-backend.log
```

📭 **Não haverá saída** — isso significa que **os arquivos são idênticos**.

---

## 🗂️ Extra: comparando diretórios

O `diff` também pode comparar diretórios inteiros:

```bash
diff diretorio1/ diretorio2/
```

🔍 Isso ajuda a rastrear mudanças em projetos inteiros, muito útil para:

- ✅ Revisar código
- 🛠️ Criar patches
- 🔐 Auditar alterações no sistema

---

## ✅ Conclusão

`diff` é uma ferramenta essencial para desenvolvedores, analistas e administradores de sistemas. Ele nos permite entender:

- O que mudou
- Onde mudou
- Como mudou

---

# 📑 Comando `diff` no Linux

O comando `diff` no Linux é usado para **comparar o conteúdo de arquivos ou diretórios**, identificando as diferenças entre eles.  
🛠️ Muito útil para desenvolvedores e administradores de sistemas que precisam revisar mudanças entre versões de arquivos ou detectar alterações em diretórios.

---

## 🔍 Comparação de Arquivos Simples

**Comando:**
```bash
diff arquivo1 arquivo2
```

📌 Compara `arquivo1` com `arquivo2` e mostra **apenas as linhas diferentes** entre eles.  
📎 O formato de saída padrão usa os símbolos `<` e `>` para indicar de qual arquivo é cada linha.

---

## 📁 Comparação Recursiva de Diretórios

**Comando:**
```bash
diff -r dir1 dir2
```

📂 Compara **todos os arquivos dentro de `dir1` e `dir2`**, recursivamente.  
✅ Mostra diferenças entre **arquivos correspondentes** em ambos os diretórios.  
✨ Ideal para comparar versões completas de projetos ou backups.

---

## 🧩 Gerar Arquivo de Diferenças (Patch)

**Comando:**
```bash
diff -u arquivo1 arquivo2 > patch.diff
```

🧷 A opção `-u` (ou `--unified`) mostra diferenças com **contexto** das linhas modificadas.  
🛠️ Útil para criar **patches** que podem ser aplicados com o comando `patch`.

---

## 🪞 Comparação Lado a Lado

**Comando:**
```bash
diff -y arquivo1 arquivo2
```

👀 Exibe as diferenças **lado a lado**, facilitando a leitura.  
⚡ Ideal para comparações rápidas e visuais.

---

## 🚫 Ignorar Diferenças Específicas

**Comando:**
```bash
diff -i arquivo1 arquivo2
```

🔡 A opção `-i` **ignora diferenças de maiúsculas/minúsculas**.  
🔧 Outras opções úteis:  
- `-w`: ignora **espaços em branco**  
- `-B`: ignora **linhas em branco**  

🎯 Perfeito para focar só nas **diferenças relevantes**.

---

## 📄 Comparar Arquivos como Texto (mesmo se forem binários)

**Comando:**
```bash
diff --text arquivo1 arquivo2
```

🔍 Força o diff a tratar **qualquer arquivo como texto**.  
🧠 Útil para arquivos com conteúdo misto ou logs de sistemas.

---

## 🧪 Exemplo Prático

Imagine que você tenha dois arquivos de configuração: `config1.conf` e `config2.conf`.  
Para ver rapidamente o que mudou entre eles, rode:

```bash
diff -u config1.conf config2.conf
```

📋 Resultado: Diferenças claras de linhas adicionadas, removidas ou modificadas.  
🧩 Isso ajuda a **identificar mudanças importantes** e **diagnosticar problemas**.

---

🎓 **Resumo Final:**  
O `diff` é uma ferramenta essencial no mundo Linux para rastrear mudanças com precisão, comparar versões e manter tudo sob controle de forma eficiente! 💪🐧

---

# 📊 Módulo 4: Analisando informações de arquivos

## 📌 Tópico: Contando linhas e palavras com `wc`

Agora que temos os arquivos `.unico`, é hora de extrair informações **quantitativas** deles, como:

- 🔢 Número de linhas
- 📝 Quantidade de palavras

Essas informações nos ajudam a entender o volume de dados nos logs e monitorar possíveis aumentos, o que pode indicar **problemas na aplicação**.

---

## 🧮 Contando linhas com `wc -l`

O comando `wc` (word count) serve para contar várias coisas em um arquivo. Com a opção `-l`, ele conta **linhas**:

```bash
wc -l myapp-backend.log.unico
```

🧾 Saída:

```lua
18 myapp-backend.log.unico
```

👉 Resultado: o arquivo tem 18 linhas.

Se quisermos apenas o número (sem o nome do arquivo), usamos o redirecionamento de entrada **<**:

```bash
wc -l < myapp-backend.log.unico
```

📤 Saída limpa:

```18
```

---

## ✍️ Contando palavras com wc -w

Para contar palavras, usamos **-w**:

```bash
wc -w myapp-backend.log.unico
```

📄 Saída:

```lua
153 myapp-backend.log.unico
```

Com redirecionamento, para ver só o número:

```bash
wc -w < myapp-backend.log.unico
```

📤 Saída limpa:

```
153
```

---

## 🛠️ Incrementando o script monitoramento-logs.sh

Vamos adicionar esses comandos ao nosso script. Dentro do while, logo após gerar o .unico, adicionamos:

```bash
num_palavras=$(wc -w < "${arquivo}.unico")
num_linhas=$(wc -l < "${arquivo}.unico")
```
✨ Isso armazena os resultados em variáveis, podendo ser usados para log, monitoramento ou análise posterior.

---

## 💻 Script atualizado:

```bash
#!/bin/bash

LOG_DIR="../myapp/logs"

echo "Verificando logs no diretorio $LOG_DIR"

find $LOG_DIR -name "*.log" -print0 | while IFS= read -r -d '' arquivo; do
    grep "ERROR" "$arquivo" > "${arquivo}.filtrado"
    grep "SENSITIVE_DATA" "$arquivo" >> "${arquivo}.filtrado"

    sed -i 's/User password is .*/User password is REDACTED/g' "${arquivo}.filtrado"
    sed -i 's/User password reset request with token .*/User password reset request with token REDACTED/g' "${arquivo}.filtrado"
    sed -i 's/API key leaked: .*/API key leaked: REDACTED/g' "${arquivo}.filtrado"
    sed -i 's/User credit card last four digits: .*/User credit card last four digits: REDACTED/g' "${arquivo}.filtrado"
    sed -i 's/User session initiated with token: .*/User session initiated with token: REDACTED/g' "${arquivo}.filtrado"

    sort "${arquivo}.filtrado" -o "${arquivo}.filtrado"

    uniq "${arquivo}.filtrado" > "${arquivo}.unico"

    num_palavras=$(wc -w < "${arquivo}.unico")
    num_linhas=$(wc -l < "${arquivo}.unico")
done
```

---

### ✅ Conclusão

Com os comandos **wc -w** e **wc -l**:

* 📏 Você mede a quantidade de conteúdo nos logs.
* 📈 Pode monitorar aumentos suspeitos.
* 🧠 Ganha mais inteligência operacional nos seus scripts.

---

📚 Dica final: Use esses dados para gerar relatórios e observar o comportamento dos logs ao longo do tempo. Isso é essencial para detecção precoce de problemas!

---

# 📊 Resumo: Comando `wc` no Linux (Word Count)

O comando `wc` (word count) é uma ferramenta poderosa no terminal Linux 🐧 que serve para contar **linhas**, **palavras**, **caracteres**, **bytes**, e até o **tamanho da linha mais longa** dentro de arquivos ou da entrada padrão (stdin).

---

## 🔧 Principais Opções do `wc`

### 📄 Contar Linhas `-l`

Mostra **apenas o número de linhas** no arquivo.

```bash
wc -l arquivo.txt
```

> 🧠 **Exemplo:** Se o arquivo tem 42 linhas, o retorno será:
> ```
> 42 arquivo.txt
> ```

---

### 📝 Contar Palavras `-w`

Mostra **apenas o número de palavras** no arquivo.

```bash
wc -w arquivo.txt
```

> 📚 Bom para medir a densidade de conteúdo textual!

---

### 🔤 Contar Caracteres `-m`

Mostra o **número de caracteres**, incluindo **espaços** e **quebras de linha**.

```bash
wc -m arquivo.txt
```

> 🧐 Útil para análise de tamanho real do conteúdo textual!

---

### 💾 Contar Bytes `-c`

Mostra o **tamanho do arquivo em bytes**.

```bash
wc -c arquivo.txt
```

> ⚠️ Lembra: 1 caractere ≠ 1 byte se estiver usando codificação UTF-8 com acentos!

---

### 📏 Tamanho da Linha Mais Longa `-L`

Mostra o **comprimento da linha mais longa** do arquivo (em caracteres).

```bash
wc -L arquivo.txt
```

> 🧵 Ajuda a identificar linhas que podem quebrar layouts ou que precisam de truncamento.

---

## 🔄 Combinação de Opções

Você pode combinar várias opções para mostrar múltiplos valores de uma só vez:

```bash
wc -l -w -m arquivo.txt
```

Ou, de forma mais simples, apenas:

```bash
wc arquivo.txt
```

> Isso exibirá:  
> `linhas palavras bytes nome-do-arquivo`

---

## 🛠️ Quando Usar o `wc`

- Em **scripts de monitoramento**, para medir crescimento de logs 📈  
- Em **relatórios automáticos**, para analisar arquivos de texto 🧾  
- Para **verificar conteúdo textual**, antes de fazer parsing ou análise de dados 🔍

---

## ✅ Dica

Se quiser **apenas o número**, sem o nome do arquivo:

```bash
wc -l < arquivo.txt
```

> Usa o **redirecionamento de entrada** `<` para evitar que o nome do arquivo apareça na saída.

---

## 🧠 Resumo Final

| Opção | O que faz                       |
|-------|---------------------------------|
| `-l`  | Conta linhas                    |
| `-w`  | Conta palavras                  |
| `-m`  | Conta caracteres                |
| `-c`  | Conta bytes                     |
| `-L`  | Mostra tamanho da linha mais longa |

---