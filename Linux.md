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
<<<<<<< HEAD

=======
>>>>>>> b77c6840acf5b4c7b70e55b96d6d80d08327b2fd
